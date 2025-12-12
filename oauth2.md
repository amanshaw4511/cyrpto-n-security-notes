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
```
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
```
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
```
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


