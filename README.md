# hubspot-connector
An example of a new Sesam component - a connector - to the Hubspot CRM system.

# Webhooks

To test webhook events you need to set `webhook_dataset` environment variable to point to a dataset with all the events you want to test. 

You can use this pipe in Sesam to receive all the events for your Hubspot application registration. You can add the following pipe to your subscription and set `webhook_dataset` to `hubspot-webhook-event`.
```
{
  "_id": "hubspot-webhook-event",
  "type": "pipe",
  "source": {
    "type": "http_endpoint"
  },
  "metadata": {
    "$config-group": "hubspot-event"
  },
  "transform": {
    "type": "dtl",
    "rules": {
      "default": [
        ["copy", "*"],
        ["add", "_id",
          ["string", "_S.eventId"]
        ]
      ]
    }
  },
  "compaction": {
    "keep_versions": 0,
    "time_threshold_hours": 168
  },
  "namespaced_identifiers": false
}
```

You can post events to this endpoint, here are some examples you can tweak.

Example change event:
```
{
  "appId": 1305300,
  "attemptNumber": 0,
  "changeSource": "HUBSPOT_INTERNAL",
  "eventId": 2931120501,
  "objectId": 6682673371,
  "occurredAt": 1670945968638,
  "portalId": 26525078,
  "propertyName": "twitterhandle",
  "propertyValue": "avisaGD",
  "sourceId": "CompanyInsightsPropertyMappings",
  "subscriptionId": 1876067,
  "subscriptionType": "company.propertyChange"
}
```

Example creation event:
```
{
  "appId": 1305300,
  "attemptNumber": 0,
  "changeFlag": "CREATED",
  "changeSource": "CRM_UI",
  "eventId": 475842865,
  "objectId": 6682673371,
  "occurredAt": 1670945968441,
  "portalId": 26525078,
  "sourceId": "userId:47812075",
  "subscriptionId": 1876045,
  "subscriptionType": "company.creation"
}
```

Example deletion event:
```
{
  "appId": 1305300,
  "attemptNumber": 5,
  "changeFlag": "DELETED",
  "changeSource": "CRM_UI_BULK_ACTION",
  "eventId": 2737147673,
  "objectId": 6674551525,
  "occurredAt": 1670944855442,
  "portalId": 26525078,
  "sourceId": "userId:47812075",
  "subscriptionId": 1876046,
  "subscriptionType": "company.deletion"
}
```

If you want to test live events from Hubspot you will need an application registration in Hubspot and point the events to `https://<your-service-url>/api/receivers/hubspot-webhook-event/entities`.
