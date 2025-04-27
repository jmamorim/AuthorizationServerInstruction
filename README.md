# Authorization Server 55624-57409 — Instructions

## 1. Register a New Client

**Request:**
```http
POST https://proj1-cse5b6hadba7a8du.westeurope-01.azurewebsites.net/auth/clients
Content-Type: application/x-www-form-urlencoded

clientid=your_client_id&clientsecret=your_client_secret
```

---

## 2. Register a New User

**Request:**
```http
POST https://proj1-cse5b6hadba7a8du.westeurope-01.azurewebsites.net/auth/users
Content-Type: application/x-www-form-urlencoded

username=your_username&password=your_password
```

---

## 3. Start Authorization Flow

**Request:**
```http
GET https://proj1-cse5b6hadba7a8du.westeurope-01.azurewebsites.net/authorize?response_type=code&client_id=your_client_id&redirect_uri=https://yourapp.com/callback&scope=public&state=xyz
```
**Query Parameters:**
- `response_type` (must be `code`)
- `client_id`
- `redirect_uri`
- `scope` (optional but have to be included)
- `state` (optional but have to be included)

---

## 4. Log In to the Authorization Server

After starting the authorization request, you will be redirected to a login page.

Alternatively, you can send the login manually:

**Request:**
```http
POST https://proj1-cse5b6hadba7a8du.westeurope-01.azurewebsites.net/authorize/login
Content-Type: application/x-www-form-urlencoded

username=your_username&password=your_password&client_id=your_client_id&redirect_uri=https://yourapp.com/callback
```
You can ignore `scope` and `state` when posting manually.

---

## 5. Request a Token

After successfully logging in and authorizing, you will receive an **authorization code** at your `redirect_uri`.

Use that code to request an access token:

**Request:**
```http
GET https://proj1-cse5b6hadba7a8du.westeurope-01.azurewebsites.net/token?code=your_authorization_code
```
You will receive a json with the token contents on Access_Token more instructions will be below.
---

## 6. How Should I Use All of This?

It's pretty easy!

1. First, **register** a client and a user using the `/auth/clients` and `/auth/users` endpoints.
2. Then, **start the authorization flow** by making a `GET` request to `/authorize`.
3. The server will **prompt you to log in** automatically.
4. After logging in, the server **handles the rest** and redirects you with an **authorization code**.
5. Finally, **exchange the code for an access token** using the `/token` endpoint.

**Important:**  
To receive and decode the token on your client, you can do something like this:

```javascript
const tokenResponse = await fetch(`${tokenEndpointRemote}?code=${code}`);
const tokenData = await tokenResponse.json();
res.json(jwt.decode(tokenData['Access_Token']));
```

This will fetch the access token and decode it.

You can also test the individual endpoints manually if you want to better understand or debug the flow.

---

# Notes

- Your client **must have a user interface** to properly display the login form.
  - If you use Postman, cURL, or similar tools, you **won't** be able to log in through a browser — you need an actual webpage.
- Always use `application/x-www-form-urlencoded` format for POST request bodies unless stated otherwise.
- The `redirect_uri` must **exactly match** between the `/authorize` request and the `/token` request.
- `scope` and `state` are optional, but using them is recommended for better security and functionality.

