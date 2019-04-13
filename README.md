# chime-news-bot

Posts items from RSS feeds to a Chime chat channel

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

Update the Feeds DynamoDB table with the RSS feeds you want to monitor. The tables take two fields, name and icon where icon is a Chime emoticon, ie: :shoe: