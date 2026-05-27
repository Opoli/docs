# Prime Time Shuttle Tracking API Documentation

These are the hosts where requests should be made.
The routes indicated on each request should be appended to either of these URLs.

| Property | Description |
| --- | --- |
| Staging/Test | `https://staging-gateway.opoli.com` |
| Production | `https://gateway.opoli.com` |

----

## Endpoints

### MTech Webhook
Send reservation updates to the Prime Time Shuttle system.
```
POST /2.0/webhooks/mtech/<code>/<code>
```
#### Request
```json
{
  "resID": 12345,
  "eventType": "Assigned",
  "deliveryStatus": "OnTheWay",
  "driverID": 67890,
  "driverName": "Test Driver",
  "driverPhone": "",
  "vehiclePlate": "",
  "vehiclePlateNumber": "",
  "vehiclePlateState": "",
  "vehicleMake": "",
  "vehicleModel": "",
  "vehicleColor": ""
}
```
#### Response
```json
{
  "success": true
}
```
