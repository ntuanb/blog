---
layout: post
title: Drupal 7, Docker, PHP Worker, Resque, Redis, Queue and Email Part 1
---

Our task was to create a module that could import users into the database.

Keep in mind, all the challenges that we had for the required features were tackled as they came up. We were new to Drupal and many of the tools that we were going to use, so we couldn't really foresee any problems that came up.

This guide also assumes that basic knowledge of how to create a plugin in Drupal 7 and the usage of a few other tools mentioned above is already known.

# Goals

The goals that was laid out at the start was to:
- Use an existing import module
- Import the users without using the internal cron system
- Utilize a seperate storage system for reliability and speed
- Process the queueing independantly
- Log any errors

### Existing Import Module

Was required because we were already using an import tool, which did the job but not fast enough. 

### Cron System

It ran through the cron system which imported around 100 users per minute and ran on each web page load.

### Seperate Storage

We wanted to use a seperate storage because it would function faster if we stored the job in memory. A relational database was too slow.

### Process Indepedantly

The cron system worked on each request. We wanted to import a large number of users so we needed speed.

### Log

If a user could not be imported, we wanted it to reliably log where it stopped and why. Losing data was not an option because the data being inserted was too large to sit down and check by eye.

# Email

First off, we needed a queueing system to be adding into the project that would allow any email sent to be redirected to a queue. 

The hard part about this was integrating it into Drupal in a way that would allow the traditional way of sending emails as an option because we wanted to slowly integrate it into the system. This meant that we needed an on/off where the user could change how emails could be processed.

By approaching this problem first, we would be able to integrate another feature into the queueing system in the future.

I picked [Resque](https://github.com/resque/resque) to be used after a bit of research because we already had a Redis server running for sessions. By using Redis to store the queue jobs, it would be fast and that data we stored there was independant of the database.

### Setup Redis

Any redis server would work as long as we had the correct url, username and password.

For this project, we used Docker containers `edis:3.2-alpine` and `eldadfux/resque-web`.

### Create the Generic Module

Download and add the plugin into the module folder under `lib`.

Make sure the library is loaded into Drupal's caching system:

{% highlight php %}

function my_resque_libraries_info() {

    $libraries['resque'] = array(
        'name' => 'PHP Resque',
        'vendor url' => '',
        'download url' => '',
        'path' => 'vendor',
        'version' => 1,
        'files' => array(
            'php' => array('autoload.php'),
        ),
    );

    return $libraries;
    
}

{% endhighlight %}

Create 2 function that would give the user the ability to save the mailsystem where the mail will actually be processed.

The 3 step process for how it would look like when a mail is queued will look like this `mailsystem > queue > custom_mailsystem > mail_processor`.

Mail processor will be `Mailgun` for example.

{% highlight php %}

function my_resque_mailsystem_form_alter(&$form, &$form_state, $form_id) {

    ...

    $html_select = '<select name=' . $key . '-' . $module . '-class' . ' class="form-select">';
        foreach (mailsystem_get_classes() as $class) {

            // Prevent infinite loop to self.
            if ($class == 'myResqueMailSystem') continue;

            $html_select .= '<option value="' . $class . '"';
                $html_select .= (isset($settings[$module]['class']) && $settings[$module]['class'] == $class) ? 'selected' : '';
            $html_select .= '>' . $class . '</option>';

        }
    $html_select .= '</select>';

    ...

}

function my_resque_mailsystem_form_submit($form, &$form_state) {

    ...

    if (isset($form_state['values'])) {

        $inputs = $form_state['input'];

        foreach (mailsystem_get() as $module => $mailsystem) {
            if (isset($inputs[$key . '-' . $module . '-class'])) {
                $settings[$module]['class'] = $inputs[$key . '-' . $module . '-class'];
            } else {
                $settings[$module]['class'] = 'DefaultMailSystem';
            }

            // Save some other mailsystems that may be related.
        }
    }

    ...

}

{% endhighlight %}

Create a generic function where all sub modules will call to add a job to the queue.

{% highlight php %}

function my_resque_queue($module, $class, $variables = array()) {

    // Check if the library is laoded.
    $library = libraries_detect('resque');
  
    // Lets load it if its not loaded.
    if (empty($library['loaded'])) {
        cache_clear_all('resque', 'cache_libraries');
        drupal_static_reset('libraries_load');
        libraries_load('resque');
    }
    
    // Get any variables
    $key = 'my';
    $redis_host         = variable_get($key . '_host', 'redis'); // redis because we're using docker named network containers
    $redis_port         = variable_get($key . '_port', '6379');
    $redis_password     = variable_get($key . '_password', '');
    $redis_table        = variable_get($key . '_table', 1);

    // Connect
    if (strlen($redis_password) == null) {
        Resque::setBackend($redis_host . ':' . $redis_port . '/' . $redis_table);
    } else {
        Resque::setBackend('redis://redis:' . $redis_password . '@' . $redis_host . ':' . $redis_port . '/' . $redis_table);
    }

    $token = Resque::enqueue($module, $class, $variables, false);

    return array('token' => $token);

}

{% endhighlight %}

Whats happening here is, the function will load the Resque library that was included above, get all the variables that we have saved. It will the enqueue the job and return the token id of the job.

If this function is called succesfully, a job should now appear in Redis.

### Create a Mail Mailsytem

Create a new submodule to with the functionality to create a new mailsystem called `MyResqueMailSystem`, which is a class that implements `MailSystemInterface` in a file called `my_resque_mailsystem.mail.inc`.

{% highlight php %}

class MyResqueMailSystem implements MailSystemInterface {

    private function template(&$message) {

        ...

    }

    public function format(array $message) {
        
        ...

    }
}

{% endhighlight %}

This module relies on the `mailsystem` module which allows the user to define custom mailing be used, in this case, we wanted all mail to be sent to the queue instead of directly sending to `Mailgun`. The queue would then process the job when the time comes and send the mail using Mailgun.

The trickiest part about this was getting the redirect to work properly.

Inside the mailsytem class, the format function had the ability to redirect to the right custom mailsystem, which was saved using the form `my_resque_mailsystem_form_alter() {}` and `my_resque_mailsystem_form_submit() {}`

{% highlight php %}

public function format(array $message) {

    ...

    $settings       = variable_get($key);
    $instances      = &drupal_static(__FUNCTION__, array());

    $class = null;
    if (isset($settings[$message['module']])) {
        $class = $settings[$message['module']]['class'];
    } else {
        $class = $settings['default-system']['class'];
    }

    if (empty($instances[$class])) {
        $interfaces = class_implements($class);

        if (isset($interfaces['MailSystemInterface'])) {
            $instances[$class] = new $class();
        } else {
            throw new Exception(strtr('Class %class does not implement interface %interface', [
                '%class' => $class, '%interface' => 'MailSystemInterface'
            ]));
        }
    }
    
    ...

}

{% endhighlight %}

### Create The Worker

...// stuff