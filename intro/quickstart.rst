Quick start
===========

Setting up Tweek to control your first feature flag takes only a few minutes. First, add the SDK to your project:

.. tabs::

  .. code-tab:: javascript npm

    npm install --save tweek-client

  .. code-tab:: javascript yarn

    yarn add tweek-client

  .. code-tab:: c#

    int main(const int argc, const char **argv) {
      return 0;
    }

Create a client
---------------

.. tabs::

  .. code-tab:: javascript

    const tweekClient = createTweekClient({
      baseServiceUrl: "https://api.{subdomain}.configz.io",
    });

  .. code-tab:: java

    TweekApiClient tweekClient = new TweekApiClient("https://api.{subdomain}.configz.io");

  .. code-tab:: c#

    ITweekApiClient tweekClient = new TweekApiClient(new Uri("https://api.{subdomain}.configz.io"));

- :code:`{subdomain}` is the name of your organization or environment


Query your configuration key and get value
------------------------------------------
In tweek configuration keys are ordered in a tree heirarchy.
Querying for "path/to/my_configuration" will lead to key named :code:`my_configuration` under path :code:`path/to/`.

.. tabs::

  .. code-tab:: javascript
  
    const myConfiguration = await tweekClient.fetch("path/to/my_configuration");

  .. code-tab:: java

    String myStringValue = tweekClient.getString("path/to/my_configuration");

  .. code-tab:: c#

    using Tweek.Client.Extensions;

    // ... 

    string myStringValue = await tweekClient.Get<string>("path/to/my_configuration", null);

Editing context
---------------
One of tweeks unique features is that properties about each of your 
identities are persisted in tweek and your identities have a schema.

So for example it is possible to create an identity named :code:`user` in the dashboard
and then set properties with types.
So considering the following identity:
::

  user: {
    name: string,
    age: number,
    gender: "male" | "female",
    country: string
  }

It is possible to update or create (upsert) it's context with the sdk.

.. tabs::

  .. code-tab:: javascript

    await tweekClient.appendContext("user", "John", { age: 23 });

  .. code-tab:: java

    Map<String, String> contextMap = new HashMap<>();
    contextMap.put("age", 23);
    tweekClient.appendContext("user", "John", contextMap);

  .. code-tab:: c#

    await tweekClient.AppendContext("user", "John", new Dictionary<string, JToken> {{ "age", JToken.FromObject(23) }});

Here we updated the context in tweek for identity "user" with the id "John". We set john's age to 23.

Querying configuration for a specific identity:
-----------------------------------------------
Now when can query configurations for John and the rules will be calculated based on his new context.

.. tabs::

  .. code-tab:: javascript

    const options = {
      context: {
        user: {
          id: "John"
        }
      }
    }

    const myConfiguration = await tweekClient.fetch("path/to/my_configuration", options);

  .. code-tab:: java

    Map<String, String> contextMap = new HashMap<>();
    contextMap.put("user","john");
    String myStringValue = tweekClient.getString("path/to/my_configuration", contextMap);

  .. code-tab:: c#

    using Tweek.Client.Extensions;

    // ... 

    string myStringValue = await tweekClient.Get<string>(
      "/path/to/my_configuration", 
      new Dictionary<string, string>{{"user", "john"}}
    );