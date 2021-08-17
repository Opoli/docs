# holoholo API Documentation

The main difference of holoholo from other RideShare PRO servers is the [Pricing Formula](#pricing-formula)

## Endpoint

These are the base URLs where requests should be made. The routes indicated on each request should be appended to either of these URLs.

| Property | Description |
| --- | --- |
| Staging | `https://staging-gateway.opoli.com` (same staging as any RideShare PRO) |
| Production | `https://hl-tnc-api.ridesharepro.com` |

### Example
If a request says `POST /1.1/auth/login`, the final endpoint will be:
```
POST https://staging-gateway.opoli.com/1.1/auth/login
```

----

## Authentication
To start using the Opoli API, the client application will have to first generate a device **token**.

### Request

| Property | Description |
| --- | --- |
| `email` | The client application email |
| `password` | The client application password |
| `device` | The custom access token that will be used on further requests to the platform. Starts with `VND-`. The succeeding `x` characters is a uuid4 string. Client applications generate this. Moving forward, this will be referred to as the **token**. |
| `deviceType` | Possible values: `android` / `ios` / `ww` |

```
POST /1.1/auth/login
```
```
{
  "email": "",
  "password": "",
  "device": "VND-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "deviceType": ""
}
```

### Response
If the response contains an `ID` property, the authentication  is successful, and the `device` string in the request can now be used in further requests. Otherwise, see section *Handling Errors*.

The other fields are just the application information and can be ignored.

| Property | Description |
| --- | --- |
| `ID` | The client application ID |

```
{
  "ID": 1,
  "email": "",
  "firstName": "",
  "lastName": "",
  "language": null,
  "referralCode": "",
  "avatarUrl": null,
  "emailSubscribed": 1,
  "phone": 1,
  "countryCode": 1,
  "created": "",
  "type": "",
  "ada": {
    "wheelChairLift": 1,
    "serviceDog": 1
  }
}
```

----

## Getting a Ride Quotation

To display list of services and their pricing to the customer, the client application must request for a quotation, using the desired pickup date, pickup and dropoff location.

### Request

| Property | Description |
| --- | --- |
| `brandId` | The brand of the requesting application. Manually ask the devleopers about this. Note: Staging and Production brand IDs are different. |
| `pickupDate` | The customer's desirec pickup date. Format: `YYYY-MM-DD` |
| `pickup` | An object containing pickup location's `lat` and `lng` |
| `dropoff` | An object containing dropoff location's `lat` and `lng` |
| `order` | The sorting of the list of services. Possible values: `priority` |

The quotation endpoint is public and does not require the **token**.

```
POST /2.0/quotation
```
```
{
  "brandId": 0,
  "pickupDate": "0000-00-00",
  "pickup": {
    "lat": 00.00000,
    "lng": -000.00000
  },
  "dropoff": {
    "lat": 00.00000,
    "lng": -000.00000
  },
  "order": "priority"
}
```

### Response

The following example response is a trimmed down version, where only the important information and shown. The staging and production APIs may give more data, but is still using the same structure.

| Property | Description |
| --- | --- |
| `route` | An object containing the route information, such as `distance` and `eta`. The ETA (`eta.sec`) is in number of seconds. |
| `services` | An array of objects containing the list of services. For the structure inside each object, see *The Service Object* on the next section. |

```
{
  "route": {
    "distance": 00.0000000,
    "eta": {
      "sec": 000
    }
  },
  "services": [
    { /* SERVICE OBJECT */ },
    { /* SERVICE OBJECT */ },
    { /* SERVICE OBJECT */ },
  ]
}
```

### The Service Object

| Property | Description |
| --- | --- |
| `serviceType` | The information about this specific service type. Used to display information to the customer. |
| `priceType` | The formula used for calculating the price. Possible values: `regular` / `discounted` / `holiday` |
| `settings` | Information that can be useful to the client information for display purposes, e.g. discount dates to display time left, etc. |
| `oneway` | Array of objects for one-way pricing, with each object pertaining to a specific number of passengers. When customer selects a different passenger count, the client application should use the pricing on the object that corresponds to the new value. |
| `roundtrip` | Contains pricing for roundtrips. Similar structure to the `oneway` array. |
| `pickupTypes` | The list of pickup types available for this service. Each one has its own `name` string, `fee` amount, and `enabled` flag. Currently, only `Curbside` and `MeetAndGreet` are possible. |

```

{
  "serviceType": {
    "id": 117,
    "name": "",
    "category": "",
    "excerptWeb": "",
    "excerptApp": "",
    "descriptionWeb": "",
    "descriptionApp": "",
    "advantageWeb": "",
    "advantageApp": "",
    "slogan": "",
    "stops": "",
    "image": "",
    "imageOff": "",
    "code": "",
    "isShared": false,
    "maxPassengers": 1,
    "maxLuggages": 1,
    "licenseType": "tcp"
  },
  "priceType": "regular",
  "settings": {
    "discountStart": 0000000000,
    "discountEnd": 0000000000,
    "travelInsurance": 0.00,
    "hasMileageThreshold": false,
    "hasFlatRate": true,
    "airportFee": 0.00,
    "getFee": 0.00
  },
  "oneway": [
    {
      "passengers": 1,
      "price": 00.00,
      "original": 00.00,
      "discount": 0,
      "distance": 0.0000000
    },
    {
      "passengers": 2,
      "price": 00.00,
      "original": 00.00,
      "discount": 0,
      "distance": 0.0000000
    },
    ....
  ],
  "roundtrip": [
    {
      "passengers": 1,
      "price": 00.00,
      "original": 00.00,
      "discount": 0,
      "distance": 0.0000000
    },
    {
      "passengers": 2,
      "price": 00.00,
      "original": 00.00,
      "discount": 0,
      "distance": 0.0000000
    },
    ....
  ],
  "pickupTypes": [
    {
      "name": "Curbside",
      "fee": 0.00,
      "enabled": true
    },
    {
      "name": "MeetAndGreet",
      "fee": 0.00,
      "enabled": false
    }
  ]
}
```

----

## Pricing Formula

Unlike other RideShare PRO servers, holoholo has a different pricing formula.

* `travelInsurance` ("travel insruance" / "service fee" / "rider fee")
  * On other RideShare PRO servers, this property is based off miles. On holoholo, this is a fixed value.
  * `.services[n].settings.travelInsurance`

* `airportFee`
  * On other RideShare PRO servers, this property is fixed. On holoholo, this is calculated.
  * `Airport Fee = (Fare + ServiceFee) x AirportFee%`

* `getFee`
  * This property does not exist on other RideShare PRO servers.
  * `GET Fee = (Fare + ServiceFee + AirportFee) x GET%`

### Total Calculation
```
totalPay = fare + travelInsurance + airportFee + getFee
```
Example:
```
baseFare = 70.00
mileageRate = 1.00
distanceMiles = 30

fare = 100.00
travelInsurance = 5.00

Settings Airport Fee 7%
airportFee = (100 + 5) * 7% = 7.35

Settings GET fee 4.71%
getFee = (100 + 5 + 7.35) * 4.71 = 5.29

total = 100 + 5 + 7.35 + 5.29 = 117.64
```

----

## Booking a Reservation

After the review page, and the customer confirms to book the reservation, the client application must submit the following request. 

### Request

| Property | Description |
| --- | --- |
| `token` | The **token** which was submitted during authentication |
| `brandId` | The brand of the requesting application. Manually ask the devleopers about this. Note: Staging and Production brand IDs are different. |
| `guest` | If the customer is booking for someone else, defines information about the actual passenger. This is an object that contains `firstName`, `lastName`, and `mobile`. Otherwise, submit as `null` e.g. `"guest": null` |
| `legs` | An array of objects, that corresponds to each leg of the trip. Can only contain two elements, first one being the first leg, and the second one being the roundtrip. If the trip is one-way, the second index should be `null`. See *Leg Object*. |
| `payment` | The payment information. See *Payment Object*. |
| `wheelchair` | Whether the passenger needs wheelchair lift. |
| `serviceDog` | Whether the passenger has a service dog. |
| `source` | Always supply `C`, meaning the source of booking request is the customer. |
| `deviceType` | Possible values: `android` / `ios` / `ww` |

### The Leg Object

| Property | Description |
| --- | --- |
| `pickup` | An object containing pickup location information |
| `dropoff` | An object containing dropoff location's information |
| `pickupTime` | The pickup time in UNIX seconds |
| `passengers` | The number of passengers that the user selected |
| `luggage` | The number of luggage that the user selected |
| `amount` | The final price of the service. Received from the quotation object. |
| `originalPrice` | The original price of the service. Received from the quotation object. |
| `airportFee` | The amount of airport fee. Received from the quotation object. |
| `flightName` | The pickup airline, if applicable |
| `flightNumber` | The pickup flight number, if applicable |
| `dropoffFlightName` | The dropoff airline, if applicable |
| `dropoffFlightNumber` | The dropoff flight number, if applicable |
| `asap` | Boolean if this is an ASAP request. Expected to be always `false` as of latest implementation |
| `etaInSec` | The ETA shown to the customer when they reviewed the booking. Received from the quotation object. |
| `serviceTypeId` | The service type ID that the user selected |
| `pickupType` | The pickup type |
| `pickupFee` | The pickup fee amount, if applicable  |
| `distance` | The distance received from the quotation object |
| `notes` | The consumer's note to driver |
| `travelInsurance` | Called the Rider Fee, it is a value from the quotation object |
| `getFee` | A new property specific to holoholo, a GET fee |

### The Payment Object

| Property | Description |
| --- | --- |
| `cardID` | The credit card ID that the customer wants to use |
| `tip` | The amount the customer wants to give the driver as a tip |
| `total` | Total cost of the trip, add fares and fees |
| `fare` | The base fare, the price of the service type only |
| `pickupFee` | The pickup fee amount, if applicable |
| `promo` | The amount of credits that the customer desired to use for this booking |


```
POST /1.1/reservations
```

```
{
  "token": "VND-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "brandId": 0,
  "guest": {
    "firstName": "",
    "lastName": "",
    "mobile": ""
  },
  "legs": [
    {
      "pickup": {
        "address1": "",
        "address2": "",
        "city": "",
        "state": "",
        "zip": "",
        "lat": 00.000000,
        "lng": -000.000000,
        "title": "",
        "geofenceId": 000
      },
      "dropoff": {
        "address1": "",
        "address2": "",
        "city": "",
        "state": "",
        "zip": "",
        "lat": 00.000000,
        "lng": -000.000000,
        "title": "",
        "geofenceId": 000
      },
      "pickupTime": 0000000000,
      "passengers": 1,
      "luggage": 1,
      "amount": 00.00,
      "originalPrice": 00.00,
      "airportFee": 00.00,
      "flightName": "",
      "flightNumber": "",
      "dropoffFlightName": "",
      "dropoffFlightNumber": "",
      "asap": false,
      "etaInSec": 000,
      "serviceTypeId": 00,
      "pickupType": "",
      "pickupFee": 0,
      "distance": 00.00000,
      "notes": "",
      "travelInsurance": 00.00
    },
    null
  ],
  "payment": {
    "cardID": 000,
    "tip": 0,
    "total": 00.00,
    "fare": 00.00,
    "pickupFee": 0,
    "promo": 0
  },
  "wheelchair": 0,
  "serviceDog": 0,
  "source": "",
  "deviceType": ""
}
```

### Response

The response will contain the `reservationId` array as the reservations ID(s). If the request was one-way booking, only the first index will have an ID, and the second will be `0`. If the request was roundtrip, both will have an ID.

```
{
  "reservationId": [
    000000,
    000000
  ]
}
```

----

## Retrieving Reservation Information

### Request

To retrive an existing reservation's information, the client application needs the reservation ID, which will be a part of the URL to be requested from. Aside from that, the only data required is the **token** from the authentication process.

| Property | Description |
| --- | --- |
| `token` | The **token** which was submitted during authentication |

```
GET /1.1/me/reservations/<RSVPID>?token=<TOKEN>

e.g. GET /1.1/me/reservations/345678?token=VND-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### Response

| Property | Description |
| --- | --- |
| `ID` | The reservation ID |
| `consumer` | The consumer information |
| `provider` | The driver information. The values will be `null` if there is no driver assiggned yet. |
| `rating` | The rating submitted by the customer after the trip |
| `vehicle` | The vehicle to be used by the driver |
| `pickupDate` | The desired pickup date. Format: `YYYY-MM-DD HH:mm:ss -ZZZZ` |
| `pickupLocation` | The pickup location information |
| `dropoffLocation` | The dropoff location information |
| `guest` | The guest object, if the customer booked for someone else |
| `amount` | The fare amount |
| `originalPrice` | The original price of the service type |
| `tipAmount` | The tip given by the customer to the driver |
| `pickupFee` | The pickup feee, if applicable |
| `total` | The total fare for the trip, including fees |
| `credits` | The amount of credits used for booking |
| `breakdown` | The object containing the breakdown of the fare calculation |
| `notes` | The consumer notes to the driver |
| `distance` | The distance from pickup to dropoff location |
| `passengers` | The specified number of passengers |
| `luggage` | The specified number of lugggage |
| `terminal` | The pickup airport terminal |
| `airline` | The airline name from customer's pickup |
| `airlineCode` | The airline code from customer's pickup |
| `flightNumber` | The flight number from customer's pickup |
| `dropoffAirline` | The airline name from customer's dropoff |
| `dropoffAirlineCode` | The airline code from customer's dropoff |
| `dropoffFlightNumber` | The airline name from customer's dropoff |
| `pickupType` | The pickup type selected |
| `created` | The date when the booking was originally created |
| `modified` | The date when the booking was last modified |
| `status` | The general status of the reservation |
| `bidStatus` | The status of the reservation that is tailored for us of the customer |
| `dispatchStatus` | The status of the reservation that is tailored for us of dispatchers |
| `credit` | (Alias) The amount of credits used for booking |
| `onboard` | The time the driver marked the trip as onboard |
| `complete` | The time the trip was marked as completed |
| `canceled` | The time the trip was marked as canceled |
| `reservationState` | The reservation's state in relation to payment |
| `paidForProvider` | The amount the driver will recieve |
| `cardTitle` | The type of credit card used during booking |
| `cardID` | The credit card number |
| `cardNumber` | The last four digits of the credit card |
| `consumerCardId` | (Alias) The credit card number |
| `consumerCardType` | (Alias) The type of credit card used during booking |
| `consumerCardNumber` | (Alias) The last four digits of the credit card |
| `isAsap` | Whether the trip was booked originally as ASAP or future date |
| `serviceLabel` | (Deprecated) The internal code to determine service type |
| `serviceType` | The object containing service type information |
| `bidDeviceType` | The `deviceType` submitted during the booking |
| `brandID` | The brand ID under which this reservation was booked |
| `packageID` | The package ID, if applicable |
| `ada` | The object that indicates whether the passenger requires either a `wheelChairLift` or `serviceDog` |
| `airportFee` | The airport fee that was added to the total fare |
| `travelInsurance` | The rider fee that was added to the total fare |
| `etaInSec` | The ETA (in seconds) from pickup to dropoff |
| `airportCode` | The airport code, if applicable |
| `overridePrice` | The fare amount if the price was set to be overriden by administrator |
| `overridePayment` | The driver payment amount t if the price was set to be overriden by administrator |


```
{
  "ID": 38786,
  "consumer": {
    "ID": 00000,
    "firstName": "",
    "lastName": "",
    "email": "",
    "avatarUrl": "",
    "phones": []
  },
  "provider": {
    "ID": null,
    "firstName": null,
    "lastName": null,
    "email": null,
    "displayPhone": "",
    "avatarUrl": "",
    "rating": null,
    "businessName": null,
    "driverLicenseNumber": null,
    "phones": []
  },
  "rating": {
    "value": null,
    "message": null,
    "date": null
  },
  "vehicle": {
    "model": null,
    "manufacturer": null,
    "plate": null,
    "photo": ""
  },
  "pickupDate": "0000-00-00 00:00:00 -0800",
  "pickupLocation": {
    "title": "",
    "addressLine1": "",
    "addressLine2": "",
    "zip": "",
    "state": "",
    "city": "",
    "country": null,
    "lat": 00.000000,
    "lng": -000.000000,
    "geofenceId": 0,
    "geofenceType": "",
    "geofenceName": "",
    "airportCode": null
  },
  "dropoffLocation": {
    "title": "",
    "addressLine1": "",
    "addressLine2": "",
    "zip": "",
    "state": "",
    "city": "",
    "country": null,
    "lat": 00.000000,
    "lng": -000.000000,
    "geofenceId": 0,
    "geofenceType": "",
    "geofenceName": "",
    "airportCode": null
  },
  "guest": {
    "firstName": null,
    "lastName": null,
    "phone": null,
    "email": null
  },
  "amount": 00.00,
  "originalPrice": 00.00,
  "tipAmount": 0,
  "pickupFee": 0,
  "total": 00.00,
  "credits": 0,
  "breakdown": {
    "fare": 00.00,
    "total": 00.00,
    "charged": 00.00,
    "promo": 0,
    "pickupFee": 0,
    "tip": 0,
    "airportFee": 00.00,
    "travelInsurance": 00.00
  },
  "notes": null,
  "distance": 00.00,
  "passengers": 1,
  "luggage": 1,
  "airline": null,
  "airlineCode": "",
  "terminal": "",
  "flightNumber": null,
  "dropoffAirline": "",
  "dropoffAirlineCode": null,
  "dropoffFlightNumber": "",
  "pickupType": "",
  "created": "0000-00-00@00:00:00@@@@@",
  "modified": "0000-00-00@00:00:00@@@@@",
  "status": "",
  "bidStatus": "",
  "dispatchStatus": "",
  "credit": 0,
  "onboard": null,
  "complete": null,
  "canceled": null,
  "reservationState": "CHARGE",
  "paidForProvider": 00.00,
  "cardTitle": "MasterCard",
  "cardID": 0,
  "cardNumber": "",
  "consumerCardId": 0,
  "consumerCardType": "",
  "consumerCardNumber": "",
  "isAsap": false,
  "serviceLabel": null,
  "serviceType": {
    "id": 0,
    "name": "",
    "color": "#FFFFFF",
    "label": "",
    "image": "",
    "imageOff": "",
    "licenseType": "tcp"
  },
  "bidDeviceType": "ww",
  "brandID": 0,
  "packageID": null,
  "ada": {
    "wheelChairLift": 0,
    "serviceDog": 0
  },
  "airportFee": 00.00,
  "travelInsurance": 00.00,
  "etaInSec": 0,
  "airportCode": "",
  "overridePrice": 00.00,
  "overridePayment": 00.00
}
```

----

## Updating a Reservation

### Request

The request is the same structure as booking a reservation. The only difference is the URL endpoint to which it needs to be submitted.

The Reservation ID that the customer decides to edit is in the URL, as follows:

```
POST /1.1/me/reservations/<RSVPID>

e.g. POST /1.1/me/reservations/345678
```

This also requires the **token**, same as when initially booking the reservation.

### Response

The response object is the same structure as retrieving the reservation information (See previous section).

----

## Getting Cancellation Fee for a Reservation

We should first get the cancellation fee and display it to the customer, and ask for confirmation. This should be done before the actual cancellation endpoint.

### Request

```
GET /1.1/me/reservations/<RSVPID>/cancel?token=<TOKEN>

e.g. /1.1/me/reservations/345678/cancel?token=VND-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### Response
```
{
  "amount": "5",
}
```

----

## Cancelling a Reservation

### Request
```
POST /1.1/me/reservations/32083/cancel
```
```
{
  "token": "VND-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "brandId": 0,
  "reason": ""
}
```

| Property | Description |
| --- | --- |
| `token` | The **token** which was submitted during authentication |
| `brandId` | The brand (app or website) from which the customer is cancelling from |
| `reason` | A text on the customer's reason for cancelling |

### Response

The following example response is a trimmed down version, where only the important information and shown. The staging and production APIs may give more data, but is still using the same structure.

```
{
  "result": {
    "success": true
  }
}
```

----

## Handling Errors
At any endpoint, the platform may respond with an error object. This can be dectected by checking the existence of an error property in the response, e.g. `response.error`.

| Property | Description |
| --- | --- |
| `error` | The error object |

### Error Infromation

| Property | Description |
| --- | --- |
| `code` | The platform error code |
| `message` | The error message that can be displayed to the user |

### Example Error Response
```
{
    "error": {
        "code": "CUST401001",
        "message": "Invalid email/password"
    }
}
```