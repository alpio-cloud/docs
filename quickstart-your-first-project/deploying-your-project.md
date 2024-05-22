# 🚀 Deploying your project

If you used the command `alpio projects create`, the CLI should ask you if you want to immediately deploy the project. You can say yes (the default is no). If you decided to deploy later, you can deploy whenever you're ready using the following command:

```
alpio projects deploy
```

This will ask you which project you want to deploy, and then proceed to do so.

The deploy should take a few minutes, don't close your terminal window if you need to see output URL of your project (although closing the terminal window has no impact on the deploy process itself). If you accidentally closed your terminal, you can still see your project's URL using the following command:

```
alpio projects list
```

You can now make an HTTP request to your project to test it, using a CLI tool like `curl`, or HTTP clients with a GUI, like Postman, or Insomnia.

## Handling authentication

If you decided to make your project **private** (this is the default behaviour), you will need to add a method of authentication to your HTTP requests. Currently, **only API keys are supported**, although other methods will quickly be added.

### Creating an API key

To add an API key to your, run the following command:

```
alpio projects auth api-keys create
```

This will ask you to select to which project you want to add an API key, to define name for the key, and then proceed to create it.

{% hint style="warning" %}
Make sure to **write down** the API key's value somewhere **safe**, as you won't be able to see it again.
{% endhint %}

### Listing API key

If you want to list your project's API keys, use the following command:

```
alpio projects auth api-keys list
```

You will be able to see the name of your API keys, and a redacted preview.

### Deleting an API key

If you lose one of your API keys, or think it might have been compromised, make sure to **delete** the API key and to re-create another one, using the following commands

<pre class="language-sh"><code class="lang-sh"><strong>alpio projects auth api-keys delete
</strong>alpio projects auth api-keys create
</code></pre>

### Authenticating using an API key

To authenticate using the API key you have just created, simply pass it to the `x-api-key` header of your HTTP request.

```bash
curl https://YOUR_PROJECT_URL/planets -H 'x-api-key: YOUR_API_KEY'
```

This should allow you to access your API.
