# SendGrid setup

## Getting SendGrid

We'll be using Twilio's Sendgrid application as one of our 'external' services.  SendGrid is a good choice for this because it has a free tier, and a well-documented an API that we'll call.  

In addition, SendGrid has the ability to call a webhook that we'll provide.  On that webhook SendGrid sends additional information about how the recipient of the email reacted to the email.  Whether the email bounced or was received, whether the recipient opened the email or clicked on a link that we provided in the email.

Create a new account by visiting the SendGrid site and signing up.  We won't spend a lot of time setting up the account - the instructions provided by SendGrid are easy to understand. 

```
https://docs.sendgrid.com/
```

{% hint style="info" %}
 Read the excellent SendGrid documentation to get your account set up
{% endhint %}

Once we've created our account we'll need to create the API key that we'll use to authenticate with SendGrid.  This process is documented in the docs here:

```text
https://docs.sendgrid.com/ui/account-and-settings/api-keys
```

Log into your SendGrid account and add the API key per the instructions provided there

![](../.gitbook/assets/sendgrid-personal-page-3.png)

{% page-ref page="untitled-7.md" %}





