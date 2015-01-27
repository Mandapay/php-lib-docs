Mandapay PHP library documentation
======================================================

.. contents:: Table of contents

Overview
--------

The Mandapay PHP library enables developers to install the Mandapay online payment system to any website or e-commerce store, or to build a payment plugin for an existing CMS (Magento, Wordpress, WooCommerce, Prestashop, etc.).

**Payment process**

1. Your website generates a dynamic payment URL by passing the payment parameters (e.g., amount, customer ID, return URL) into the PHP library
2. Redirect your customer to that URL, which points to a secure online payment page 
3. Once the payment goes through, Mandapay redirects your customer towards the return URL you specify (in a future version)

Installation
------------

**Download the library:**

Download `mandapay_php` and copy the folder in your environment.

**Configuration**

Mandapay generates a set of unique parameters and keys for each user account, which needs to be saved on your server by following these configuration instructions.

Create a file called ``setup.php`` and insert the following lines to set-up the Mandapay library. Replace ``PATH_TO_MANDAPAY`` with the correct path for your environment.

.. code-block:: php
   :linenos:

   <?php
   require_once("PATH_TO_MANDAPAY/mandapay_php/lib/Mandapay.php");
   $isTest = true;
   $parameters = Mandapay::loadParameters();
   $parameters->saveInFile("PATH_TO_MANDAPAY/parameters.json");


You need to execute this code once, for example by opening your web browser and accessing ``http://yoursite.com/setup.php`` (the URL where you saved the above code). Verify that everythings went well by looking at the file ``parameters.json`` and identifying the parameter ``paymentBaseUrl``.

If you encounter the error ``Warning: file_put_contents(./parameters.json): failed to open stream: Permission denied in PATH_TO_MANDAPAY/lib/mandapay/Parameters.php on line 53``, it is likely that you have a permission issue. Open a terminal and try ``chmod +777 .`` (note the trailing dot, it is important).

TEST (Sandbox) Mode
------------
Mandapay has created a sandbox environment for testing transactions without making real payments. 

When you sign up to Mandapay, you can only use the TEST mode. Once your account is verified, you will be able to configure your site with the LIVE mode to make real transactions.



.. _create_a_payment:

Creating a payment
------------------

Create a file called ``payment.php`` that will generate a payment URL and direct the customer to the online payment page.

.. code-block:: php
   :linenos:

   <?php
   require_once("PATH_TO_MANDAPAY/mandapay_php/lib/Mandapay.php");
   Mandapay::setConfigFromFile("PATH_TO_MANDAPAY/parameters.json");

   $paymentUrl = PaymentUrl::generateUrl(array(
                                         'amount' => 999,
					 'merchant_id' => 999,
                                         'email' => 'john.doe@example.fr', /* Your customer mail address */
                                         'firstName' => 'John',
                                         'lastName' => 'Doe'
                                         ));
   header("Location: $paymentUrl");
   exit();

The fields ``amount``, ``merchant_id`` and ``john.doe@example.fr`` are required. 

The complete list of accepted fields is available in the reference_ section.


Reference
---------

**Payment fields**

Fields marked with an * are required.

============== ======= =
Name           Type    Description
============== ======= =
amount *       Integer Transaction amount, in cents (such as ``4207`` for 42,07€). We advise you to verify that the amount is between the minimum and maximum amounts allowed for your account.
-------------- ------- -
merchant_id *  String  The personnal id that was given by Mandapay at the subscription step.
-------------- ------- -
ipnUrl         String  URL pointing to the ``ipn.php`` page, to which Mandapay will send payment and refund notifications. This URL must be accessible from anywhere on the Internet (usually not the case in ``localhost`` environments).
-------------- ------- -
cancelUrl      String  URL pointing to your payment cancelation page, to which Mandapay will redirect your customer if he cancels the payment.
-------------- ------- -
returnUrl      String  URL pointing to your payment confirmation page, to which Mandapay will redirect your customer after the payment.
-------------- ------- -
email *        String  The customer's email address.
-------------- ------- -
firstName      String  The customer's first name.
-------------- ------- -
lastName       String  The customer's last name.
-------------- ------- -
customer       String  The customer ID in your database.
-------------- ------- -
order          String  The order ID in your database.
-------------- ------- -
customData     String  Additional data that you want to specify (e.g. the order description).
============== ======= =


**IPN fields (future use)**

============== ======= =
Name           Type    Description
============== ======= =
state          String  The new state of the transaction: ``paid`` or ``refunded``.
-------------- ------- -
idTransaction  Integer The Mandapay transaction ID. We recommend you save it and associate it with this order in your database.
-------------- ------- -
amount         Integer Transaction amount, in cents (such as ``4207`` for 42,07€).
-------------- ------- -
email          String  The customer's email address, either provided when creating the payment URL or entered manually on the payment page by the customer.
-------------- ------- -
firstName      String  The customer's first name, either provided when creating the payment URL or entered manually on the payment page by the customer.
-------------- ------- -
lastName       String  The customer's last name, either provided when creating the payment URL or entered manually on the payment page by the customer.
-------------- ------- -
customer       String  Customer ID provided when creating the payment URL.
-------------- ------- -
order          String  Order ID provided when creating the payment URL.
-------------- ------- -
customData     String  Custom data provided when creating the payment URL.
-------------- ------- -
origin         String  Information about your website version (e.g., 'My Website 1.2 mandapay_php0.9 PHP 5.3'), provided when creating the payment URL, with additional data sent by the library itself.
-------------- ------- -
isTest         Boolean If value is ``true``, the payment was done in Sandbox (TEST) mode.
============== ======= =
