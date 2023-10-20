# Integration API - Reference guide

# Overview

The Auto SAPO Integration API is organized around [REST](https://en.wikipedia.org/wiki/Representational_state_transfer), taking advantage of all semantics of Standard HTTP like status codes and verbs.

We have adopted [JSON](https://en.wikipedia.org/wiki/JSON) as the default and only format for all Requests and Responses.

To use the API, these steps must be followed:
 - Step 1 - **Authorization** - *Get a access_token*
 - Step 2 - **Locate vehicle** - *Find or Request a vehicle creation*
 - Step 3 - **Manage Adverts** - *Compose Adverts*
 - Step 4 - **Publish Adverts** - *Get adverts online/offline*

We provide two environments of Integration API:

### SANDBOX
The purpose of SANDBOX environment is to provide a stable and controlled way to help you to develop and test your application when communicating with Auto SAPO.

It is **HIGHLY RECOMMENDED** to develop and test all functionalities on your use cases before start using Production environment. 

**ATTENTION** - Due to the testing nature of this environment, data will be periodically wiped out, so you **SHOULD** consider that (for example) overnight you might lose adverts, photos, publications, etc,...

If you don't have a SANDBOX advertiser account yet, you can create a new one using the following address: https://auto-frontoffice-sbx.sapo.pt/

In order to test and check how adverts will be presented, please use the testing address https://auto-frontend-sbx.sapo.pt. 

This environment uses the following base addresses:
 
 - AUTHORIZATION: https://auto-oauth-sbx.sapo.pt
 - INTEGRATION API: https://auto-integration-sbx.sapo.pt

### PRODUCTION
Production is the live environment where all interactions will take effect on Auto SAPO website.

New accounts should be created using the following address: https://auto-frontoffice.sapo.pt/

You **SHOULD NEVER** use this environment to test functionalities.

This environment uses the following base addresses:
 - AUTHORIZATION: https://auto-oauth.sapo.pt/
 - INTEGRATION API: https://auto-integration.sapo.pt/

---
# STEP 1 - Authorization

Before you start managing and publishing your adverts, you will need to obtain one `access_token` in order to be able to interact with the API. 

To obtain the `access_token` you need to use a `client_id` and a `client_secret` which you can obtain using Backoffice on "Conta > Aplicações" to create a new "Application Access".

**Request to obtain access token**
```sh
curl -X POST https://auto-oauth-sbx.sapo.pt/token \
	-H "Content-Type: application/json" \	
	-d '{"client_id":"XXXXX","client_secret":"YYYYY"}' 
```

**Response with access token**
```json
{
  "access_token":"...ey87JkJSUzI1N...",
  "expires_in":28800
}
```

Now you are able to send requests using the Authorization header as follows:
```sh
curl -X GET https://auto-integration.staging.sapo.pt/adverts \
	 -H "Authorization: Bearer <TOKEN_FROM_OAUTH_ENDPOINT>"
```
***NOTE:*** *It is expected the reutilization of access_token across multiple calls while it is valid. You **SHOULD NOT** create an access_token per endpoint call.*

### Endpoints
 - SANDBOX: https://auto-oauth-sbx.sapo.pt/token
 - PRODUCTION: https://auto-oauth.sapo.pt/token

 ---
# STEP 2 - Check vehicle's existence

Before an advert can be created, it is necessary to ensure that AutoSAPO has the vehicle's Information, you can check it by License plate OR VIN.

**Examples of check request**
```sh
curl -X HEAD https://auto-integration-sbx.sapo.pt/vehicles/{licenceplate} \
	 -H "Authorization: Bearer {access_token}" 

# OR

curl -X HEAD https://auto-integration-sbx.sapo.pt/vehicles/VIN/{vin} \
	 -H "Authorization: Bearer {access_token}" 

# A response with the status code:
#   - 200 means we have the vehicle's information
#   - 404 means we don't have the vehicle's information
```

If AutoSAPO doesn't have the vehicle's information, you can request to add it.

**Example of vehicle's creation**
```sh
curl -X POST https://auto-integration-sbx.sapo.pt/vehicles \
	 -H "Authorization: Bearer {access_token}" \
	 -H "Content-Type: application/json" \
	 -d "{ ...vehicle properties...  }" 

# A response with the status code:
#   - 201 means you have added the vehicle's information
#   - 400 means that something is wrong on vehicle's information
```

Please use the REST API endpoints grouped as `Vehicles` and `Vehicles lookup data`.

***NOTE:*** *If you detect that some lookup data is missing (as for example when a manufacturer releases a new model or version) you are always welcome to request us to add it.*

For a detailed documentation please check: https://auto-integration-sbx.sapo.pt/swagger

---
# STEP 3 - Manage Adverts

Now it's time to create a new advert or edit an existing one.

**Example of advert's creation**
```sh
curl -X POST https://auto-integration-sbx.sapo.pt/adverts \
	 -H "Authorization: Bearer {access_token}" \
	 -H "Content-Type: application/json" \
	 -d "{ ...advert properties...  }" 

# A response with the status code:
#   - 201 means the advert was created
#   - 400 means that something is wrong in advert request
```

**Example of adding image to advert**
```sh
curl -H "Authorization: Bearer {access_token}"\ 
	 -F "file=@advertphoto.jpg" \
	 https://auto-integration-sbx.sapo.pt/adverts/{idadvert}/photo

# A response with the status code:
#   - 201 means the image was added to the advert
#   - 400 means that something is wrong in the request
```

Note that:
 - You can only have one advert per license plate since you can publish it multiple times;
 - To add photos you need to create the advert first;
 - Use `Advertiser locations lookup` to obtain the `locationKey` of advert. Locations are read-only on the API and managed on Backoffice;
 - Changes made to an already published (online) advert will never take effect until you republish it again;

Please use the REST API endpoints grouped as `Advertiser locations lookup` and `Adverts Management`.
For a detailed documentation please check: https://auto-integration-sbx.sapo.pt/swagger

---
# STEP 4 - Publish Adverts

The final step is meant to manage the presence of the advert on Auto SAPO, taking it online/offline.

This step is asynchronous therefore, all the necessary actions to take one advert online/offline will be processed in background.

Note that:
 - A publication is a online snapshot of one advert;
 - Changes on the adverts (step 3) will not get online until the publish operation is invoked;

Please use the REST API endpoints grouped as `Adverts Publication`.
For a detailed documentation please check: https://auto-integration-sbx.sapo.pt/swagger

---