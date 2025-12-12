## OAuth2.0

- Oauth2.0 is authorization protocol (not authentication)
- It is used to authorized a client (eg. 3rd party application) to access the protected resource on behalf of owner/user.
- Oauth2.0 provide a access-token which is used to access the protected resource.

- Components
1. Resource Owner: Person/system who owns the protected resource.
2. Client: System who need to access the protected resource.
3. Authorization Server: Server who grands the access token to Client on consent of Resource owner.
4. Resource Server: The server which stores the protected resource.


## Flow (Authorization code flow)
Consider an example txteditor.com which is a online text editor. It enable us to edit google drive text file online.

- You visit txteditor.com and login.
- Click edit a document on google drive.
- It redirect to Google Authorization server
```sh
https://accounts.google.com/o/oauth2/v2/auth
  ?client_id=YOUR_CLIENT_ID
  &redirect_uri=https://txteditor.com/callback
  &response_type=code
  &scope=https://www.googleapis.com/auth/drive.file
```
- Authenticate at google (UI).
- Provide consent on the listed scope (UI).
- Google Authorization server redirect back to txteditor.com with authorization token
```https://txteditor.com/callback?code=4f0AdQt8...```
- The txtedtior.com (bakend server) calls the Authrozation Server to get access token in exchange of auth token and client secret
```sh
POST https://oauth2.googleapis.com/token
{
  "code": "4f0AdQt8...",
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_SECRET",
  "redirect_uri": "https://txteditor.com/callback",
  "grant_type": "authorization_code"
}
# Response
{
  "access_token": "ya29.a0Af...",
  "expires_in": 3599,
  "scope": "https://www.googleapis.com/auth/drive.file",
  "token_type": "Bearer"
}
```
- the txteditor.com now can access the files using the access token
```sh
GET https://www.googleapis.com/drive/v3/files
Authorization: Bearer ya29.a0Af...
# Response
{
  "files": [
    {
      "id": "1Cds87...",
      "name": "notes.txt",
      "mimeType": "text/plain"
    }
  ]
}
```

## Token Expiry and Referesh Token
- The access token usually expiry after 10-20 mins and the user has to follow the same process.
- To get the access for longer period the client can add request `offline` access type during request
```sh
https://accounts.google.com/o/oauth2/v2/auth
  ?client_id=YOUR_CLIENT_ID
  &redirect_uri=https://txteditor.com/callback
  &response_type=code
  &scope=https://www.googleapis.com/auth/drive.file
  &access_type=offline
```
- Now when the client will exchange the auth token, it will receive two tokens
  - access token (short lived, 10-20 mins)
  - referesh token (long lived, 1-2 days)
- If the client see that the access token is about to expire, it can renew the token using refersh token
```sh
POST https://oauth2.googleapis.com/token
{
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_SECRET",
  "refresh_token": "1//04x...",
  "grant_type": "refresh_token"
}
```



