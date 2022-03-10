# Cloud Pub/Sub Sample

Curl
-
You can use curl to publish to a Pub/Sub topic.

Export the following variables:

```bash
export PROJECT=<your project id>
export TOPIC=<your topic> 
``` 

First you have to create service account:
```bash
gcloud iam service-accounts create curl-publisher
```
Next set role pubsub.publisher for this service-account:
```bash
gcloud projects add-iam-policy-binding $PROJECT --member=serviceAccount:curl-publisher@$PROJECT.iam.gserviceaccount.com --role=roles/pubsub.publisher
```

Get access token:
```bash
export ACCESS_TOKEN=`gcloud auth application-default print-access-token`
```
Create topic:


```bash
curl -H "Authorization: Bearer $ACCESS_TOKEN" -X PUT https://pubsub.googleapis.com/v1/projects/$PROJECT/topics/$TOPIC
```

Create subscription:
```bash
echo {\'topic\':\'projects/$PROJECT/topics/$TOPIC\'} > /tmp/data; curl -H 'content-type: application/json' -H "Authorization: Bearer $ACCESS_TOKEN" -X PUT -d @/tmp/data https://pubsub.googleapis.com/v1/projects/$PROJECT/subscriptions/my-sub
```

Publish message:
```bash
export MESSAGE=`echo This is my message to you-ou-ou | base64`; echo "{'messages': [{'data': '$MESSAGE'}]}" > /tmp/data; curl -H 'content-type: application/json' -H "Authorization: Bearer $ACCESS_TOKEN" -X POST --data @/tmp/data https://pubsub.googleapis.com/v1/projects/$PROJECT/topics/$TOPIC:publish
```

Pull message:
```bash
curl -H 'content-type: application/json' -H "Authorization: Bearer $ACCESS_TOKEN" -X POST -d '{"returnImmediately":"false", "maxMessages":"1"}' https://pubsub.googleapis.com/v1/projects/$PROJECT/subscriptions/my-sub:pull
```

Acknowledge message:

```bash
export ACK_ID=<the message ack_id>
curl -H 'content-type: application/json' -H "Authorization: Bearer $ACCESS_TOKEN" -X POST -d '{"ackIds": ["$ACK_ID"]}' https://pubsub.googleapis.com/v1/projects/$PROJECT/subscriptions/my-sub:acknowledge
```

Delete topic:
```bash
curl -H "Authorization: Bearer $ACCESS_TOKEN" -X DELETE https://pubsub.googleapis.com/v1/projects/$PROJECT/topics/$TOPIC
``` 

List topics:
```bash
curl -H "Authorization: Bearer $ACCESS_TOKEN" https://pubsub.googleapis.com/v1/projects/$PROJECT/topics

```