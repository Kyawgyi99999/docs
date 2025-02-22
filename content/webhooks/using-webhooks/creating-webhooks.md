---
title: Creating webhooks
intro: 'Learn to build a webhook, choosing the events your webhook will listen for on {% data variables.product.prodname_dotcom %} and how to set up a server to receive and manage the webhook payload.'
redirect_from:
  - /webhooks/creating
  - /developers/webhooks-and-events/creating-webhooks
  - /developers/webhooks-and-events/webhooks/creating-webhooks
  - /webhooks-and-events/webhooks/creating-webhooks
  - /webhooks/webhooks/creating-webhooks
  - /webhooks/creating-webhooks
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
  ghec: '*'
topics:
  - Webhooks
---
Now that we understand [the basics of webhooks][webhooks-overview], let's go through the process of building out our own webhook-powered integration. In this tutorial, we'll create a repository webhook that will be responsible for listing out how popular our repository is, based on the number of issues it receives per day.

Creating a webhook is a two-step process. You'll first need to set up what events your webhook should listen to. After that, you'll set up your server to receive and manage the payload.

{% data reusables.webhooks.webhooks-rest-api-links %}

## Exposing localhost to the internet

For the purposes of this tutorial, we're going to use a local server to receive webhook events from {% data variables.product.prodname_dotcom %}.

First of all, we need to expose our local development environment to the internet so {% data variables.product.prodname_dotcom %} can deliver events. We'll use [`ngrok`](https://ngrok.com) to do this.

{% ifversion cli-webhook-forwarding %}
{% note %}

**Note:** Alternatively, you can use webhook forwarding to set up your local environment to receive webhooks. For more information, see "[AUTOTITLE](/webhooks-and-events/webhooks/receiving-webhooks-with-the-github-cli)."

{% endnote %}
{% endif %}

`ngrok` is available, free of charge, for all major operating systems. For more information, see [the `ngrok` download page](https://ngrok.com/download).

After installing `ngrok`, you can expose your localhost by running `./ngrok http 4567` on the command line. `4567` is the port number on which our server will listen for messages. You should see a line that looks something like this:

```shell
Forwarding  http://7e9ea9dc.ngrok.io -> 127.0.0.1:4567
```

Make a note of the `*.ngrok.io` URL. We'll use it to set up our webhook.

## Setting up a webhook

You can install webhooks on an organization or on a specific repository.

{% data reusables.organizations.owners-and-admins-can %} manage webhooks for an organization. {% data reusables.organizations.new-org-permissions-more-info %}

To set up a webhook, go to the settings page of your repository or organization. From there, click **Webhooks**, then **Add webhook**.

Alternatively, you can choose to build and manage a webhook [through the Webhooks API][webhook-api].

Webhooks require a few configuration options before you can make use of them. We'll go through each of these settings below.

## Payload URL

{% data reusables.webhooks.webhooks-ipv6 %}

{% data reusables.webhooks.payload_url %}

Since we're developing locally for our tutorial, we'll set it to the `*.ngrok.io` URL, followed by `/payload`. For example, `http://7e9ea9dc.ngrok.io/payload`.

## Content type

{% data reusables.webhooks.content_type %} For this tutorial, the default content type of `application/json` is fine.

## Secret

{% data reusables.webhooks.secret %}

## SSL verification

{% data reusables.webhooks.webhooks_ssl %}

## Active

By default, webhook deliveries are "Active." You can choose to disable the delivery of webhook payloads by deselecting "Active."

## Events

Events are at the core of webhooks. These webhooks fire whenever a certain action is taken on the repository, which your server's payload URL intercepts and acts upon.

A full list of webhook events, and when they execute, can be found in [the webhooks API][hooks-api] reference.

Since our webhook is dealing with issues in a repository, we'll click **Let me select individual events** and then **Issues**. Make sure you select **Active** to receive issue events for triggered webhooks. You can also select all events using the default option.

When you're finished, click **Add webhook**.

Now that you've created the webhook, it's time to set up our local server to test the webhook. Head on over to [Configuring Your Server](/webhooks-and-events/webhooks/configuring-your-server-to-receive-payloads) to learn how to do that.

### Wildcard event

To configure a webhook for all events, use the wildcard (`*`) character to specify the webhook events. When you add the wildcard event, we'll replace any existing events you have configured with the wildcard event and send you payloads for all supported events. You'll also automatically get any new events we might add in the future.

[webhooks-overview]: /webhooks-and-events/webhooks/about-webhooks
[webhook-api]: /rest/repos#hooks
[hooks-api]: /webhooks-and-events/webhooks/about-webhooks#events

### Ping event

{% data reusables.webhooks.ping_short_desc %}

For more information about the `ping` event webhook payload, see the [`ping`](/webhooks-and-events/webhooks/webhook-events-and-payloads#ping) event.
