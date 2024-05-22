# 📟 CLI

## Installation

The CLI can currently be installed as Node.js package, through npm

```
npm install -g @alpio/cli
```

## Commands

### Authentication

#### `alpio login`

Allows you to login to Alpio Cloud.

***

### Projects

#### `alpio projects list`

List existing projects, including their URL.

#### `alpio projects create`

Create and configure a new project, using your code editor.

#### `alpio projects edit`

Edit an existing project, using your code editor.

#### `alpio projects deploy`

Deploy an existing project, and output its URL.

***

### Project authentication

#### `alpio projects auth api-keys list`

List existing API keys for your project.

#### `alpio projects auth api-keys create`

Create a new API key for your project.

#### `alpio projects auth api-keys delete`

Delete an API key. Any requests using the deleted API key will immediately be forbidden. Once deleted, API keys can not be retrieved.

***
