---
description: Learn how to set up a Alpio cloud account and how to install the CLI
---

# ⚙️ Setup & usage

## CLI

Currently, Alpio services are only available through a **CLI**, which can be installed through Node.js's package manager, **npm**.

### Requirements

* node >= v20
* npm

### Installation & setup

In a terminal window, run:

```sh
npm install -g @alpio/cli
```

Then, you will need to create an account. Run:

```sh
alpio login
```

This should open a page in your web browser, where you will be able to **sign up**.

You can then use the CLI to create, edit or deploy new projects. To list all available commands, run:

```sh
alpio help
```

