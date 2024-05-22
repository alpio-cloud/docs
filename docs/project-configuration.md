---
description: >-
  Each project created or edited through the CLI is configured using a JSON file
  which defines your project's configuration. This is a complete reference of
  the available properties.
---

# 📋 Project configuration

## Project object

<table><thead><tr><th width="160">Property name</th><th width="289">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>name</strong></td><td><code>String</code></td><td>The name of your project. This can be changed later</td></tr><tr><td><strong>models</strong></td><td><code>Object { [name]:</code> <a href="project-configuration.md#model-object"><code>Model</code></a> <code>}</code></td><td>An object whose keys are the name of a model, and the value is a <a href="project-configuration.md#model-object">model object</a>.</td></tr><tr><td><strong>api</strong></td><td><a href="project-configuration.md#api-object"><code>API</code></a></td><td>The configuration of your API.</td></tr></tbody></table>

## Model object

<table><thead><tr><th width="163">Property name</th><th width="274">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>primary_key</strong></td><td><code>String | String[]</code></td><td>The name of your primary key. This must match the name of one of your <a href="project-configuration.md#property-object">properties</a>. To use a composite index, pass an array. Primary key must always be non-nullable.</td></tr><tr><td><strong>properties</strong></td><td><p><code>Object {</code></p><p>  <code>[name]:</code> <a href="project-configuration.md#property-object"><code>Property</code></a></p><p><code>}</code></p></td><td>An object whose keys are the name of a property, and the value is a <a href="project-configuration.md#property-object">property object</a>.</td></tr></tbody></table>

### Property object

<table><thead><tr><th width="163">Property name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>type</strong></td><td><code>'string' | 'boolean' | 'number' | 'date' | 'timestamp' | 'array' | 'reference'</code></td><td>The type of your property. If it set to <code>array</code>, then <code>items</code> must be defined. If it set to <code>reference</code>, then <code>reference</code> must be defined.</td></tr><tr><td><strong>nullable</strong></td><td><code>Boolean</code></td><td>Indicates whether the property is nullable in the database or not. If not specified, the default value is <code>true</code>.</td></tr><tr><td><strong>readonly</strong></td><td><code>Boolean</code></td><td>A readonly property will never be able to be set by the client, on create or update operations.</td></tr><tr><td><strong>unique</strong></td><td><code>Boolean</code></td><td>Indicates that the associated value can only exist once in the database.</td></tr><tr><td><strong>reference</strong></td><td><code>String</code></td><td>A reference to an existing property. It must only be used if <code>type</code> is set to <code>reference</code>. The value must match the name of one your model (eg. <code>Planet</code>), or one of your model's property (eg. <code>Planet/name</code>)</td></tr><tr><td><strong>items</strong></td><td><a href="project-configuration.md#property-object"><code>Property</code></a></td><td>Must only be used if <code>type</code> is set to <code>array</code>.</td></tr></tbody></table>

## API object

<table><thead><tr><th width="163">Property name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>info</strong></td><td><a href="project-configuration.md#info-object"><code>Info</code></a></td><td>A description of your API.</td></tr><tr><td><strong>paths</strong></td><td><p><code>Object {</code></p><p> <code>[path]:</code> <a href="project-configuration.md#path-object"><code>Path</code></a></p><p><code>}</code></p></td><td>An object whose keys are paths (eg. <code>/planets/:id</code>) and values are <a href="project-configuration.md#path-object">path objects</a>.</td></tr></tbody></table>

### Info object

<table><thead><tr><th width="163">Property name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>title</strong></td><td><code>String</code></td><td>The name of your API</td></tr><tr><td><strong>description</strong></td><td><code>String</code></td><td>A short description of your API</td></tr><tr><td><strong>version</strong></td><td><code>String</code></td><td>A semver-compatible version (eg. <code>1.0.0</code>)</td></tr></tbody></table>

### Path object

<table><thead><tr><th width="164">Property name</th><th width="287">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>methods</strong></td><td><p><code>Object {</code></p><p>  <code>[http_verb]:</code> <a href="project-configuration.md#method-object"><code>Method</code></a></p><p><code>}</code></p></td><td>An object whose keys are HTTP verbs such as <code>get, post, put, patch</code> and whose values are <a href="project-configuration.md#model-object">method objects</a>.</td></tr><tr><td><strong>parameters</strong></td><td><a href="project-configuration.md#parameter-object"><code>Parameter</code></a><code>[]</code></td><td>An array of <a href="project-configuration.md#parameter-object">parameter objects</a>.</td></tr><tr><td><strong>paths</strong></td><td><code>Object { [path]:</code> <a href="project-configuration.md#path-object"><code>Path</code></a> <code>}</code></td><td></td></tr></tbody></table>

{% hint style="info" %}
Parameters defined at the path level apply to all its methods, but also its subpath methods. If you only wish to define a parameter for a single method, define the parameter at the method level.
{% endhint %}

### Method object

<table><thead><tr><th width="166">Property name</th><th width="291">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>operationId</strong></td><td><code>String</code></td><td>A unique identifier for your endpoint</td></tr><tr><td><strong>description</strong></td><td><code>String</code></td><td>A short description of what your endpoint does</td></tr><tr><td><strong>parameters</strong></td><td><a href="project-configuration.md#parameter-object"><code>Parameter</code></a><code>[]</code></td><td>An array of <a href="project-configuration.md#parameter-object">parameter objects</a>.</td></tr><tr><td><strong>body</strong></td><td><a href="project-configuration.md#property-object"><code>Property</code></a></td><td>A property describing the body of the HTTP request. It must only be used on <code>POST</code>, <code>PUT</code> or <code>PATCH</code> endpoints.</td></tr><tr><td><strong>responses</strong></td><td><p><code>Object {</code></p><p> <code>[status_code]:</code> <a href="project-configuration.md#property-object"><code>Property</code></a></p><p><code>}</code></p></td><td>An object whose keys are HTTP status codes as strings, and whose values are <a href="project-configuration.md#property-object">property objects</a>. Response objects must include a <code>description</code></td></tr></tbody></table>

### Parameter object

<table><thead><tr><th width="169">Property name</th><th width="250">Type</th><th>Description</th></tr></thead><tbody><tr><td><strong>name</strong></td><td><code>String</code></td><td>The name of your parameter. The couple (<code>name</code>, <code>in</code>) must be unique.</td></tr><tr><td><strong>description</strong></td><td><code>String</code></td><td>A description of what your parameter does.</td></tr><tr><td><strong>in</strong></td><td><code>'path' | 'query'</code></td><td>Defines where to expect the parameter, either in path or query. If it set to <code>path</code>, then the path include a URL parameter of the same name</td></tr><tr><td><strong>required</strong></td><td><code>Boolean</code></td><td>Makes the parameter mandatory. It must only be used for <code>query</code> parameters, as <code>path</code> parameters are always required.</td></tr></tbody></table>

