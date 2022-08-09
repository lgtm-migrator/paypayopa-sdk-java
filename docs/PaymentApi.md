# PaymentApi

Method | HTTP request | Description
------------- | ------------- | ------------- 
[**createAccountLinkQRCode**](PaymentApi.md#createAccountLinkQRCode) | **POST** /v1/qr/sessions | Create an ACCOUNT LINK QR and display it to the user
[**cancelPayment**](PaymentApi.md#cancelPayment) | **DELETE** /v2/payments/{merchantPaymentId} | Cancel a payment
[**createPayment**](PaymentApi.md#createPayment) | **POST** /v2/payments | Create a payment
[**createPaymentAuthorization**](PaymentApi.md#createPaymentAuthorization) | **POST** /v2/payments/preauthorize | Create a payment authorization to block the money
[**createContinuousPayment**](docs/PaymentApi.md#createContinuousPayment) | **POST** /v1/subscription/payments | Create a continuous payment and start the money transfer
[**revertAuth**](PaymentApi.md#revertAuth) | **POST** /v2/payments/preauthorize/revert | Revert a payment authorization
[**capturePaymentAuth**](PaymentApi.md#capturePaymentAuth) | **POST** /v2/payments/capture | Capture a payment authorization
[**createQRCode**](PaymentApi.md#createQRCode) | **POST** /v2/codes | Create a Code
[**deleteQRCode**](PaymentApi.md#deleteQRCode) | **DELETE** /v2/codes/{codeId} | Delete a Code
[**getPaymentDetails**](PaymentApi.md#getPaymentDetails) | **GET** /v2/payments/{merchantPaymentId} | Get payment details
[**getPaymentMethods**](PaymentApi.md#getPaymentMethods) | **GET** /v4/paymentMethods | Get payment methods
[**getCodesPaymentDetails**](PaymentApi.md#getCodesPaymentDetails) | **GET** /v2/codes/payments/{merchantPaymentId} | Get payment details for QR code
[**getRefundDetails**](PaymentApi.md#getRefundDetails) | **GET** /v2/refunds/{merchantRefundId} | Get refund details
[**refundPayment**](PaymentApi.md#refundPayment) | **POST** /v2/refunds | Refund a payment
[**revertAuth**](PaymentApi.md#revertAuth) | **POST** /v2/payments/preauthorize/revert | Revert a payment authorization


<a name="cancelPayment"></a>
# **cancelPayment**
> NotDataResponse cancelPayment(merchantPaymentId)

Cancel a payment

This API is used in case, while creating a payment, the client can not determine the status of the payment. For example, client get timeout or the response cannot contain the information to indicate the exact payment status.

By calling this API, if accepted, the OPA will guarantee the money eventually goes back to user's account.


Note: The Cancel API can be used until 00:14:59 AM the day after the Payment has happened.
For 00:15 AM or later, please call the refund API to refund the payment. 

### Example
```java
//Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;

PaymentApi apiInstance = new PaymentApi(apiClient);

String merchantPaymentId = "MERCHANT_PAYMENT_ID"; 

try {
    NotDataResponse result = apiInstance.cancelPayment(merchantPaymentId);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#cancelPayment");
    System.out.println(e.getResponseBody());
}
```
```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/dynamicqrcode#operation/cancelPayment 
```

<a name="capturePaymentAuth"></a>
# **capturePaymentAuth**
> PaymentDetails capturePaymentAuth(body)

Capture a payment authorization

This API is used to capture the payment authorization for a payment  **Timeout: 30s** 

### Example
```java
//Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;

PaymentApi apiInstance = new PaymentApi(apiClient);

CaptureObject captureObject = new CaptureObject();
      captureObject.setMerchantCaptureId("MERCHANT_CAPTURE_ID");//UNIQUE ID
      captureObject.setMerchantPaymentId("MERCHANT_PAYMENT_ID");
      captureObject.setAmount(new MoneyAmount().amount(1).currency(MoneyAmount.CurrencyEnum.JPY));
      captureObject.setOrderDescription("ORDER_DESCRIPTION");
      captureObject.setRequestedAt(Instant.now().getEpochSecond());

try {
    PaymentDetails result = apiInstance.capturePaymentAuth(captureObject);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#capturePaymentAuth");
    System.out.println(e.getResponseBody());
}
```

```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/dynamicqrcode#operation/capturePaymentAuth
```

<a name="createPayment"></a>
# **createPayment**
> PaymentDetails createPayment(body, agreeSimilarTransaction)

Create a payment

Create a direct debit payment and start the money transfer.  **Timeout: 30s** 

NOTE: If you would like to specify payment method, see also [**getPaymentMethods**](PaymentApi.md#getPaymentMethods).

### Example
```java
//Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;


PaymentApi apiInstance = new PaymentApi(apiClient);

Payment payment = new Payment();
      payment.setAmount(new MoneyAmount().amount(1).currency(MoneyAmount.CurrencyEnum.JPY));
      payment.setMerchantPaymentId("MERCHANT_PAYMENT_ID");
      payment.setUserAuthorizationId("USER_AUTHORIZATION_ID");
      payment.setRequestedAt(Instant.now().getEpochSecond());
      payment.setStoreId("STORE_ID");
      payment.setTerminalId("TERMINAL_ID");
      payment.setOrderReceiptNumber("ORDER_RECEIPT_NUMBER");
      payment.setOrderDescription("ORDER_DESCRIPTION");
      MerchantOrderItem merchantOrderItem =
              new MerchantOrderItem()
                      .category("pasteries").name("Moon Cake")
                      .productId("PRODUCT_ID").quantity(1)
                      .unitPrice(new MoneyAmount().amount(10).currency(MoneyAmount.CurrencyEnum.JPY));
      List<MerchantOrderItem> merchantOrderItems = new ArrayList<>();
      merchantOrderItems.add(merchantOrderItem);
      payment.setOrderItems(merchantOrderItems);

String agreeSimilarTransaction = "false"; // (Optional) If the parameter is set to true, the payment duplication check will be bypassed. 

try {
    PaymentDetails result = apiInstance.createPayment(payment, agreeSimilarTransaction);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#createPayment");
    System.out.println(e.getResponseBody());
}
```

```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/direct_debit#operation/createPayment
```

<a name="createPaymentAuthorization"></a>
# **createPaymentAuthorization**
> PaymentDetails createPaymentAuthorization(body, agreeSimilarTransaction)

Create a payment authorization

Create a payment authorization to block the money.  **Timeout: 30s** 

NOTE: If you would like to specify payment method, see also [**getPaymentMethods**](PaymentApi.md#getPaymentMethods).

### Example
```java
//Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;


PaymentApi apiInstance = new PaymentApi(apiClient);

Payment payment = new Payment();
      payment.setAmount(new MoneyAmount().amount(1).currency(MoneyAmount.CurrencyEnum.JPY));
      payment.setMerchantPaymentId("MERCHANT_PAYMENT_ID");
      payment.setUserAuthorizationId("USER_AUTHORIZATION_ID");
      payment.setRequestedAt(Instant.now().getEpochSecond());
      payment.setStoreId("STORE_ID");
      payment.setTerminalId("TERMINAL_ID");
      payment.setOrderReceiptNumber("ORDER_RECEIPT_NUMBER");
      payment.setOrderDescription("ORDER_DESCRIPTION");
      MerchantOrderItem merchantOrderItem =
              new MerchantOrderItem()
                      .category("pasteries").name("Moon Cake")
                      .productId("PRODUCT_ID").quantity(1)
                      .unitPrice(new MoneyAmount().amount(10).currency(MoneyAmount.CurrencyEnum.JPY));
      List<MerchantOrderItem> merchantOrderItems = new ArrayList<>();
      merchantOrderItems.add(merchantOrderItem);
      payment.setOrderItems(merchantOrderItems);

String agreeSimilarTransaction = "false";  //(Optional) If the parameter is set to true, the payment duplication check will be bypassed. 

try {
    PaymentDetails result = apiInstance.createPaymentAuthorization(payment, agreeSimilarTransaction);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#createPaymentAuthorization");
    System.out.println(e.getResponseBody());
}
```

```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/preauth_capture#operation/createAuth
```

<a name="createContinuousPayment"></a>
# **createContinuousPayment**
> PaymentDetails createContinuousPayment(body)

Create a continuous payment
Create a continuous payment and start the money transfer.  **Timeout: 30s** 

NOTE: If you would like to specify payment method, see also [**getPaymentMethods**](PaymentApi.md#getPaymentMethods).

### Example
```java
//Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;


PaymentApi apiInstance = new PaymentApi(apiClient);

Payment payment = new Payment();
      payment.setAmount(new MoneyAmount().amount(1).currency(MoneyAmount.CurrencyEnum.JPY));
      payment.setMerchantPaymentId("MERCHANT_PAYMENT_ID");
      payment.setUserAuthorizationId("USER_AUTHORIZATION_ID");// User should be with scope continuous_payments
      payment.setRequestedAt(Instant.now().getEpochSecond());
      payment.setStoreId("STORE_ID");
      payment.setTerminalId("TERMINAL_ID");
      payment.setOrderReceiptNumber("ORDER_RECEIPT_NUMBER");
      payment.setOrderDescription("ORDER_DESCRIPTION");
      MerchantOrderItem merchantOrderItem =
              new MerchantOrderItem()
                      .category("pasteries").name("Moon Cake")
                      .productId("PRODUCT_ID").quantity(1)
                      .unitPrice(new MoneyAmount().amount(10).currency(MoneyAmount.CurrencyEnum.JPY));
      List<MerchantOrderItem> merchantOrderItems = new ArrayList<>();
      merchantOrderItems.add(merchantOrderItem);
      payment.setOrderItems(merchantOrderItems);

try {
    PaymentDetails result = apiInstance.createContinuousPayment(payment);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#createContinuousPayment");
    System.out.println(e.getResponseBody());
}
```

```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/continuous_payments#operation/createPayment
```

<a name="revertAuth"></a>
# **revertAuth**
> PaymentDetails revertAuth(body)

Revert a payment authorization

This api is used in case, the merchant wants to cancel the payment authorization because of cancellation of the order by the user.  **Timeout: 30s** 

### Example
```java
//Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;


PaymentApi apiInstance = new PaymentApi(apiClient);

PaymentStateRevert payment = new PaymentStateRevert();
      payment.setPaymentId("Payment_Transaction_ID");
      payment.setMerchantRevertId(UUID.randomUUID().toString());
      payment.setRequestedAt(Instant.now().getEpochSecond());

try {
    RevertAuthResponse result = apiInstance.revertAuth(payment);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#revertAuth");
    System.out.println(e.getResponseBody());
}
```

```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/preauth_capture#operation/revertAuth
```


<a name="createQRCode"></a>
# **createQRCode**
> QRCodeDetails createQRCode(body)

Create a Code

Create a Code to receive payments.  **Timeout: 30s** 

### Example
```java
// Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;

PaymentApi apiInstance = new PaymentApi(apiClient);

QRCode qrCode = new QRCode();
      qrCode.setAmount(new MoneyAmount().amount(amount).currency(MoneyAmount.CurrencyEnum.JPY));
      qrCode.setMerchantPaymentId(MERCHANT_PAYMENT_ID);
      qrCode.setCodeType("ORDER_QR");
      qrCode.requestedAt(Instant.now().getEpochSecond());
      qrCode.redirectUrl("REDIRECT_URL");
      qrCode.redirectType(QRCode.RedirectTypeEnum.WEB_LINK);//For Deep Link, RedirectTypeEnum.APP_DEEP_LINK
      qrCode.setOrderDescription("ORDER_DESCRIPTION");
      MerchantOrderItem merchantOrderItem =
              new MerchantOrderItem()
                      .category("Psteries").name("Moon Cake")
                      .productId("PRODUCT_ID").quantity(1)
                      .unitPrice(new MoneyAmount().amount(10).currency(MoneyAmount.CurrencyEnum.JPY));
      List<MerchantOrderItem> merchantOrderItems = new ArrayList<>();
      merchantOrderItems.add(merchantOrderItem);
      qrCode.setOrderItems(merchantOrderItems);

try {
    QRCodeDetails result = apiInstance.createQRCode(qrCode);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#createQRCode");
    System.out.println(e.getResponseBody());
}
```

```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/dynamicqrcode#operation/createQRCode
```

<a name="deleteQRCode"></a>
# **deleteQRCode**
> NotDataResponse deleteQRCode(codeId)

Delete a Code

Delete a created Code.  **Timeout: 15s** 

### Example
```java
// Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;



PaymentApi apiInstance = new PaymentApi(apiClient);

String codeId = "QR_CODE_ID"; 

try {
    NotDataResponse result = apiInstance.deleteQRCode(codeId);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#deleteQRCode");
    System.out.println(e.getResponseBody());
}
```

```
 Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/dynamicqrcode#operation/deleteQRCode
```


<a name="getPaymentDetails"></a>
# **getPaymentDetails**
> PaymentDetails getPaymentDetails(merchantPaymentId)

Get payment details

Get payment details.  **Timeout: 15s** 

### Example
```java
// Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;



PaymentApi apiInstance = new PaymentApi(apiClient);

String merchantPaymentId = "MERCHANT_PAYMENT_ID"; // String

try {
    PaymentDetails result = apiInstance.getPaymentDetails(merchantPaymentId);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#getPaymentDetails");
    System.out.println(e.getResponseBody());
}
```

```
 Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/direct_debit#operation/getPaymentDetails
```

<a name="getPaymentMethods"></a>
# **getPaymentMethods**
> PaymentMethodsResponse getPaymentMethods(userAuthorizationId)

Get payment methods

Get payment methods.  **Timeout: 30s**

### Example
```java
// Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;



PaymentApi apiInstance = new PaymentApi(apiClient);

String userAuthorizationId = "USER_AUTHORIZATION_ID"; // String
ProductType productType = null; // Optional

try {
    PaymentMethodsResponse result = apiInstance.getPaymentMethods(userAuthorizationId, productType);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#getPaymentMethods");
    System.out.println(e.getResponseBody());
}
```

You can specify payment method by `setPaymentMethodType` and `setPaymentMethodId`.

```java

import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;


PaymentApi apiInstance = new PaymentApi(apiClient);

String agreeSimilarTransaction = "false"; // (Optional) If the parameter is set to true, the payment duplication check will be bypassed. 
String userAuthorizationId = "USER_AUTHORIZATION_ID"; // String
ProductType productType = null; // Optional

Payment payment = new Payment();
// ... set various fields to build a request body


try {
    // Get availabkle payment methods
    PaymentMethodsResponse paymentMethodsResponse = apiInstance.getPaymentMethods(userAuthorizationId, productType);
    List<PaymentMethod> methods = paymentMethodsResponse.getData().getPaymentMethods();

    // Set payment method if the response contains what you want for the payment
    Optional<PaymentMethod> method = methods.stream()
        .filter(x -> "PAY_LATER_CC".equals(x.getPaymentMethodType()))
        .findFirst();

    if (method.isPresent()) {
        payment.setPaymentMethodType(method.get().getPaymentMethodType());
        payment.setPaymentMethodId(method.get().getPaymentMethodId());
    }

    PaymentDetails result = apiInstance.createPayment(payment, agreeSimilarTransaction);
    System.out.println(result);
} catch (ApiException e) {
    System.out.println(e.getResponseBody());
}

```

```
 Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/direct_debit#tag/Payment/operation/GetPaymentMethods
```

<a name="getCodesPaymentDetails"></a>
# **getCodesPaymentDetails**
> PaymentDetails getCodesPaymentDetails(merchantPaymentId)

Get payment details

Get payment details.  **Timeout: 15s** 

### Example
```java
// Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;



PaymentApi apiInstance = new PaymentApi(apiClient);

String merchantPaymentId = "MERCHANT_PAYMENT_ID";

try {
    PaymentDetails result = apiInstance.getCodesPaymentDetails(merchantPaymentId);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#getCodesPaymentDetails");
    System.out.println(e.getResponseBody());
}
```

```
 Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/dynamicqrcode#operation/getPaymentDetails
```

<a name="getRefundDetails"></a>
# **getRefundDetails**
> RefundDetails getRefundDetails(merchantRefundId)

Get refund details

Get refund details.  **Timeout: 15s** 

### Example
```java
// Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;



PaymentApi apiInstance = new PaymentApi(apiClient);

String merchantRefundId = "MERCHANT_REFUND_ID";

try {
    RefundDetails result = apiInstance.getRefundDetails(merchantRefundId);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#getRefundDetails");
    System.out.println(e.getResponseBody());
}
```

```
 Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/dynamicqrcode#operation/getRefundDetails
```

<a name="refundPayment"></a>
# **refundPayment**
> RefundDetails refundPayment(body)

Refund a payment

Refund a payment.  **Timeout: 30s** 

### Example
```java
// Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;



PaymentApi apiInstance = new PaymentApi(apiClient);

Refund refund = new Refund();
      refund.setAmount(new MoneyAmount().amount(1).currency(MoneyAmount.CurrencyEnum.JPY));
      refund.setMerchantRefundId("MERCHANT_REFUND_ID");
      refund.setPaymentId("PAYMENT_ID");
      refund.setReason("REFUND REASON");
      refund.setRequestedAt(Instant.now().getEpochSecond())

try {
    RefundDetails result = apiInstance.refundPayment(refund);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#refundPayment");
    System.out.println(e.getResponseBody());
}
```

```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/dynamicqrcode#operation/refundPayment
```


<a name="revertAuth"></a>
# **revertAuth**
> RevertAuthResponse revertAuth(body)

Revert a payment authorization

This API is used in case, the merchant wants to cancel the payment authorization because of cancellation of the order by the user.  **Timeout: 30s** 

### Example
```java
// Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;



PaymentApi apiInstance = new PaymentApi(apiClient);

PaymentStateRevert body = new PaymentStateRevert();
      payment.setPaymentId("PAYMENT_ID");
      payment.merchantRevertId("MERCHANT_REVERT_AUTH_ID");
      payment.setRequestedAt(Instant.now().getEpochSecond()); | Revert Authorized Order Request

try {
    RevertAuthResponse result = apiInstance.revertAuth(body);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#revertAuth");
    System.out.println(e.getResponseBody());
}
```

```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/dynamicqrcode#operation/revertAuth
```

<a name="createAccountLinkQRCode"></a>
# **createAccountLinkQRCode**
> LinkQRCodeResponse createAccountLinkQRCode(body)

Create an ACCOUNT LINK QR and display it to the user.

### Example
```java
// Import classes:
import jp.ne.paypay.ApiException;
import jp.ne.paypay.api.PaymentApi;



PaymentApi apiInstance = new PaymentApi(apiClient);

AccountLinkQRCode accountLinkQRCode = new AccountLinkQRCode();
      List<AuthorizationScope> scopes = new ArrayList<>();
      scopes.add(AuthorizationScope.DIRECT_DEBIT);
      accountLinkQRCode.setScopes(scopes);
      accountLinkQRCode.setNonce(RandomStringUtils.randomAlphanumeric(8).toLowerCase());
      accountLinkQRCode.setDeviceId("device_id");
      accountLinkQRCode.setRedirectUrl("merchant.domain/test");
      accountLinkQRCode.setPhoneNumber("phone_number");
      accountLinkQRCode.setReferenceId("reference_id");
      accountLinkQRCode.setRedirectType(QRCode.RedirectTypeEnum.WEB_LINK);

try {
    LinkQRCodeResponse result = apiInstance.createAccountLinkQRCode(accountLinkQRCode);
    System.out.println(result);
} catch (ApiException e) {
    System.err.println("Exception when calling PaymentApi#createAccountLinkQRCode");
    System.out.println(e.getResponseBody());
}
```

```
Please refer to the below document for more information :
https://www.paypay.ne.jp/opa/doc/v1.0/account_link#operation/createQRSession
```