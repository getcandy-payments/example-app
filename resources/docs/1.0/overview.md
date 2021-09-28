# GetCandy Payments Documentation

---

- [Overview](#overview)


GetCandy Payments provides Laravel applications which an easy way to integrate payment gateways.

<a name="overview"></a>
## Overview

```php
use GetCandyPayments\Exceptions\RefundException;
use GetCandyPayments\Payment;
use GetCandyPayments\Requests\AuthDetails;
use GetCandyPayments\Responses\AuthResponse;
use GetCandyPayments\SavedCard; // token?
use GetCandyPayments\Stripe\Gateway as Stripe;

$stripe = new Stripe($apiKey);

$payment = new Payment;
$payment->setGateway($stripe);
$payment->setAmount(29999);

$stripeCustomer = $stripe->createCustomer();

$authDetails = new AuthDetails([
    // Standard data...
    'billing_first_name' => 'Bob',
    'billing_last_name' => 'Smith',
    // etc

    // Gateway specific data...
    'extra' => [
        'stripe_customer' => $stripeCustomer,
    ],
]);

$payment->initAuth($authDetails);

// Either Laravel HTML response or redirect
return $payment->getUI();  

// Process return from gateway
try {
    $authResponse = (new AuthResponse)->set($request);
    $payment->completeAuth($authResponse);
} catch (AuthException $e) {
    // Whoops!
}

// Optional payment release method
try {
    $payment->release();
} catch (ReleaseException $e) {
    // Whoops!
}

try {
    $amount = 10099;
    $description = 'Refund for damaged shoes.';

    // Hmmm, how would we store associated transactions?
    $payment->refund($amount, $description);
} catch (RefundException $e) {
    // Whoops!
}

```

