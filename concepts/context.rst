Context
=======


What is context?
----------------

Context is a collection of all the facts available in a single Tweek evaluation. Context is evaluated against key’s rules to retrieve the value, meaning:

Context + key definition = value

For example, assuming we have a key :code:`is_allowed_to_drive` with rule:

.. code-block:: code

	default value: false
	User.Age > 18 then true

If we send these requests to Tweek:

.. code-block:: code

	GET http://api.dev.local.tweek.fm:81/api/v1/keys/is_allowed_to_drive -> false
	GET http://api.dev.local.tweek.fm:81/api/v1/keys/is_allowed_to_drive?User.Age=20 -> true

In order to get the right values from Tweek, we need to provide Tweek the relevant context for the request.

Inline context vs remote context
--------------------------------

While we can always pass context parameters in url, a different approach is to save context in Tweek for identity. For example:

.. code-block:: code
	
	GET http://api.dev.local.tweek.fm:81/api/v1/keys/is_allowed_to_drive?User=john -> false

We’ve asked for the value of “is_allowed_to_drive” for user John, but Tweek doesn’t know any facts about him, let’s change it:

.. code-block:: code

	POST http://api.dev.local.tweek.fm:81/api/v1/context/user/john
	{
			"Age": 20
	}

After adding the data, let’s retry our first request:

.. code-block:: code

	GET http://api.dev.local.tweek.fm:81/api/v1/keys/is_allowed_to_drive?User=john -> true

Identities & Properties
-----------------------

You’ve noticed that we used “User.Age” and not simply “Age”, the reason is that Tweek treat facts as properties on top of identities, for example:

.. code-block:: code

	GET http://api.dev.local.tweek.fm:81/api/v1/keys/path/to/key?User=john&User.Country=england

Tweek understands that it need to get the values for identity user “john”.
Tweek look at inline context to see relevant properties for this identity, for example “User.Country=england”
Tweek look at remote context to get all properties for identity user “john”, from previous example it would be Age=20
Tweek merge inline and remote context to a single context.
Tweek evaluate the context against the requested key definition (rules)
Tweek send the results back to the user