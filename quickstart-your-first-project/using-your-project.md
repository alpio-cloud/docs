---
description: Learn how to make API requests to query your project's data
---

# 👩‍💻 Using your project

Now that we've deployed our project, and [created an API key](deploying-your-project.md#creating-an-api-key) to authenticate ourselves, let's use our API and create some data.

## Listing galaxies & planets

Although we don't have any data at the moment, let's list our galaxies to make sure everything is working as expected. Let's make a `GET` request to do so. You can use any HTTP client to do so, like curl, or Postman.

```bash
curl 'https://YOUR_PROJECT_URL/galaxies' \
--request GET \
--header 'x-api-key: YOUR_API_KEY'
```

If everything is working well, you should get an empty array in response:

```
[]
```

Let's do the same thing for planets:

```bash
curl 'https://YOUR_PROJECT_URL/planets' \
--request GET \
--header 'x-api-key: YOUR_API_KEY'
```

You should see the same result.

## Creating a galaxy

Let's make a `POST` request to create our first galaxy.&#x20;

<pre class="language-bash"><code class="lang-bash">curl 'https://<a data-footnote-ref href="#user-content-fn-1">YOUR_PROJECT_URL</a>/galaxies' \
--request POST \
--header 'x-api-key: <a data-footnote-ref href="#user-content-fn-2">YOUR_API_KEY</a>' \
--header 'content-type: application/json' \
--data '{"name": "Milky Way"}'
</code></pre>

This should return a galaxy with an auto-generated `id` and a name set to "Milky Way", like this:

```json
{
    "id": "d653f053-b07f-4ace-af00-d1d3da3af225", // randomly generated ID
    "name": "Milky Way"
}
```

You can try to run the first request to see that our galaxy is now present in the list of galaxies.

## Creating a planet

We can also create a Planet; let's create planet Earth:

```bash
curl 'https://YOUR_PROJECT_URL/planets' \
--request POST \
--header 'x-api-key: YOUR_API_KEY' \
--header 'content-type: application/json' \
--data '{
  "name": "Earth",
  "mass": 5972,
  "habitable": true,
  "discovered_at": "0001-01-01T00:00:00.000Z"
}'
```

You should get a result with an automatically generated ID, which should look something like this:

```json
{
    "id": "1a54f440-249f-4faf-a984-dd4864e2d161",
    "mass": 5972,
    "name": "Earth",
    "habitable": true,
    "atmosphere": [],
    "discovered_at": "0001-01-01T00:00:00.000Z",
    "galaxyId": null
}
```

## Creating a planet linked to a galaxy

Now, let's say we want to create planet Neptune in our model, and have it be linked to the Milky Way galaxy we created just before. When we defined our Planet model, we added a property called `galaxy`, which represents a related Galaxy entity (using the the `reference` keyword).

```json
{
  "Planet": {
    "primary_key": "id",
    "properties": {
      // other properties...
      "galaxy": {
        "type": "reference",
        "reference": "Galaxy"
      }
    }
  }
}
```

This allows us to link a Planet to a Galaxy, directly upon creation. By re-using the Galaxy id we obtained [when we created the Milky Way,](using-your-project.md#creating-a-galaxy) we can write the following request to create planet Neptune:

<pre class="language-bash"><code class="lang-bash">curl https://YOUR_PROJECT_URL/planets \
--request POST \
--header 'x-api-key: YOUR_API_KEY' \
--header 'content-type: application/json' \
--data '{
  "name": "Neptune",
  "mass": 1024,
  "habitable": false,
  "discovered_at": "1846-09-23T00:00:00.000Z",
  "galaxy": {
    "id": "<a data-footnote-ref href="#user-content-fn-3">GALAXY_ID</a>"
  }
}'
</code></pre>

The result should look something like this, with an automically generated `id` and a `galaxyId` set to the galaxy's id we passed as input.

```json
{
    "id": "65f74f2d-4cb0-4135-926c-b82678608a62",
    "mass": 1024,
    "name": "Neptune",
    "habitable": true,
    "atmosphere": [],
    "discovered_at": "1846-09-23T00:00:00.000Z",
    "galaxyId": "GALAXY_ID"
}
```

Our API seems to work well, the last thing we can do is make some updates to our data model or API if we need to.

[^1]: Replace this with your project's URL

[^2]: Replace this your API key

[^3]: Don't forget to change this your randomly generated ID :wink:
