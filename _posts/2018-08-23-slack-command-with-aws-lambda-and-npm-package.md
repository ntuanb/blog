---
layout: post
title: Slack Command with AWS Lambda and NPM Package
---

Challenge: Implement a Slack app that flips tables and deploy it to AWS Lambda and NPM.

The following links are apart of this:
- [https://github.com/ntuanb/slack-fliptable](https://github.com/ntuanb/slack-fliptable)
- [https://www.npmjs.com/package/slack-fliptable](https://www.npmjs.com/package/slack-fliptable)
- [https://travis-ci.org/ntuanb/slack-fliptable](https://travis-ci.org/ntuanb/slack-fliptable)

The magic in this is mostly done through the script file ran in Travis when the tests pass 100%:

{% highlight bash %}

#/usr/bin/env sh

echo "Pushing to Lambda";
dpl --provider="lambda" \
    --access_key_id="${AWS_ACCESS_KEY}" \
    --secret_access_key="${AWS_SECRET_KEY}" \
    --function_name="slack-fliptable" \
    --role="${AWS_LAMBDA_ROLE}" \
    --runtime="nodejs8.10" \
    --region="ap-southeast-2" \
    --handler_name="handler";

echo "Pushing to NPM"
echo "//registry.npmjs.org/:_password=${NPM_PASSWORD}" > ~/.npmrc
echo "//registry.npmjs.org/:_authToken=${NPM_TOKEN}" >> ~/.npmrc
echo "//registry.npmjs.org/:username=${NPM_USERNAME}" >> ~/.npmrc
echo "//registry.npmjs.org/:email=${NPM_EMAIL}" >> ~/.npmrc
npm publish ./ || true

{% endhighlight %}

An app is added in slack where a slack `command` is added with response url pointing to the `API Gateway` provided by Lambda.

The resulting output is:

![Slack table flipping]({{ "/images/2018/slack-flip-table.png" | absolute_url }})
