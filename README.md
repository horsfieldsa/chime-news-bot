# chime-news-bot

![Alt text](docs/diagram.png?raw=true "Diagram")

## Overview

This solution deploys a simple serverless process for getting articles from RSS feeds and posting them to an Amazon Chime chat channel using a web hook.

### Planned Improvements

1. Break GetItems Function into State Machine (AWS Step Functions).
2. Add Sentiment/Key Phrase Detection.
3. Daily Roll-Up of Activity.
4. Notification Improvements (New Versions, New Feeds, Etc)
5. Better handling of feeds to load when deployed (config file perhaps).

## Prerequisites

### Create Chime Room and WebHook

https://docs.aws.amazon.com/chime/latest/ug/webhooks.html

### Update Feeds to Load

Update functions/load_feeds/app.py

NOTE: You can also directly update the Feeds DynamoDB table after deployment to include additional feeds.

```
feeds_to_load = [
    {
        'title': 'AWS Updates',
        'url': 'http://aws.amazon.com/new/feed/',
        'icon': ':cloud:'
    },
    {
        'title': title,  # Feed Title Displayed in Chat
        'url': 'url',    # URL of RSS Feed (Only RSSV2 Currently Supported)
        'icon': ':icon:' # Corresponds to Chime Emoticons
    },
    # More Feeds Below Here
]
```

## Deploy

### Install SAM CLI

Install the AWS Serverless Application Model CLI - https://aws.amazon.com/serverless/sam/
Configure your local AWS Credentials (aws configure).
Create an S3 bucket to store the packaged code and replace S3_BUCKET_TO_STAGE_CODE with the name of your bucket in the comamands below.

### Build

```
sam build --use-container
```

### Package

Replace [YOUR_S3_BUCKET] with the bucket you'll use to hold deployment artifacts.

```
sam package \
    --output-template-file packaged.yaml \
    --s3-bucket [YOUR_S3_BUCKET]
```

### Deploy

Replace [YOUR_WEBHOOK_URL] with the webhook you created for your Chime chat room.

```
sam deploy \
    --template-file packaged.yaml \
    --stack-name chime-news-feed \
    --capabilities CAPABILITY_IAM \
    --parameter-overrides \
    ChimeWebHookURL=[YOUR_WEBHOOK_URL]
```

### Post Deploy

Update the Feeds DynamoDB table with the RSS feeds you want to monitor. Follow the pattern of the default item, which can be deleted if needed.
