---
layout : post
title : "Integrating RASA chat bot with Slack"
categories : [rasa, slack]
published : true
---

> [**Shrijan Choudhary >>**](https://medium.com/analytics-vidhya/integrating-your-rasa-chat-bot-with-slack-c18bffc6018b)
> I will try to maintain the brevity of the article without missing on any crucial step and I guarantee that if you follow the steps, you will have a working slack bot for yourself.

### Creating a slack app

* To create the app go to [Your Apps](https://api.slack.com/apps) and click on Create New App.
* `Basic information` : Scroll down to reach the `Display Information` filling the name, description, a photo and a background colour for your bot.

![rasa-connector-slack-00](/assets/img/blog/rasa-connector-slack-00.png)

![rasa-connector-slack-01](/assets/img/blog/rasa-connector-slack-01.png)


### Authorization and Permission for your BOT
> add the permissions for your bot so that it can read and write text messages with anyone who wants to interact with your bot.

* Head over to `OAuth & Permissions` and scroll down to `Scopes`. To add the permissions

	* `chat:write`
	* `im:read`
	* `im:write`
	* `im:history`
	* `mpim:history`
	* `app_mentions:read`
	* `channels:history`
	* `groups:history`
	* `reactions:write`

![rasa-connector-slack-02](/assets/img/blog/rasa-connector-slack-02.png)

![rasa-connector-slack-03](/assets/img/blog/rasa-connector-slack-03.png)

* Scroll up and click `Install App` option to install the app on slack with these permissions.


### Setting up the RASA credentials.yml

* Copy the `Bot User OAuth Token` and paste in the `credentials.yml` page of your RASA project

![rasa-connector-slack-04](/assets/img/blog/rasa-connector-slack-04.png)

* Head over to `Basic Information` to gather the `Signing Secret`.

![rasa-connector-slack-05](/assets/img/blog/rasa-connector-slack-05.png)

* Keep the `slack_channel` parameter empty.

![rasa-connector-slack-06](/assets/img/blog/rasa-connector-slack-06.png)


### Running scripts for actions, ngrok and shell.
1. Running the action script

	Un-comment the `action_endpoint` tag in the `endpoints.yml` 

	![rasa-connector-slack-07](/assets/img/blog/rasa-connector-slack-07.png)


	```bash
	$ rasa run actions
	```

2.  Start your RASA shell

	Make sure you are on the RASA project directory and the virtual environment of project

	```bash
	$ rasa run --port 5002 \
	--connector slack \
	--credentials credentials.yml \
	--endpoints endpoints.yml \
	--log-file out.log \
	--cors * \
	--enable-api \
	--debug 
	```

3. Start ngrok on the `5002` port

	```bash
	$ ngrok http 5002
	```

	![rasa-connector-slack-08](/assets/img/blog/rasa-connector-slack-08.png)

4. Copy the URL and append `webhooks/slack/webhook` to get something like below:

	```
	https://b90b-202-44-192-214.ngrok.io/webhooks/slack/webhook
	```

### Final configurations on Slack

1. Go to the Event Subscription section and then toggle the Enable Events switch on the page to ensure it is **ON**.

2. Paste the above copied `ngrok URL` to **Request URL**

3. Waiting for a **“Verified”** remarks against your request URL section.

	![rasa-connector-slack-09](/assets/img/blog/rasa-connector-slack-09.png)

4. Go to `Subscribe to bot events` and add the following events

	* message.channels
   * message.groups
   * message.im
   * message.mpim

	![rasa-connector-slack-10](/assets/img/blog/rasa-connector-slack-10.png)


5. Save the changes and reinstall the app. 


> To start chatting with your bot, go to https://slack.com/intl/en-in/ and click Launch Slack to go to your workspace. Select your bot from the left hand side menu and start chatting! :)

![rasa-connector-slack-11](/assets/img/blog/rasa-connector-slack-11.png)

### Reference
* [Integrating your RASA chat bot with Slack](https://medium.com/analytics-vidhya/integrating-your-rasa-chat-bot-with-slack-c18bffc6018b)

* [RASA Connecting a bot to Slack](https://rasa.com/docs/rasa/connectors/slack/)
