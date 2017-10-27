.. meta::
   :description: Hasura Auth user sessions 
   :keywords: hasura, users, sessions


Understanding Sessions
======================

When a user is logged-in, a session is attached.

A session is nothing but a unique, un-guessable identifier  attached to that
Hasura Auth user account (referred to as ``auth_token``) for that session. This
way a user can make subsequent requests without having to authenticate with
credentials on every request. Instead, on every request the user can present
the ``auth_token`` to identify themself.

Every service benefits from having the user's information (id and roles) with
each request. In the Hasura platform, every request goes through the API
Gateway. So, the API Gateway integrates with the session store to act as a
session middleware for all services.

When the gateway receives a request, it looks for a session token in the
``Bearer`` token of ``Authorization`` header or in the cookie. It then
retrieves the user's id and roles attached to this session token from the
session store. This information is sent as ``X-Hasura-User-Id`` and
``X-Hasura-Role`` headers to the upstream service.

When the session token is absent from both header and cookie, the gateway
considers it an anonymous request and adds the header ``X-Hasura-Role:
anonymous``. The ``X-Hasura-User-Id`` header is **not** set in this case.


.. _session-expiry:

Session Expiry
--------------
A user session will expire:

* when a logout action is requested
* password is changed
* role is assigned or unassigned
* sessions are expired explicitly by any admin user

Sessions are managed by Hasura Auth and the Gateway. Whenever a request is
made Hasura Gateway resolves the session from Authorization header or Cookies.

If you are building browser-based apps, then Hasura Auth already sends
appropriate cookie headers to. You don't have to do any additional work to
manage sessions, except making the appropriate API calls.

If you are building mobile/device apps, then you have to device your own
mechanism of storing the authentication tokens (``auth_token``) and managing
them. That means, storing and updating them whenever a Hasura Auth API returns
a new authentication token, and remove all existing authentication tokens
(``auth_token``) on the above conditions.