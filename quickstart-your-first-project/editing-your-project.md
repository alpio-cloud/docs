# ✏️ Editing your project

If you want to add more data models, endpoints, or change the existing configuration of your project, use the following command to edit it:

```
alpio projects edit
```

You will be able to edit its configuration, through the same process you used in the [Configuring your project](configuring-your-project.md) step.

Once you are done, you can choose to immediately deploy your modifications, or deploy later using the following command:

```
alpio projects deploy
```

## Querying nested elements

When we defined our Planet and Galaxy models, we [added a link between them](configuring-your-project.md#adding-relationships-between-data-models). More specifically, we added a `galaxy` property on our Planet model, which is a reference to a planet's parent galaxy. Let's say we now want to retrieve a planet and its parent Galaxy in the same request.

We can add an `include_galaxy` query parameter to the `getPlanet` operation, which retrieves a single Planet by id.

```json
{
  "operationId": "getPlanet",
  "parameters": [
    {
      "name": "include_galaxy",
      "type": "boolean",
      "in": "query"
    }
  ],
  "responses": { ... }
}
```



{% hint style="info" %}
"Include" parameters are special parameters, designed to include related objects in a response. They must follow the format: `include_property` where "property" is the name of a property explicitly listed on your model. This property should be a reference to another existing model.
{% endhint %}

Let's now try to retrieve planet Neptune which [we created earlier](using-your-project.md#creating-a-planet) and its parent galaxy, using its id. If you can't find it anymore, just make a quick [query to retrieve the list of planets](using-your-project.md#listing-galaxies-and-planets).

```bash
curl 'https://YOUR_PROJECT_URL/planets/PLANET_ID?include_galaxy=true' \
--request GET \
--header 'x-api-key: YOUR_API_KEY'
```

You should see the following result:

```json
{
  "id":"PLANET_ID"
  "mass":1024
  "name":"Neptune",
  "habitable":false,
  "atmosphere":[],
  "discovered_at":"1846-09-23T00:00:00.000Z",
  "galaxyId":"GALAXY_ID",
  "galaxy":{
    "id":"GALAXY_ID",
    "name":"Milky Way"
    }
  }
```

Let's say we now want to do retrieve a galaxy, and all its planets in a single query. We could try to do the same thing we did previously, and add a `include_planets` parameter to our API `getGalaxy` operation's configuration. But remember, we said that include parameters only work for a relationship if the related element is declared as property on the origin mode, so we first need to explicitly declare a `planets` property on our Galaxy model.

Let's run first:

```bash
alpio projects edit
```

Then, let's add the planets property on the Galaxy model:

```json
{
  "Galaxy": {
    "properties": {
      // previous properties
      "planets": {
        "type": "array",
        "items": {
          "type": "reference",
          "reference": "Planet"
        }
      }
    }
  }
}
```

We can now add our `include_planets` query parameter on our `getGalaxy` operation:

```json5
{
  "operationId": "getGalaxy",
  "parameters": [
    {
      "name": "include_planets",
      "type": "boolean",
      "in": "query"
    }
  ],
  "responses": { ... }
}
```

Once you have saved these changes and deployed a new version of your project, we can try to make our query using the Milky Way's `id`:

```bash
curl 'https://YOUR_PROJECT_URL/planets/GALAXY_ID?include_planets=true' \
--request GET \
--header 'x-api-key: YOUR_API_KEY'
```

This should return the following result:

```json
{
  "id":"7a99ea83-e427-42c0-9949-8cb50be8deed",
  "name":"Milky Way",
  "planets":[
    {
      "id":"b4085b58-7530-4886-ab92-d2a80adf3e6e",
      "mass":1024,"name":"Neptune",
      "habitable":false,
      "atmosphere":[],
      "discovered_at":"1846-09-23T00:00:00.000Z",
      "galaxyId":"7a99ea83-e427-42c0-9949-8cb50be8deed"
    }
  ]
}
```

We can retrieve a galaxy and all its related planets in one query. Victory!
