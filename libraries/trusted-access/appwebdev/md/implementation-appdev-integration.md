### Integration with the NDI App
<br/>

You can initiate authentication by launching the NDI App with the required parameters. This will either be done with Universal Links (iOS)/ App Links (Android) or custom URI schemes.

#### iOS (9 and above only)
<br/>

1.	You need to setup universal links.  Custom URI schemes will not be supported for iOS to provide better security.  You can refer to https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html.  Alternatively, if you wish to use a third party solution for deep linking such as Branch and Firebase Dynamic Links, skip to step 4.

2.	First, you need to create an association from a website you own and your app. For example, you can host and serve it as application/json at 
https://example.com/apple-app-site-association. You would need the app ID prefix/team ID and your bundle ID for appID. It should look something like this:

````
[{
  "applinks": {
    apps": [],
details": [{
  "appID": "92W9FM4VJN.com.example.app",
      "paths": [ "*" ]
    }]
  }
}]
````

3. You also need to enable the associated domains capability in XCode.  You can do this by going to the Capabilities tab in XCode, turn it on and add an entry with your website with the prefix of <b>applinks</b>: For example, applinks:example.com.

4. If you are not using a third party solution go to the next step. Otherwise please ensure that you enable universal links.

5. Then you need a universal link that the NDI app can return the authorization code to. For example, if your link is https://example.com/auth then the NDI app will return the authorization code by launching your app with https://example.com/auth?state={state}&code={code}.

6. When the NDI app launches your app, you would need to retrieve and handle the corresponding data. In particular, you should add <b>application:continueUserActivity:restorationHandler</b>: in your Application Delegate.

7. You can then initiate authentication by opening the NDI App’s universal link with openURL: 
   
   ````
   
   https://sandbox.ndi.io/auth?client_id={client_id}&scope={scope}&redirect_uri={redirect_uri}&nonce={nonce}&state={state}&code_challenge={challenge}&code_challenge_method={challenge_method}
   
   ````

    | <b>Paremeters | <b>Remarks |
    | ------ | ----------- |
    | client_id <b>(required) | The client ID for your app 
    | redirect_uri | The universal link that the NDI App would return the authorization code to 
    | nonce <b>(required)	| A one-time randomly generated unique string which will be included in the ID token as a means to prevent replay attacks 
    | state | A one-time randomly generated unique string which will be returned along with the authorization code as a means to prevent CSRF and can also be used to maintain state
    | code_challenge_method <b>(required) | The method used for transforming the code verifier. The only supported values are "plain" or "S256" 
    | code_challenge <b>(required) | The code challenge derived from the code verifier using the transformation specified by the code challenge method to enforce PKCE. 

8.	The NDI App will then make the relevant requests to the ASP to initiate the user authentication. On completion of user authentication, the NDI App will call your redirect URI accordingly.  For example, suppose your redirect URI is https://example.com/redirect:

- 	User authentication succeeded, your Mobile App will be called with the authorization code as follows: https://example.com/redirect?state={state}&code={code}

- User authentication failed, your Mobile App will be called with an error as follows: https://example.com/redirect?error=access_denied&error_reason=user_denied&error_description=Permissions+error

9.	With the received authorization code, your Mobile App can now call the ASP token endpoint to obtain the ID token and access token accordingly.

#### Android
<br/>

1.	You need to setup a custom URI scheme and app links for your app. You can refer to https://developer.android.com/training/app-links/.  Alternatively, if you wish to use a third party solution for deep linking such as Branch and Firebase Dynamic Links, skip to step 5.
2.	First, setup a custom URI scheme for Android by modifying your <b>AndroidManifest.xml</b>. 
Set the <b>scheme</b> to your application ID and make sure you set the <b>host</b>.  You should end up with something like this:
````
<intent-filter android:label="@string/filter_view_auth ">
     <action android:name="android.intent.action.VIEW" />
     <category android:name="android.intent.category.DEFAULT" />
     <category android:name="android.intent.category.BROWSABLE" />
     <!-- Accepts URIs that begin with "com.example.app://ndi” ->
     <data android:scheme="com.example.app"
           android:host="ndi" />
</intent-filter>
````

3.	Setup app links in your <b>AndroidManifest.xml</b>. 
Set the <b>scheme</b> to <b>"https"</b> and the host with a domain you own.  Make sure you can serve content on it.  You should end up with something like this:

   ```` 
    <intent-filter android:label="@string/filter_view_auth" android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Accepts URIs that begin with "https://example.com” ->
        <data android:scheme="https"
              android:host="example.com" />
    </intent-filter>
   ````

4. You need to [declare the host’s association with the app](https://developer.android.com/training/app-links/verify-site-associations#web-assoc) by creating a Digital Asset Links JSON file. 
For example, you may host it at 
https://example.com/.well-known/assetlinks.json.  You would need the application ID for package_name and the SHA256 fingerprints of your signing certificate for <b>sha256_cert_fingerprints</b>.  It should look something like this:
````
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.example.app",
    "sha256_cert_fingerprints": ["14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"]
  }
}]
````

5.	If you are not using a third party solution go to the next step.  Otherwise please ensure that you create a custom URI scheme with your application ID and enable app links in your third party solution.

6.	You need a redirect URI for both your custom URI scheme and app links that the NDI App can return the authorization code to.  For example, if your redirect URI is {scheme}://{host}<b>/auth</b> then the NDI App will return the authorization code by launching your app using an Intent with the action <b>ACTION_VIEW</b> and data with {scheme}://{host}/<b>auth?state={state}&code={code}</b>.

7.	You can then initiate authentication by launching the NDI app ([like how you load a web URL](https://developer.android.com/guide/components/intents-common#ViewUrl)) using an Intent with action <b>ACTION_VIEW</b> with the following data:

- 	Custom URI scheme:

ndi://ndi/auth?client_id={client_id}&scope={scope}
&redirect_uri={redirect_uri}&nonce={nonce}&state={state}&code_challenge={challenge}&code_challenge_method={challenge_method}

- 	App Links:

https://sandbox.ndi.io/auth?client_id={client_id}&scope={scope}&redirect_uri={redirect_uri}&nonce={nonce}&state={state}&code_challenge={challenge}&code_challenge_method={challenge_method}

You should launch using an app link if the user has an Android version of 6.0 and above otherwise you should fall back to the custom URI scheme.

| Parameters | Description |
| ------ | ----------- |
| client_id <b>(required) | The client ID for your application.
| redirect_uri | The URI that the NDI app would return the authorization code depending if you are using a custom URI scheme or app link.
| nonce <b>(required) | A randomly generated unique string which will be included in the ID token as a means to prevent replay attacks.
| state | A randomly generated unique string which will be returned along with the authorization code as a means to prevent CSRF and can also be used to maintain state.
| code_challenge_method <b>(required) | The method used for transforming the code verifier. The only supported values are "plain" or "S256".
| code_challenge <b>(required) | The code challenge derived from the code verifier using the transformation specified by the code challenge method to enforce PKCE.
8.	The NDI App will then make the relevant requests to the ASP to initiate the user authentication. On completion of user authentication, the NDI App will call your redirect URI accordingly.  For example, suppose your redirect URI is https://example.com/redirect:

  -	  User authentication succeeded, your Mobile App will be called with the authorization code as follows: https://example.com/redirect?state={state}&code={code}

  - User authentication failed, your Mobile App will be called with an error as follows: https://example.com/redirect?error=access_denied&error_reason=user_denied&error_description=Permissions+error
9.	With the received authorization code, your Mobile App can now call the ASP token endpoint to obtain the ID token and access token accordingly.