# Opoli API Documentation

----

## Authentication
### Request

| Property | Description |
| --- | --- |
| `email` | The client application email |
| `password` | The client application password |
| `device` | The custom access token that will be used on further requests to the platform. Starts with `VND-`. The succeeding `x` characters is a uuid4 string. Client applications generate this. |
| `deviceType` | Possible values: `android` / `ios` / `ww` |

```
{
  "email": "",
  "password": "",
  "device": "VND-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "deviceType": ""
}
```

### Response
If the response contains an ID, the authentication  is successful, and the `device` string in the request can now be used in further requests.

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

----

## Booking a Reservation



----

## Retrieving Reservation Information

----

## Updating a Reservation


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