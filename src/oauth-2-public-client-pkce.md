# Oauth2 Authorization Code flow public client with PKCE.
- PKCE ("pixie") - Proof Key for Code Exchange
- In previous example, the txteditor.com exchange the auth code for access token over the secure backend server with client secret.
- But in some cases there is no backend available (eg. SPA, Android App).
- The Public Client Authorization Code flow with PKCE can be used to securily access resource in such case.

## Flow
- txteditor.com generate a cryptographically secure random key called `code_verifier`
- Then it create a hash of `code_verifier` which is called `code_challenge`.
- The client send the `code_challenge` at intial authorzation request
```sh
https://accounts.google.com/o/oauth2/v2/auth
  ?client_id=YOUR_CLIENT_ID
  &redirect_uri=https://app.com/callback
  &response_type=code
  &scope=openid email profile
  &code_challenge=Th29Kf8...
  &code_challenge_method=S256
```
- The google Auth server internally saves the `code_challange` for the provided auth token
- When txteditor.com exchange the authorization code, it sends the `code_verifier` with it
```sh
POST https://oauth2.googleapis.com/token
{
  "grant_type": "authorization_code",
  "code": "4/0AdQt8...",
  "client_id": "YOUR_CLIENT_ID",
  "redirect_uri": "https://app.com/callback",
  "code_verifier": "Gjs8Lkdm29f..."
}
```
- The Authorization server now calculate the hash of `code_verifier` and if it matches with saved `code_challange` then only it return access token.
  
