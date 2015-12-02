<!--
{
"name" : "mobile-auth",
"version" : "0.0.1",
"title" : "Mobile Auth Services by Dalton Hubble",
"description" : "We'll discuss how Twitter and Digits can authenticate your app users to your backend, design a small Go auth service for a Twitter/Digits app, and use containers to deploy it.",
"freshnessDate" : 2015-11-01,
"homepage" : "https://dev.twitter.com/flight/2015",
"canonicalSource" : "https://dev.twitter.com/flight/2015",
"license" : "All Rights Reserved"
}
-->

<!-- @section -->

## Mobile Auth Services by Dalton Hubble

The best mobile apps leverage remote services to store rich personalized data, perform secure transactions, sync accounts, and more. Fabric's single sign-on kits let your app access the Twitter and Digits APIs, but they can do more. In this talk, we'll discuss how Twitter and Digits can authenticate your app users to your backend, design a small Go auth service for a Twitter/Digits app, and use containers to deploy it.

You can follow along with the slides as you watch the talk.

<iframe width="560" height="315" src="https://www.youtube.com/embed/IsFx3yXIhdM" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="https://outlearn-content.s3.amazonaws.com/fabric/DaltonHubbleWithNotes.pdf" frameborder="0" allowfullscreen></iframe>

<!-- @task, "text" : "Try adding a Fabric single sign-on kit to your app." -->

If you prefer to just read the slides without the video, below are the presenter notes.

**Slide 2:**  
Let’s get started. Many of you know that authentication can be one of the most challenging parts of building a mobile app. Everything from making login easy, to verifying emails and phone numbers, to session systems and key rotation can be tricky.

I work on the Fabric engineering team and today I’d like to *show* you how Twitter and Digits can be used to *build* authentication services to power your mobile applications.

**Slide 3:**  
- First, I’ll summarize when apps choose to authenticate to their own API  
- Then, I’ll review what Fabric kits provide to you, out of the box.  
- Finally I’ll dive in, to discuss building authentication services for your apps and show some example code along the way.  

**Slide 4:**  
First off, let talk about apps. Many of the best apps offer logged-in functionality  

* they store and manage user content
* they provide personalized experiences
* they sync settings across different platforms
* and they perform sensitive operations like charging a linked credit card

**Slide 5:**  
Apps that operate or store data on behalf of users, usually need to authenticate to their own backing APIs and services.

**Slide 6:**  
Twitter and Digits have a lot to offer as identity providers to help you build apps like this.  

* First, they’re convenient for your users - a large number of people have phone numbers or Twitter accounts. (Digits also gives users optional 2 factor auth they can turn on).
* For you, Twitter and Digits are both highly scalable and reliable. They can simplify your stack
    * by handling resets and confirmations
    * Twitter suspends abusive users and spammers as well.
* Your *app* can also obtain verified email addresses and verified phone numbers

**Slide 7:**  
Last year, we launched the Twitter and Digits Kits for Android and iOS to make it even easier to integrate these identity providers into your apps. The kits include embeddable UI buttons like these ^ and callbacks or completion blocks which perform…

**Slide 8:**  
Single Sign-On on the device takes a user through the OAuth1 Authorization flow

**Slide 9:**  
It handles the complexities of

* obtaining request tokens
* prompting users to grant your app access to their account
* getting the Twitter or Digits access token
* and signing each of these request made to the API

It saves you a lot of work.

**Slide 10:**  
Let’s take a look at what that flow looks like to one of your users.

* An app can get a “Login with Twitter” button, which uses the installed Twitter App. Prompted to login with current account or switch accounts. If Twitter App not installed, flow falls back to web view
* The “Login with Digits” button is similar. The user grants access by entering their phone number and receives an SMS confirmation code to confirm the phone number.

So very quickly, your mobile app can add this flow to login a user to Twitter or Digits.

**Slide 11:**  
So the SDK gives apps easy access to the Twitter/Digits APIs, but what about these apps that authenticate to their own API?

For example, we might have a Note App (with Android and iOS clients) which stores and syncs *a users* personal notes. It would be **really** fantastic to leverage the features and flow of Twitter and Digits login, but communicate in a secure way with the Note App backend

**Slide 13:**  
It all starts with the OAuth1 Token Credentials, or Access Token.

**Slide 14:**  
Both Twitter and Digits use OAuth1 and their credentials consist of a token and secret string.

In an example auth service in Go, I’ll define a struct with string fields to represent this.

**Slide 15:**  
In the mobile app, its easy to grab the token after Single Sign-On completes.

On Android, get the token from the Twitter or Digits Session in the login success callback.

**Slide 16:**  
On iOS, get the token fields from the Twitter and Digits Session in the login completion block.

**Slide 17:**  
What would a note app want these access token for?

Well, access tokens are valuable because they act as a verifiable proof (pause) of a user’s identity.

**Slide 18:**  
So what that means is that if the mobile client can securely send the token to its own auth service, Twitter and Digits provide endpoints to

* validate those tokens
* respond with the corresponding Twitter User or Digits Account

**Slide 19:**  
To verify a Twitter Token, the *server* can make an OAuth1 Authorized Request, using the Token Secret and known Twitter Application Secret.

The verification endpoint responds with the Twitter User, showing that the token is tied to the Note App and the Twitter identity.

**Slide 20:**  
To verify a Digits Token, the server can make a similar OAuth1 Authorized Request to receive the Digits Account tried to the token and application.

This concept of server verification of tokens passed from a mobile client is very important. Let’s go through an example of a tiny authentication service written in Go.

**Slide 21:**  
To do this in Go, I’ll show a couple of ContextHandlers.

* All you need to know about ContextHandlers is that they’re effectively **functions** that **receive requests** and **write responses**
* chained - They tend to be **small** and single purpose so they can be **chained** together for more complex operations.
* ctx - The context is just an argument passed between chained handers to propagate values btw handlers.

**Slide 22:**  
The very first handler is responsible for reading a POSTed Twitter or Digits token and secret from a request.

If that succeeds, it writes the token and secret to the context and calls the next handler, which is called success in this example.

**Slide 23 & 24:**  
The next handler does the verification we talked about.

* Building on the previous handler, it reads the token and secret back from the context.
* Token used to create Oauth1 HTTP client which will sign any requests made. Twitter and Digits API clients take this client and allow requests to be made to the Twitter and Digits verification endpoints.
* Like before, if that succeeds, the Twitter/Digits User is written to the context for later handlers.

**Slide 25:**  
Now that you can convert a Token -> Twitter/Digits User, the next step

* Be careful when deciding how to do that. The …. are not safe for this mapping.

**Slide 26:**  

* Instead, store the Twitter ID or Digits ID in your User database models. They’re guaranteed to be stable over time.

**Slide 27:**  
So, a later handler can get the Twitter User from the context and use the ID to lookup the associated App User.

**Slide 28:**  
Do the same thing for Digits login, lookup the Note App user by the Digits User ID.

At this point, I’d like to call attention to a few mistakes we’ve seen developers make.

**Slide 29:**  
At this point, I’d like to call attention to a few mistakes we’ve seen developers make.

First, *use* the verification response you receive. Just checking that Twitter or Digits return a OK 200 HTTP response is not enough - it just proves the token is a valid token, it doesn’t tell you anything about the identity tied to the token.

**Slide 30:**  
This mistake usually boils down to something like this.

A Twitter or Digits token is send to the auth service, the verification endpoint returns a 200, and gets the Note User ID from the POST.

**Slide 31:**  
The mistake is that any id could be passed along with the valid token.

* Don’t trust the client. Don’t take shortcuts. Derive the Twitter/Digits User from the Token, then map to the corresponding User of your app.
* You may pass additional info for prefetching, just be sure to have safeguards in place to prevent engineers from trusting data that data for authentication

**Slide 32:**  

* HTTPS - Use HTTPS for your auth service endpoints. Sensitive data. Consumer key/secret can be extracted. If an attacker can sniff…
* Any secret embedded in your mobile clients to access your API can be extracted, no matter how obscure. The Auth Service will accept valid tokens from any client including curl, so never assume your API is public to just your apps.
* Digits Web Login Verifications

**Slide 33:**  
Now that you can map Mobile Tokens -> Twitter/Digits User -> Note App User, you could respond to API requests like GET Notes by just attaching the token in requests

But you probably have a few other requirements for your API

**Slide 34:**  

* Latency
* Expiration - Next up, Twitter and Digits Tokens don’t expire
* Revocation - you as an application developer don’t have an easy way to revoke them
* Metadata - finally, you may wish to store some custom metadata in tokens for your API

What this is leading up to is simply to have the auth service grant a session of some form to the app.

**Slide 35:**  
Let’s walk through how this might work.

* First, we separate the auth service and note api service
* Issue *some* form of session, possibly integrate with existing session systems. Completely up to you.
* In this example I’ll use a JSON Web Token, which is a signed token.
* Signing key rotation policies
* JSON Web Tokens in this example have a few nice properties 

**Slide 36:**  

- claims - they support claims data which let you store JSON data in the token
- client-side session - This lets you use them like client-side or server-side sessions. Might include just a user_id or maybe a session_id which corresponds to a backend session store, or some combination
- optionally encrypted
- Composer of 3 parts, a header, payload, and signature.
- Each part is base64 encoded into a string which gets concatenated

**Slide 37:**  

- That string can easily be used as a Authorization header and persisted on the mobile client as long as it is valid.
- Great libraries exist for creating JWTs.

Finally, I want to show an example of the Note App we’ve discussed.

**Slide 38:**  

- Using TwitterKit and Digits on your mobile clients
- Designing an authentication service which performs the right verifications, to accept Twitter/Digits tokens and exchanges them for User sessions
- Combined with TwitterKit and Digits on the mobile clients for Android and iOS and microservices for storing Accounts and Notes, you might get an app like this.

**Slide 39:**  

- Link to Digits Web login additional verifications which are required.
- All context handlers shown are drawn from the gologin library
- A popular JWT library
