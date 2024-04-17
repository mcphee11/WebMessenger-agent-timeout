# WebMessenger-agent-timeout

Auto disconnect interactions when conversation idle for x time to stop agents staying busy for to long increasing wait times.

## Step 1

This example uses "Implicit Grant" OAuth2 to your Genesys Cloud ORG environment. The first thing you will need to do is to get a ClientID from your Genesys Cloud ORG with the ability to connect to the API endpoints. This will also need to have a redirect to the Hosting location of the server url for example: "http://localhost:8080/index.html". You will also need add the required "scopes" for the endpoints that you want to use. In this example we are using "conversations".

![](/docs/images/oauth.png?raw=true)

Save this and copy the

    "clientId"

as you will need this later for the authentication.

## Step 2

Create the [Agent Script](https://help.mypurecloud.com/articles/create-script/) This can be editing an existing one you already have or creating a new one. To add the code to your script you will need to put in a "Web Page" object with the below URL.

This is dependent on where you host the webpage of course as well as your own clientId.

    http://localhost:8080/index.html?gc_response=are%20you%20still%20there&gc_responseTime=60&gc_closeTime=160&gc_region=mypurecloud.com.au&gc_clientId=YOUR_CLIENT_ID&gc_conversationId={{Scripter.Interaction ID}}&gc_redirectUrl=http://localhost:8080/index.html

NOTE: I'm in the apse2 region so i have the "mypurecloud.com.au" this may differ in your region. The conversationId is being gathered by the default scripter variable. Make sure the you update the other variables with your data from above eg:

    YOUR_CLIENT_ID

The "gc_response" is a message that will be sent on behalf of the agent after the timer expires with no messages from either the agent or customer. This needs to be formatted as URL Encoded so the "%20" is a SPACE. "gc_responseTime" this is in seconds so 60 = 60sec the same goes for the "gc_closeTime" this is when the interaction will be auto disconnected for the agent. If there is either no wrapup or a wrapup timeout set on the queue this will then auto clear out allowing them to receive more interactions.

![](/docs/images/agentScript.png?raw=true)

In my case I have also made the iFrame only 5x5 Pixels as there is no need for the agent to see or interact with this page. Its only used for the JS with the APIs.

Once you host the html file on a web server this would also be moved from localhost:8080 when this is done remember to also add it as a redirect in your OAuth client you setup earlier.

## Testing

Now your done. Send in a WebMessenger interaction and when you wait without sending or receiving a message on the conversation for the timeouts that you set you will see first the gc_responseTime message first. Then after the gc_closeTime expires the conversation will then be disconnected.

![](/docs/images/response.png?raw=true)

## NOTE:

    This has been built so that by default the agent needs to of sent at least one message into the conversation before the timers will be applied.
