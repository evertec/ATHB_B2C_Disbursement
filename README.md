# ATHB_B2C_Disbursement
Repository dedicated to ATH Business Disbursement integration documents. 

For B2C Disbursement Transactions - Merchants

# Change Log

| Date | Changes | Comments |
| --- | --- | --- |
| 17/01/2025 | Version 1.0.0 | Initial release of B2C Disbursement API. |

# Table of Contents

**[Change Log](#change-log)**

**[Overview](#overview)**

**[Prerequisites](#prerequisites)**

**[Validate Users](#validate-users)**

**[Payment Service (Send Disbursement)](#payment-service-send-disbursement)**

**[Authorization Service](#authorization-service)**

**[Cancel Disbursement](#cancel-disbursement)**

## Overview

API Services responsible for managing disbursement transactions (Business to Consumer). These services allow merchants to:
* Validate Users (Client, Business, and Card).
* Send a Disbursement Transaction.
* Cancel a Disbursement Transaction.

**Base URL:** `https://business.athmovil.com/api`

## Prerequisites

Before using the ATH Móvil's disbursement services you need:

1.  **Authentication:** An API Key/Token provided by ATH Móvil.
2.  **Public Token:** The identifier for the business account.
3.  **Headers:**
    * `Content-Type: application/json`
    * `Accept: application/json`
    * `Authorization: <api_key>` (Required for most endpoints).

## Validate Users

This service validates the client, business, and card status before retrying or initiating a payment flow.

**Endpoint:** `https://business.athmovil.com/api/b2c/validate-users`

**Method:** `POST`

**Headers:**
* Content-type: application/json
* Authorization: `<api_key>`

**Request**

* **publicToken**: Business account identifier.
* **phoneNumber**: The phone number of the user to be validated.

```json
{
  "publicToken": "1f99b07c57ff2c3a5e3c9d987660aadecac85a4b",
  "phoneNumber": "1234567890"
}

```
**Response**

```json

{
  "status": "success",
  "data": {
    "isValid": true
  }
}
```
## Payment Service (Send Disbursement)

Request to initiate the payment (disbursement) from a business to a person. There are two ways to execute this:

Via a PIN code: Requires running the /authorize endpoint afterwards to complete the transaction.

Without PIN code: The payment authorization is completed immediately in this call.

Endpoint: https://business.athmovil.com/api/b2c/payment

**Method** POST

**Headers**:

Content-type: application/json

Authorization: <api_key>

**Request**

pinCode: Boolean. Set true if a PIN flow is required, false for direct disbursement.

amount: Amount to be disbursed (Minimum 0.01).

```json
{
  "publicToken": "1f99b07c57ff2c3a5e3c9d987660aadecac85a4b",
  "phoneNumber": "7777777777",
  "metadata1": "metadata1",
  "metadata2": "metadata2",
  "amount": 1500,
  "pinCode": false,
  "message": "Service Payment"
}
```
**Response**
(Scenario A: Flow WITH PIN) Returns an auth_token and disbursementId to be used in the authorization step.
```json
{
  "status": "success",
  "data": {
    "disbursementId": "9a3ee5e1-1f51-11ef-aea4-8fb6c7b084ee",
    "auth_token": "eyJraWQiOiJNeUtVRXZvb2NSMWptbnZocHZXVEI0WmZvcU1wbEx6TWF5VzdjUWd1ck5FIiwidHlwIjoiSldUIiwiYWxnIjoiUlMyNTYifQ..."
  }
}
```
**Response**
(Scenario B: Flow WITHOUT PIN) Returns the completed transaction details.
```json
{
  "status": "success",
  "data": {
    "disbursementStatus": "COMPLETED",
    "disbursementId": "1dcf8cb8-1f5f-11ef-94ec-b54057f8c9f7",
    "referenceNumber": "215073070-40288a0f8fcf2c4e018fcf2f860a0000",
    "businessCustomerId": "8a36db988613dfba0186608a79cc003d",
    "transactionDate": "2024-05-31 10:05:46",
    "dailyTransactionId": "0003",
    "businessName": "JFG Ok",
    "businessPath": "JFG",
    "industry": "ACCOUNTING",
    "subTotal": null,
    "tax": null,
    "total": null,
    "fee": 0.35,
    "netAmount": 13.43,
    "totalRefundedAmount": null,
    "metadata1": null,
    "metadata2": null,
    "isNonProfit": false,
    "transactionSubtype": "DISBURSEMENT"
  }
}
```

## Authorization Service
Authorization Service
Request to authorize payment from the business to the person using a PIN code. This is required if pinCode: true was sent in the Payment Service.

Endpoint: https://business.athmovil.com/api/b2c/authorize

Method: POST

Headers:

Content-type: application/json

Authorization: <auth_token> (Received from /payment response)

**Request**

pinCode: The integer code to authorize the transaction.

```json
{
  "pinCode": 214278
}
```
**Response**

```json
{
  "status": "success",
  "data": {
    "disbursementStatus": "COMPLETED",
    "disbursementId": "1dcf8cb8-1f5f-11ef-94ec-b54057f8c9f7",
    "referenceNumber": "215073070-40288a0f8fcf2c4e018fcf2f860a0000",
    "businessCustomerId": "8a36db988613dfba0186608a79cc003d",
    "transactionDate": "2024-05-31 10:05:46",
    "dailyTransactionId": "0003",
    "businessName": "JFG",
    "businessPath": "JFG",
    "industry": "ACCOUNTING",
    "subTotal": null,
    "tax": null,
    "total": null,
    "fee": 0.35,
    "netAmount": 13.43,
    "totalRefundedAmount": null,
    "metadata1": null,
    "metadata2": null,
    "isNonProfit": false,
    "transactionSubtype": "DISBURSEMENT"
  }
}
```

## Cancel Disbursement

Cancels a disbursement that is currently in OPEN status using the disbursement ID generated by the B2C transaction.

Endpoint: https://business.athmovil.com/api/b2c/business-cancel

Method: POST

Headers:

Content-type: application/json

Authorization: <api_key>


**Request**
```json
{
  "disbursementId": "c7fea0f9-e77f-11ee-8682-e7a8e118fb1b"
}
```

**Response**
```json
202 Accepted (String response)
```



# Global Error Log
**Error Codes**
Comprehensive list of all possible error codes returned by the B2C Disbursement API services.

**Error Codes:**

| Http Status | Error Code | Message (en) | Message (es) |
| :--- | :--- | :--- | :--- |
| 409 | BTRA_0003 | Card number from the customer is the same than the business | Número de tarjeta del cliente es el mismo que el del negocio |
| 409 | BTRA_0004 | Amount is Over the limits | El monto está por encima de los límites |
| 409 | BTRA_0005 | Transaction failed. Status error | Transacción fallida. Estatus de error |
| 400 | BTRA_0006 | Invalid format | Formato inválido |
| 400 | BTRA_0018 | must not be blank | no debe estar vacío |
| 400 | BTRA_0019 | must not be null | no debe ser nulo |
| 400 | BTRA_0020 | numeric value out of bounds (<4 digits>.<2 digits> expected) | valor numérico fuera de los límites (<4 dígitos>. <2 dígitos> esperados) |
| 409 | BTRA_0035 | The e-commerce transaction has not the expected status | El estatus del registro de Transacción no es el esperado |
| 400 | BTRA_0038 | The metadata field exceeds the maximum supported characters (40) | El campo metadata excede el máximo de caracteres soportado (40) |
| 409 | BTRA_0039 | Valid time to execute the transaction has expired | Tiempo válido para ejecutar la transacción ha expirado |
| 409 | BTRA_0067 | Invalid pin code | Código PIN inválido |
| 409 | BTRA_0068 | B2C transaction / Disbursement identifier not found | Transacción B2C / Identificador del desembolso no encontrado |
| 409 | BTRA_0071 | Disbursements not allowed | Desembolsos no permitido |
| 409 | BTRA_0072 | The card associated with your business has insufficient balance | La tarjeta asociada a tu negocio tiene balance insuficiente |
| 409 | BTRA_0076 | Card with condition. The user must validate with their financial institution. | Tarjeta con condición. Usuario debe validar con su institución financiera. |
| 409 | BTRA_0077 | ATH Movil user cannot receive transactions | Usuario de ATH Móvil no puede recibir transacciones |
| 409 | BTRA_0078 | ATH Business user cannot send transactions | Usuario de ATH Business no puede enviar transacciones |
| 401 | BTRA_0401 | Invalid authorization token | Token de autorización no válido |
| 401 | BTRA_0402 | Authorization token expired | Token expirado |
| 401 | BTRA_0403 | Invalid authorization token | Cabecera de autorización inválida |
| 409 | BTRA_9998 | Communication Error | Error de Comunicación |
| 500 | BTRA_9999 | Internal Error | Error Interno |
