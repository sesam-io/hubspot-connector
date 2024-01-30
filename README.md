# hubspot-connector
An example of a new Sesam component - a connector - to the Hubspot CRM system.

# Custom fields

Pr. now two custom fields are in use: `sesam_org_number_no` and `sesam_org_number_se` for `companies`. These will have to be manually created in each hubspot instance, but once they are they will syncronize as a Norwegian and a Swedish organization number correspondingly. They will also be used as merge criteria for organizations.  

# Associations

The cronological sequence of hacks we have had to do on associations.
 1. Create expected payload for $based_on_comparison with key "associationTypes".
 2. Reconstructing the original structure from "sesam_simpleAssociationTypes", which has been created to simplify model mapping and removing it.
 3. Reshape the payload since its asymmetric for insert/update vs. collect/lookup.
 4. On lookup, find the one relation we are about to update, since all relations between datatypes are returned.
 5. Overwriting the returned "associationTypes" in collect; get rid of the default association that always follows the primary association. This association always has no label and is created by default by HubSpot and has no meaning for us.
 
 
Sequence description on inserts and updates:
- You can only have one label assigned to an association at any given time, unless its an insert, where the label primary is added automatically by HubSpot. So, if you do an insert where you insert a USER_DEFINED label that association will end up with two labels. One being the USER_DEFINED label sent from Sesam and the other the Primay label automatically added by HubSpot. After, if the same entity gets and update where you change the USER_DEFINED label, then only that label will be assigned to that association. As such, the primary label is removed. 

# Webhooks

To test webhook events you need to set the `hubspot_webhook_dataset` environment variable to point to a dataset with all the events you want to test. 

You can use this pipe in Sesam to receive all the events for your Hubspot application registration. You can add the following pipe to your subscription and set `hubspot_webhook_dataset` to `hubspot-webhook-event`.
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
