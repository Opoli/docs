# Prime Time Shuttle Tracking API Documentation

These are the base URLs where requests should be made. The routes indicated on each request should be appended to either of these URLs.

| Property | Description |
| --- | --- |
| Staging | `https://staging-gateway.opoli.com` |
| Production | `https://gateway.opoli.com` |

For example, if the document says `GET /1.1/reservations`, the full URL will be:
```
GET https://gateway.opoli.com/1.1/reservations
```

----

## Authentication
The Prime Time Shuttle API endpoints necessary to track a reservation, is accessible using a `token` provided for each client application. It should be passed as a query to the `GET` endpoints below.

----

## Endpoints

### Reservation Information
```
GET /1.1/reservations/0000000
?token=abcdef123456
```
#### Response
```json
{
  "schema": "rsvp:full",
  "ID": 0000000,
  "consumer": {
    "ID": 000000,
    "firstName": "",
    "lastName": "",
    "email": "",
    "avatarUrl": "",
    "phones": [
      ...
    ]
  },
  "provider": {
    "ID": 000000,
    "firstName": "",
    "lastName": "",
    "email": "",
    "displayPhone": "",
    "avatarUrl": "",
    "rating": null,
    "businessName": "",
    "driverLicenseNumber": "",
    "phones": [
      ...
    ]
  },
  "vehicle": {
    "model": "",
    "manufacturer": "",
    "plate": "",
    "photo": ""
  },
  "pickupDate": "2023-01-16 16:45:06 -0800",
  "pickupLocation": {
    "addressLine1": "1",
    "addressLine2": "World Way",
    "zip": "",
    "state": "CA",
    "city": "Los Angeles",
    "country": null,
    "lat": 33.9415889,
    "lng": -118.40852999999998,
    ...
  },
  "dropoffLocation": {
    "addressLine1": "West Imperial Highway",
    "addressLine2": "4520",
    "zip": "90304",
    "state": "CA",
    "city": "Inglewood",
    "country": null,
    "lat": 33.9306876,
    "lng": -118.35555440000002,
    ...
  },
  "guest": {
    "firstName": "",
    "lastName": "",
    "phone": ""
  },
  "amount": 20,
  "originalPrice": 20,
  "tipAmount": 0,
  "pickupFee": 0,
  "airportFee": 2,
  "gasFee": 0,
  "travelInsurance": 3,
  "total": "20.00",
  "distance": 3.9,
  "etaInSec": 819,
  "passengers": 1,
  "luggage": 1,
  "airline": "Sichuan Airlines",
  "airlineCode": "3U",
  "terminal": "B",
  "flightNumber": "123",
  "pickupType": "MeetAndGreet",
  "dispatchStatus": "upcoming",
  "serviceType": {
    "id": 107,
    "name": "",
    "label": "",
    "image": "",
    "licenseType": "tnc",
    ...
  },
  "brandID": 12,
  "ada": {
    "wheelChairLift": 0,
    "serviceDog": 0
  },
  ...
}
```

### Driver Tracking
```
GET /1.1/reservations/0000000/tracking
?token=abcdef123456
```

#### Response
```json
{
  "list": [
    {
      "lat": 33.9307,
      "lng": -118.3558,
      "heading": 135,
      ...
    }
  ]
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
