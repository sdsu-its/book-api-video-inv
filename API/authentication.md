# Authentication
Authentication with the API is initially handled via Username and Password authentication for the initial Login request. Afterward, all requests are authenticated with a secure session token, which is obtained during the login process, and needs to be included as a header in EVERY request made to the API.

Session tokens are not everlasting, and will eventually expire, at which point you will receive a **401 - Unauthorized** error message. Tokens cannot be renewed, and you will need to authenticate via username and password to obtain a new token.

{% method %}
## POST `/login`

This request is used to authenticate a user, be it to create a new transaction, or access the Admin tools. It can also be used to verify if a user has entered their password correctly, for example if they are updating their password.

The password needs to be sent as a Base64 encoded string. This allows for complex passwords, that would break the JSON formatting if not encoded properly.

If the username and password are correct, the response from the server will contain a `session` header. This is your session token. You will want to save this, possible in a Cookie, Global Variable, etc. You should keep it relatively secure, since it is used to authenticate with the API for all further interactions, but will eventually expire. The tokens TTL is set in the Server's Vault configuration[^1].

{% sample lang="js" %}
Login request using jQuery. You will need to fill the object yourself, this example uses example content selectors.

```js
var payload = {};
payload.username = $('#loginUsername').val();
payload.username = bota($('#loginPassword').val());

$.ajax({
  type: "POST",
  url: API_ROOT + '/login',
  data: payload,
  success: success,
  dataType: 'json'
});
```

{% common %}
If successful (**200**), the user object corresponding to the username and password pair that were submitted is returned.

```json
{
  "dbID": 1,
  "username": "admin",
  "firstName": "Administrator",
  "lastName": "User",
  "supervisor": true
}
```
Also, a `session` header will be included in the response, this is the session token that you will need to include in all future requests, and will serve to identify the user.

If either the username or password or incorrect a status **404** is returned, along with a JSON error message. All expected errors will return a status message, along with a possible explanation of why the error occurred.

```json
{
  "status": "Error",
  "message": "That user does not exist or the password is incorrect."
}
```
{% endmethod %}

[^1] The Vault settings can be changed in real time, however the WebApp would need to be restarted for the TTL changes to take effect. More information on the Vault configuration can be found in the WebApp repo at: https://github.com/sdsu-its/video-inv.
