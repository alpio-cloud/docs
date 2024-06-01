# ⚙️ Configuring your project

## Creating the project

After you have installed the CLI & created your Alpio account, you can create your first project.

In a terminal window, run the following command:

```sh
alpio projects create
```

After following the instructions in the CLI (press the `ENTER` key), your favorite code editor should open to a configuration file.

{% hint style="info" %}
If the configuration file does not open in your preferred code editor, make sure the [global environment variable `EDITOR`](https://bash.cyberciti.biz/guide/$EDITOR\_variable) is set correctly for your code editor.\
If you are using VSCode, you can set the variable to: `code --wait`
{% endhint %}

The configuration file uses the JSON format, and is generated from a template containing one data model, and one API endpoint.

{% hint style="success" %}
To help you edit your project configuration file correctly, Alpio project configuration files use [JSON Schema](https://json-schema.org/). If your code editor supports it natively, you will have instant validation & autocompletion.
{% endhint %}

### Giving a name to your project

You can start by giving a name to your project, by giving a value to the `name` key.

```json
{
  "name": "YOUR PROJECT NAME",
  "models": {
    // ...
  },
  "api": {
    // ...
  }
}
```

That's all you need to have a deployable backend! If you wanted to, you could close the configuration file, and deploy your project right away. Although it would be a bit limited, your API would work right away.

## Data modeling

### Adding additional properties to an existing model

You can then add additional properties to the `Planet` data model. Let's add the following properties:

```json
{
  "models": {
    "Planet": {
      "primary_key": "id",
      "properties": {
        // existing properties
        "mass": {
          "type": "integer"
        },
        "habitable": {
          "type": "boolean"
        },
        "atmosphere": {
          "type": "array",
          "items": {
            "type": "string"
          }
        },
        "discovered_at" : {
          "type": "timestamp"
        }
      }
    }
  }
}
```

We've added four new properties:

* **mass**, an integer which represents the mass of the planet
* **habitable**, a boolean indicating whether or not the planet could sustain human life
* **atmosphere**, an array of strings which will describe the main gases present in the planet's atmosphere
* **discovered\_at**, a timestamp which indicates when the planet was discovered

### Creating a new data model

Let's add another data model, a **Galaxy**

```json
{
  "models": {
    "Planet": {
      // planet properties...
    },
    "Galaxy": {
      "primary_key": "id",
      "properties": {
        "id": {
          "type": "string",
          "nullable": false,
          "readonly": true,
          "default": "uuid()"
        },
        "name": {
          "type": "string",
          "nullable": false
        }
      }
    }
  }
}
```

We've just added a new data model, **Galaxy**, which will represent the different galaxies of the universe.  This model features the following properties:

* **`id`**, which is a string. To indicate that the key will be used as the unique identifier of the resource, we must add the `primary_key` property on the model itself, and assign it the name of the key we want to use. Unique identifiers must always be non-nullable, so we must also add `nullable: false` to the property's configuration. Finally, we can either pass the value for the id ourselves when we create an entity, or let our backend handle the id generation for us. We went for the latter option here, and added `readonly: true` to prevent the `id` property to be specified from  the client side in create or update operations, and `default: uuid()` to indicate that an UUID v4 should be generated upon creation.
* **`name`**, which will be the name of the galaxy. We made it non-nullable, to make sure all galaxies are created with a name.

But now we need a way to tell to which galaxy a planet belongs. In other words, we need to define a **relationship** between our two data models.

### Adding relationships between data models

Let's add a property to our **Planet** data model to indicate in what galaxy they are located.

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

We have now added a new property called `galaxy` to our Planet model. It is defined as a **reference** to the Galaxy data model. A reference is a special type of property (in addition to the usual types `string, integer, float, boolean, timestamp, array`) designed to **reference another part of your data model**. You can reference an entire data model, or only one its properties. This allows you to avoid duplicating different parts of your schema when it is required, and helps you make sure that the matching types are always the same. For example, if the `x` property of your model `A` is defined as a reference to the `y` property of another model `B`, you will make sure that `A.x` and `B.y` are always of the same type, even if you later change the type of `B.y`.

{% hint style="info" %}
References are **case-sensitive.**
{% endhint %}

## API modeling

### Creating a new query endpoint

In the template configuration, we already have a GET endpoint defined for us, which allows us to retrieve a planet by its id. Let's create a new one to retrieve a list of planets.

```json
{
  "/planets": {
    "methods": {
      "get": {
        "operationId": "listPlanets",
        "responses": {
          "200": {
            "description": "A successful response",
            "type": "array",
            "items": {
              "type": "reference",
              "reference": "Planet"
            }
          }
        }
      }
    },
    "paths": {
      // subpath endpoints go here
      "/:id": { ... }
    }
  }
}
```

We defined a new endpoint, named `listPlanets`, at the root of the `/planets` path. Notice how instead of a returning a single of type `Planet`, we instead return an `array`, whose `items` property reference the `Planet` data model defined above.

### Adding parameters to an existing endpoint

Let's now add a query parameter to this new endpoint to only retrieve habitable planets.&#x20;

```json
{
  "get": {
    "operationId": "listPlanets",
    "parameters": [
      {
        "name": "habitable",
        "in": "query",
        "type": "reference",
        "reference": "Planet/habitable",
        "default": false
      }
    ],
    "responses": {
      // responses configuration
    }
  }
}
```

We added here a new parameter, `habitable`, which is specific to our `listPlanets` method. Notice that we didn't define this parameter as a `boolean` parameter, but rather as a **reference** to a Planet's `habitable` property. This tells Alpio to map whatever is passed as input for the query parameter to the actual `habitable` property of our Planet data model. If we simply defined the parameter as being of type `boolean`, the configuration would be valid, but it wouldn't have any effect on the query's results, as Alpio wouldn't know what it should do with this query parameter.



### Creating a new "create" endpoint

We can already get a single planet by its id, and query a list of planets, but we can't create a new planet yet, which might be problematic if we ever want our 2 previous endpoints to be of any use. Let's create a `POST` endpoint, which will allow us to create a new planet. For `POST` endpoints, the input data is usually passed in the `body` property of the underlying HTTP request. Let's do that.

```json
{
  "/planets": {
    "methods": {
      "get": {
        // query method description
      },
      "post": {
        "operationId": "createPlanet",
        "body": {
          "type": "reference",
          "reference": "Planet"
        },
        "responses": {
          "201": {
            "description": "Successful creation",
            "type": "reference",
            "reference": "Planet"
          }
        }
      }
    },
    "paths": {
      // subpath endpoints go here
    }
  }
}
```

To define the schema the endpoint should accept through the request's `body` parameter, we once again used a **reference** to our Planet data model.&#x20;

We also used a reference to indicate that the request will also return a Planet data object upon success.

### Adding additional endpoints for our Galaxy model

We can do the same thing that we did for our Planet model, and create three endpoints for Galaxy model, under the `/galaxies` path.

```json
{
  "paths": {
    "/planets": {
      // paths related to planets
    },
    "/galaxies": {
      "methods": {
        "get": {
          "operationId": "listGalaxies",
          "responses": {
            "200": {
              "description": "A successful response",
              "type": "array",
              "items": {
                "type": "reference",
                "reference": "Galaxy"
              }
            }
          }
        },
        "post": {
          "operationId": "createGalaxy",
          "body": {
            "type": "reference",
            "reference": "Galaxy"
          },
          "responses": {
            "201": {
              "description": "Successful creation",
              "type": "reference",
              "reference": "Galaxy"
            }
          }  
        }
      },
      "paths": {
        "/:id": {
          "parameters": [
            {
              "name": "id",
              "in": "path",
              "type": "string"
            }
          ],
          "methods": {
            "get": {
              "operationId": "getGalaxy",
              "responses": {
                "200": {
                  "description": "A successful response",
                  "type": "reference",
                  "reference": "Galaxy"
                }
              }
            }
          }
        }
      }
    }
  }
}
```

We have just added three new endpoints:

* `GET /galaxies`, to list our galaxies
* `POST /galaxies`, to create a new galaxy
* `GET /galaxies/:id`, to retrieve a galaxy by its `id`

### Wrapping up before deploying our project

Our entire project configuration should look something like this:

```json
{
  "$schema": "https://storage.googleapis.com/alpio/schema.json",
  "name": "YOUR PROJECT NAME",
  "models": {
    "Planet": {
      "primary_key": "id",
      "properties": {
        "id": {
          "type": "string",
          "nullable": false,
          "readonly": true,
          "default": "uuid()"
        },
        "name": {
          "type": "string",
          "nullable": false
        },
        "mass": {
          "type": "integer"
        },
        "habitable": {
          "type": "boolean"
        },
        "atmosphere": {
          "type": "array",
          "items": {
            "type": "string"
          }
        },
        "discovered_at": {
          "type": "timestamp"
        }
      }
    },
    "Galaxy": {
      "primary_key": "id",
      "properties": {
        "id": {
          "type": "string",
          "nullable": false,
          "readonly": true,
          "default": "uuid()"
        },
        "name": {
          "type": "string",
          "nullable": false
        },
        "galaxy": {
          "type": "reference",
          "reference": "Galaxy"
        }
      }
    }
  },
  "api": {
    "info": {
      "title": "My API Title",
      "description": "Description of my API",
      "version": "1.0.0"
    },
    "paths": {
      "/planets": {
        "methods": {
          "get": {
            "operationId": "listPlanets",
            "parameters": [
              {
                "name": "habitable",
                "in": "query",
                "type": "reference",
                "reference": "Planet/habitable"
              }
            ],
            "responses": {
              "200": {
                "description": "A successful response",
                "type": "array",
                "items": {
                  "type": "reference",
                  "reference": "Planet"
                }
              }
            }
          }
        },
        "paths": {
          "/:id": {
            "parameters": [
              {
                "name": "id",
                "in": "path",
                "type": "string"
              }
            ],
            "methods": {
              "get": {
                "operationId": "getPlanet",
                "responses": {
                  "200": {
                    "type": "reference",
                    "description": "A successful response",
                    "reference": "Planet"
                  }
                }
              },
              "post": {
                "operationId": "createPlanet",
                "body": {
                  "type": "reference",
                  "reference": "Planet"
                },
                "responses": {
                  "201": {
                    "description": "Successful creation",
                    "type": "reference",
                    "reference": "Planet"
                  }
                }
              }
            }
          }
        }
      },
      "/galaxies": {
        "methods": {
          "get": {
            "operationId": "listGalaxies",
            "responses": {
              "200": {
                "description": "A successful response",
                "type": "array",
                "items": {
                  "type": "reference",
                  "reference": "Galaxy"
                }
              }
            }
          },
          "post": {
            "operationId": "createGalaxy",
            "body": {
              "type": "reference",
              "reference": "Galaxy"
            },
            "responses": {
              "201": {
                "description": "Successful creation",
                "type": "reference",
                "reference": "Galaxy"
              }
            }  
          }
        },
        "paths": {
          "/:id": {
            "parameters": [
              {
                "name": "id",
                "in": "path",
                "type": "string"
              }
            ],
            "methods": {
              "get": {
                "operationId": "getGalaxy",
                "responses": {
                  "200": {
                    "type": "reference",
                    "description": "A successful response",
                    "reference": "Galaxy"
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
```

You can close the file in your code editor. This will save your project on Alpio's cloud. Any potential error in the configuration will be displayed in the CLI, allowing you to make adjustments if necessary. Let's now deploy the project!
