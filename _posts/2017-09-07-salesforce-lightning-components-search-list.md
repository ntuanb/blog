---
layout: post
title: Salesforce Lightning Component Search List
---

This step by step guide is purely for personal learning and is based off another tutorial. The steps will be mostly the same, some copy pasta here and there, along with syntax and variable changes.

The original tutorial can be found here [Lightning Components Tutorial](http://developerforce.github.io/lightning-components-tutorial/setup-environment.html).

# Part 1 - Creating the ConsignmentListController Class

Create a controller in Apex that will be primarly used to retrieve the data by using SOQL(Salesforce Object Query Language).

1. 
- **File** > **New** > **Apex Class**, specify `ConsignmentListController` as the class name and click OK.
or
- In PHPStorm (with JedIDE), create a class in `classes` called `ConsignmentListController`.

2. Implement the class as follows:

{% highlight java %}

public with sharing class ConsignmentListController {

    @AuraEnabled
    public static List<Consignment__c> findAll() {
        return [SELECT Id, Name__c, Phone__c FROM Consignment__c LIMIT 50];
    }

    @AuraEnabled
    public static List<Contact> findByName(String searchKey) {
        String name = '%' + searchKey + '%';

        return [
            SELECT Id, Name__c, Phone__c
            FROM Consignment__c
            WHERE Name__c LIKE :name
            LIMIT 50
        ];
    }

    @AuraEnabled
    public static Contact findById(String consignmentId) {
        return [
            SELECT Id, Name__c, Phone__c
            FROM Contact
            WHERE Consignment_Id = :consignmentId
        ];
    }

}

{% endhighlight %}

### Notes
- The @AuraEnabled method annotation makes a method available to Lightning applications

# Part 2 - Creating the QuickConsignment Lightning Component

## Step 1 - Create the component

1. **File** > **New** > **Lightning Component**.

2. Implement the component as follows:

{% highlight html %}

<aura:component implements="force:appHostable">

    <p>ContactList goes here</p>

</aura:component>

{% endhighlight %}

## Step 2 - Create a tab

1. **Create** > **Tabs**

In the Lightning Component Tabs section, click New

# Part 3 - Creating the ConsignmentList Component

## Step 1 - Create the component

1. **File** > **New** > **Lightning Component**. Specify `ConsignmentList` as the bundle name and click OK.

2. Implement the component as follows:

{% highlight html %}

<aura:component controller="ConsignmentListController">

    <aura:attribute name="consignments" type="Consignment__c[]"/>
    <aura:handler name="init" value="{!this}" action="{!c.doInit}" />

    <table class="slds-table slds-table_bordered slds-table_cell-buffer">
        <tbody>
            <aura:iteration items="{!v.consignments}" var="consignment">
                <tr>
                    <td data-label="Id">
                        <div class="slds-truncate">
                            <a href="{! '#/sObject/' + consignment.Id + '/view'}">
                                {!consignment.Id}
                            </a>
                        </div>
                    </td>
                    <td data-label="Order Reference">
                        <div class="slds-truncate">
                            {!consignment.Name__c}
                        </div>
                    </td>
                    <td data-label="Order Reference">
                        <div class="slds-truncate">
                            {!consignment.Phone__c}
                        </div>
                    </td>
                </tr>
            </aura:iteration>
        </tbody>
    </table>

</aura:component>

{% endhighlight %}

### Notes
- The controller assigned to the component (first line of code) refers to the server-side controller created in the classes file.

## Step 2 - Implement the Controller

{% highlight js %}

({
    doInit : function(component, event) {
        var action = component.get("c.findAll");

        action.setCallback(this, function(a) {
            component.set("v.contacts", a.getReturnValue());
        });

        $A.enqueueAction(action);
    }
})

{% endhighlight %}

## Step 3 - Add the component to a page

{% highlight html %}

<aura:component implements="force:appHostable">

    <c:ConsignmentList/>

</aura:component>

{% endhighlight %}

### Notes
- c. is the default namespace for Lightning components

# Part 4

Now that we decided to build SearchBar and ContactList as two separate components, we need a way for ContactList to know when the search key changes so that it can retrieve and display the matching contacts. Lightning Events enable that kind of communication between components. In this step, you create a Lightning Event used by the SearchBar component to notify other components when the search key changes.

## Step 1 - Create an event

1. **File** > **New** > **Lightning Event**

2. Implement the component as follows:

{% highlight html %}

<aura:event type="APPLICATION">
    <aura:attribute name="searchKey" type="String"/>
</aura:event>

{% endhighlight %}

## Step 2 - Create a SearchBar component

1. File > New > Lightning Component

2. Implement the component as follows:

{% highlight html %}

<aura:component>

    <input
        type="text"
        class="slds-input"
        placeholder="search"
        onkeyup="{!c.searchKeyChange}"
        />

</aura:component>

{% endhighlight %}

## Step 3 - Implement the Controller

{% highlight js %}

({
    searchKeyChange: function(component, event, helper) {
        var myEvent = $A.get("e.c:SearchKeyChange");
        myEvent.setParams({"searchKey": event.target.value});
        myEvent.fire();
    }
})

{% endhighlight %}

### Notes
- Gets an instance of the **SearchKeyChange** event

## Step 4 -  Listen for the SearchKeyChange Event in ConsignmentList

1. Go back to the ConsignmentList component.
2. Add the following to the existing code:

{% highlight html %}

<aura:handler event="c:SearchKeyChange" action="{!c.searchKeyChange}"/>

{% endhighlight %}

3. In the controller, add the following below `onInit`:

{% highlight js %}

searchKeyChange: function(component, event) {
    var searchKey = event.getParam("searchKey");
    var action = component.get("c.findByName");

    action.setParams({
      "searchKey": searchKey
    });

    action.setCallback(this, function(a) {
        component.set("v.contacts", a.getReturnValue());
    });

    $A.enqueueAction(action);
}

{% endhighlight %}

# Step 5 - Finish Off

1. Add `<c:SearchBar/>` and `<c:ContactList/>` to any page to retreive and display the component.

{% highlight html %}

<aura:component implements="force:appHostable">

    <c:SearchBar/>
    <c:ContactList/>

</aura:component>

{% endhighlight %}