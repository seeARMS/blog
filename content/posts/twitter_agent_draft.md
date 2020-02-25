+++
draft = true
example = ""

+++

## Setting up the Twitter agent

To use Twitter, we need to create an OAuth application and provide credentials to Huginn.

Log into the [Twitter developer website](https://developer.twitter.com/en/apps "Twitter Developers") and 'Create an app'. It's a lightweight process, but the key thing to provide is the **Callback URL**. This needs to be set to `http://<your_ip>:3000/auth/twitter/callback` in order to work with Huginn.

After submitting the Twitter app (and ideally getting an instant approval), you'll receive two tokens: the `API key` and the `API secret key`. Copy these, and head back over to GCP. Edit the VM you previously deployed, and under _Advanced container options_, add two new Environment variables: **TWITTER_OAUTH_KEY** and **TWITTER_OAUTH_SECRET**.

After saving, Huginn should restart. Log in, navigate to /services, and you should see an 'Authenticate with Twitter' button now!

![](/uploads/twitter_authenticate.png)

Authenticate, and you can begin using Twitter intelligence inside Huginn via the Twitter agent. For any issues encountered, check out the [Github page on OAuth applications](https://github.com/huginn/huginn/wiki/Configuring-OAuth-applications#twitter "Github OAuth").