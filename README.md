# the-Rails-Authenticity-Token
What happens

When the user views a form to create, update, or destroy a resource, the Rails app creates a random authenticity_token, stores this token in the session, and places it in a hidden field in the form. When the user submits the form, Rails looks for the authenticity_token, compares it to the one stored in the session, and if they match the request is allowed to continue.

Why it happens

Since the authenticity token is stored in the session, the client cannot know its value. This prevents people from submitting forms to a Rails app without viewing the form within that app itself. Imagine that you are using service A, you logged into the service and everything is ok. Now imagine that you went to use service B, and you saw a picture you like, and pressed on the picture to view a larger size of it. Now, if some evil code was there at service B, it might send a request to service A (which you are logged into), and ask to delete your account, by sending a request to http://serviceA.com/close_account. This is what is known as CSRF (Cross Site Request Forgery).

If service A is using authenticity tokens, this attack vector is no longer applicable, since the request from service B would not contain the correct authenticity token, and will not be allowed to continue.

API docs describes details about meta tag:

CSRF protection is turned on with the protect_from_forgery method, which checks the token and resets the session if it doesn't match what was expected. A call to this method is generated for new Rails applications by default. The token parameter is named authenticity_token by default. The name and value of this token must be added to every layout that renders forms by including csrf_meta_tags in the HTML head.

Notes

Keep in mind, Rails only verifies not idempotent methods (POST, PUT/PATCH and DELETE). GET request are not checked for authenticity token. Why? because the HTTP specification states that GET requests is idempotent and should not create, alter, or destroy resources at the server, and the request should be idempotent (if you run the same command multiple times, you should get the same result every time).

Also the real implementation is a bit more complicated as defined in the beginning, ensuring better security. Rails does not issue the same stored token with every form. Neither does it generate and store a different token every time. It generates and stores a cryptographic hash in a session and issues new cryptographic tokens, which can be matched against the stored one, every time a page is rendered. See request_forgery_protection.rb.

Lessons

Use authenticity_token to protect your not idempotent methods (POST, PUT/PATCH, and DELETE). Also make sure not to allow any GET requests that could potentially modify resources on the server.
