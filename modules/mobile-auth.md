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

<iframe width="560" height="315" src="https://g.twimg.com/dev/flight/2015/keynotes/Flight2015-Hubble_Dalton-Mobile_Auth_Services.pdf" frameborder="0" allowfullscreen></iframe>

<!-- @task, "text" : "Try adding a Fabric single sign-on kit to your app." -->

If you prefer to just read the slides without the video, below are the presenter notes. They are labeled with the slide numbers to help you track the slides.

**Slide 2:**  
Let’s get started. Many of you know that authentication can be one of the most challenging parts of building a mobile app. Everything from making login easy, to verifying emails and phone numbers, to session systems and key rotation can be tricky.

I work on the Fabric engineering team and today I’d like to *show* you how Twitter and Digits can be used to *build* authentication services to power your mobile applications.

**Slide 3:**  
First, I’ll summarize when apps choose to authenticate to their own API  
Then, I’ll review what Fabric kits provide to you, out of the box.  
Finally I’ll dive in, to discuss building authentication services for your apps and show some example code along the way.  

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

**Slide 17:**

**Slide 18:**

**Slide 19:**

**Slide 20:**

**Slide 21:**

**Slide 22:**

**Slide 23:**

**Slide 24:**

**Slide 25:**

**Slide 26:**

**Slide 27:**

**Slide 28:**

**Slide 29:**

**Slide 30:**

**Slide 31:**

**Slide 32:**

**Slide 33:**

**Slide 34:**

**Slide 35:**

**Slide 36:**

**Slide 37:**

**Slide 38:**

**Slide 39:**
