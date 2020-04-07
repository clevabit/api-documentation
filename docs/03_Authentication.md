# Authentication

API calls to any endpoint other than _/auth/_ require a valid authorization token.

By default, the authorization request requires a valid CSRF (Cross Site Request Forgery)
token, which is provided by the server and only valid for a single form submission. In
addition the CSRF token is only valid for limited amount of time.

For applications that cannot show an HTML login form, or partner systems that use direct
server to server communication, the API provides a way to login a third party (user) by
providing its unique application id (_x-app-id_) and application key (_x-app-key_), as
well as the customer id (_x-app-cid_) it wants to be logged in with.

Requests using the application id and application key to authenticate as a valid
login mechanism, still need to provide a valid username/email address and password.

For partners reading data for multiple users, a special account with access to multiple
customer accounts may be provided. Otherwise multiple requests (one per customer account)
are required. 

To request an application id and application key, please contact your clevabit
representative.
