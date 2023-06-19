# Integration API - Reference guide

# Overview

The Auto SAPO Integration API is organized around REST, taking advantage of all semantics of Standard HTTP like status codes and verbs.

We have adopted json as the default and only format for all Requests and Responses.

To use the API is required to follow these steps:
 - Step 1 - **Authorization** - *Get a access_token*
 - Step 2 - **Locate vehicle** - *Find or Request a vehicle creation*
 - Step 3 - **Manage Adverts** - *Compose Adverts*
 - Step 4 - **Publish Adverts** - *Get adverts online/offline*

We provide two environments of Integration API:

### SANDBOX
The purpose of SANDBOX environment is to provide a stable and controlled way to help you to develop and test your application when communicating with Auto SAPO.

Is **HIGHLY RECOMENDED** to develop and test all functionalities on your use cases before start using Production environment. 

**ATTENTION** - Due to the testing nature of this environment, data will periodically wiped out, so you **SHOULD** consider that (for example) overnight you might lose adverts, photos, publications, etc,...

This environment uses the following base addresses:
 - AUTHORIZATION: https://auto-oauth-sbx.sapo.pt
 - INTEGRATION API: https://auto-integration-sbx.sapo.pt

### PRODUCTION
Production is the live environment where all interactions will take effect on Auto SAPO website.

You **SHOULD NEVER** use this environment to test functionalities.

This environment uses the following base addresses:
 - AUTHORIZATION: https://TBA
 - INTEGRATION API: https://TBA

---
# SETP 1 - Authorization

Before you start managing and publishing your adverts, you will need to obtain one `access_token` in order to be able to interact with the API. 

To obtain the `access_token` you need to use a `client_id` and a `client_secret` witch you can obtain using Backoffice on "Conta > Aplicações" to create a new "Application Access".

**Request to obtain access token**
```sh
curl -d '{"client_id":"yEybaeZzlKz0tSZt","client_secret":"DdgMCk24nGwIq1icUvaNxDpL46QWgQge"}' \
	-H "Content-Type: application/json" \
	-X POST https://auto-oauth-sbx.sapo.pt/token
```

**Response with access token**
```json
{
  "access_token":"...ey87Jkujdfuhdfiuhf2332d776sahbGciOiJSUzI1N...",
  "expires_in":28800
}
```

Now you are able to send requests using the Authorization header as follows:
```sh
curl -H "Authorization: Bearer <TOKEN_FROM_OAUTH_ENDPOINT>" \
	-X GET https://auto-integration.staging.sapo.pt/adverts
```


### Endpoints
 - SANDBOX: https://auto-oauth-sbx.sapo.pt/token
 - PRODUCTION: https://TBA
 
 ---
# STEP 2 - Locate vehicle

Before an advert can be created, is necessary to ensure that AutoSAPO knowns the vehicle's License plate.

If a license plate is not yet known, you can request to add it.

Please use the REST API endpoints grouped as `Vehicles` and `Vehicles lookup data`.

For a detailed documentation please check: https://auto-integration-sbx.sapo.pt/swagger

---
# STEP 3 - Manage Adverts

Now its time to create a new advert or edit an existing one.

Note that:
 - You can only have one advert per license plate since you can publish it multiple times;
 - To add photos you need to create the advert first;
 - Use `Advertiser locations lookup` to obtain the `locationKey` of advert. Locations are read-only on the API and managed on Backoffice;
 - Changes made to an already published (online) advert will never take effect until you republish it again;

Please use the REST API endpoints grouped as `Advertiser locations lookup` and `Adverts Management`.
For a detailed documentation please check: https://auto-integration-sbx.sapo.pt/swagger

---
# SETP 4 - Publish Adverts

The final step is meant to manage the presence of the advert on Auto SAPO, taking it online/offline.
This step is asynchronous, so, all the necessary actions to take one advert online/offline will be processed in background.

Note that:
 - A publication is a online snapshot of one advert;
 - Changes on the adverts (step 3) will not get online until the publish operation is invoked;

Please use the REST API endpoints grouped as `Adverts Publication`.
For a detailed documentation please check: https://auto-integration-sbx.sapo.pt/swagger

---