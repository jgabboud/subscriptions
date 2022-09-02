A Laravel package that will allow you to have dynamic subscriptions with free trials and discounts with their respective middlewares, enjoy!

The library consists of 5 main tables: <br>
plans <br>
plan_has_items <br>
plan_items <br>
plan_subscriptions <br>
plan_subscription_usages

Installation
=========

install the package using composer
```
composer require jgabboud/subscriptions
```

publish the vendor
 ```
 php artisan vendor:publish --provider="Jgabboud\Subscriptions\SubscriptionServiceProvider"
 ```

Usage
=====
First let's add the subscription out Model, supposedly the User Model 

<code>
use Jgabboud\Subscriptions\Traits\HasSubscriptions;

class MyModel extends Model
{
    use HasSubscriptions;
    
    //...
}
</code>

Now let's make use of our subscriptions!

In order to create a plan we will do the following:
```
Plan::create([
    'name' => 'Gold Plan',
    'description' => 'This plan is the one for you',
    'price' => 25.50,
    'currency' => 'USD',
    'trial_duration' => 20,
    'trial_duration_type' => 'days',
    'package_duration' => 1,
    'package_duration_type' => 'month',
    'subscriptions_limit' => 1500    
]);
```
notice the duration type, whether trial_duration_type or package_duration_type, can be days, month, or year

Structure
=========
The plan table will hold the the details of each plan regarding the name description, prices, trial periods and more.
Now a plan might have many features or items inside it e.g  maximum number of products you can create using this subscription.
Hence we will use the plan_items table to initialize these items.
Since a plan can have more than one item and item can belong to more than one plan we connect these two using the plan_has_items.

Now for a user to subscribe to plan we will mock the plans details to the plan_subscriptions table with the corresponding subscriber
info and plan info, and we will mock the items to the plan_subscription_usages.
The main reason for mocking nearly most of the data, beside keeping track of the plans that a user has subscribed to, is to keep 
track of what the plan was really like when the user subscribed at that day, since the same plan might be updated later on 
and the newly updated plan shouldn't take effect on the what the users has really subscribed for back then, but should take effect
as of new subscriptions.

Free Trials
==========
you can create a package, name it "30 days Free Trial", give it 30 days trial period and the invoice period will be 0.
In this you have a created a free trial for the users.
on the other hand you can as well give an ongoing subscription a free trial by adding the free trial period to it along with the
invoice period.

what really happens in the plan_subscriptions table is that the record for each subscription will be inserted only once with the 
corresponding subscriber. and now every time the subscription renews we only update the starts_at and ends_at fields.
and we can either reset the plan subscription usages to what it became in the new plan, or role over the old usages to the new
depending on the business.
what if we want to keep track of the history of subscription? you can later create an invoice table that will hold the details of
the subscription. 

One can always cancel a subscription

Addons and Bundles
==================
you can later add add another table that will have more than one item in it and a user can buy that bundle and, similarly, 
append the items to plan_subscription_usages with an addon flag 

Pay as You Go
=============
by default the subscriptions library has no limitations on how many times the items should be used and thus you can specify 
a functionality that will calculate how many time the item has been used a set a billing price for it.


