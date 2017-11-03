# WebpayInterswitch


[![Code Climate](https://codeclimate.com/github/vinsol/webpay_interswitch.png)](https://codeclimate.com/github/vinsol/webpay_interswitch)


A simple gem to integrate your Rails app with [Webpay Interswitch](http://www.interswitchng.com/), a nigerian payment gateway. It provides methods to add webpay as a payment option in your Rails application out of the box. It also provides a simple API that can be used to fetch transactions (JSON Only).

##Getting Started


Include the gem in your Rails app by adding it to Gemfile:

 `gem 'webpay_interswitch'` and run 

 `bundle install`

And then:

 `rails generate webpay_interswitch:install`
 
The generator will install an initializer which adds the configuration options for setting up the gateway from the webpay_interswitch.yml file.

It also adds a `webpay_interswitch.yml.example` file which should be considered as a reference while setting up the gateway. Create `webpay_interswitch.yml` file in `config` directory and specify your mac_key, product_id, pay_item_id etc.


##Helper


It comes with a helper method that seamlessly adds a webpay form to your Rails application.

```ruby
    <%= form_for_webpay(txn_ref, amount, cust_name, cust_id) %>
```

This helper adds a button (Along with all the hidden elements required to make the transaction). You can also configure the text that appears on the button and add any valid html attributes to it like:

```ruby
    <%= form_for_webpay(txn_ref, amount, cust_name, cust_id, { submit_button_text: 'Make Payment', class: 'btn-class' }) %>
```

txn_ref must be a globally unique number generated by your application. Each request that hits the gateway must have a unique value of txn_ref. To avoid polluting the global space, consider prefixing it with a specific string. E.g. my_app_1, my_app_2 and so on for each transaction.

The gateway accepts the amount only in Kobo. So, you must always pass the amount after converting the amount in Naira to Kobo.

Once you click the submit button, you'll be redirected to the Webpay to carry out the transaction. Upon completion(success or failure), Webpay will send a POST request to the redirect URL along with a few parameters. Remember that the helper generates an HTML form for itself. So, make sure it is not placed inside another HTML form.


##Querying Transactions


The gem provides an API that should be used to query the transaction.

Create an object of WebpayInterswitch::Transaction using:

```ruby
    transaction = WebpayInterswitch::TransactionQuery.new(params, amount)
```

It accepts the params hash that is sent by the gateway to the redirect URL. The amount of the transaction is a required parameter and must be set to ensure the payment amount was not tampered.

You can then call methods on this object like:

```ruby
    transaction.success?
    
    transaction.fetch_response

    transaction.full_error_message
```

##Configurations Parameters


Below parameters are needed to setup the gateway.

* product_id: It is provided when you sign up for interswitch webpay.
    
* pay_item_id: It is provided when you sign up for interswitch webpay.

* site_redirect_url: This is the URL where the interswitch redirects back with a POST request when the transaction is complete (Successful Or Failed). This is the controller action, where you want to process the transaction response using `WebpayInterswitch::TransactionQuery` class.

* mac_key: It is provided when you sign up for interswitch webpay.

* currency (Optional, defaults to Naira): Currently, we only support Naira. Currency Code for Naira is `566`. The payment amount is always specified in Kobo. While interfacing with the gem, you must always pass in the amount in Kobo. `Conversion: 1 Naira = 100 Kobo`

* test (Optional, defaults to true): This is used to specify Interswitch URL. If true, all the requests are made at the test gateway. For production environments (Making actual payments), you must set this explicitly to false. By default, it would always be set to true.

## Find a Demo Application

##### [Interswitch Demo](https://github.com/vinsol/webpay_interswitch_gem_demo_app)

##Credits
[![vinsol.com: Ruby on Rails, iOS and Android developers](http://vinsol.com/vin_logo.png "Ruby on Rails, iOS and Android developers")](http://vinsol.com)

Copyright (c) 2014 [vinsol.com](http://vinsol.com "Ruby on Rails, iOS and Android developers"), released under the New MIT License

