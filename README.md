# hubspot-connector
An example of a new Sesam component - a connector - to the Hubspot CRM system.

# Webhooks

Add the following pipe to post webhook events during development:
```
{
  "_id": "hubspot-webhook-event-18JWxtqA",
  "type": "pipe",
  "source": {
    "type": "http_endpoint"
  },
  "metadata": {
    "test": true
  },
  "namespaced_identifiers": false
}
```
