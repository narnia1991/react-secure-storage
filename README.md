[![npm](https://img.shields.io/npm/v/react-secure-storage.svg)](https://www.npmjs.com/package/react-secure-storage) [![downloads](https://img.shields.io/npm/dm/react-secure-storage.svg)](http://npm-stat.com/charts.html?package=react-secure-storage)

## The Problem Statement

Most people save data in local storage. Is this a safe method to store data? No! Local storage writes the data as a plain string, and anyone who has access to the device can read and manipulate this data.

Many people think that encrypting the data and saving it in local storage is secure. However, in this case, you need a secure key to decrypt the data.

Let's consider this **scenario**: You have encrypted the user's login information and saved it in local storage. When the platform reloads, you decrypt the data from local storage and mark the user as logged in or out. Here, your website shares a common secure key to encrypt and decrypt, which means only your website knows how to decrypt.

In this case, if someone copies the data from local storage and pastes it into a different browser, then loads your website, your website will authenticate the user. Why? Because your website knows how to decrypt the data!

This is the problem when you have a single secure key. **So how do we solve this issue?**

## Why React Secure Storage?

React Secure Storage was created to securely write data to local storage (**it's a wrapper written on top of default localStorage to securely write data to localStorage**). This secure storage library generates a secure key for every browser and encrypts the data using this key, which means only the browser that encrypted the data can decrypt it.

Additionally, React Secure Storage preserves the data format for every data type. Out of the box, it supports the following data types:

**String | Object | Number | Boolean**

This means you don't need to explicitly convert every data type to a string.

## How Does It Work?

React Secure Storage is written in the Singleton design pattern. When the library is initialized, it reads all the data from local storage, decrypts all the data written using react-secure-storage, and keeps it in memory. This ensures faster reading of all the data.

The key is generated using a browser fingerprint, which is created using 10+ browser key identifiers and a user-specific secure key.

The user-specific secure key can be configured using a .env file as follows:

```
SECURE_LOCAL_STORAGE_HASH_KEY=xxxxxxxxx
```

The secure local storage prefix can be configured using a .env file as follows:

```
SECURE_LOCAL_STORAGE_PREFIX=xxxxxxx
```

### Here are the .env prefix lists for the supported languages that are built-in.

| Language | Prefix       |
|----------|--------------|
| React    | REACT_APP_   |
| Vite     | VITE_        |
| Next.js  | NEXT_PUBLIC_ |

You can always use the environment variables without the prefix as well.

## How to Use

To use the library, first, you need to install it using:

```
yarn add react-secure-storage
```

or

```
npm install react-secure-storage
```

You can use the following methods to read and write items to secure local storage:

| Function              | Use Case                                          | Data Type                       |
|-----------------------|---------------------------------------------------|---------------------------------|
| `setItem(key, value)` | To set values in secure storage                   | Supports `String - Object - Number - Boolean` as value |
| `getItem(key)`        | To get values saved in secure local storage       | Returns null if the key does not exist |
| `removeItem(key)`     | To remove a specified key from secure local storage |                                 |
| `clear()`             | Removes all data from secure local storage        |                                 |

## How to Use with Vite

In the latest version of Vite, process is not defined by default; it uses `import.meta.env`.

To define the process, you need to add the following code inside `vite.config.ts`:

```js
import { defineConfig } from 'vite'
// ...
export default defineConfig({
  // ...
  define: {
    "process.env": {},
  },
})
```

Here you can pass all the required `ENV` variables supported by the library inside the process.env object.

## To Disable Properties from Key Generation

If you wish to disable any of the key generation properties, you can do so as follows:

```
SECURE_LOCAL_STORAGE_DISABLED_KEYS=ScreenPrint|Plugins
```

or

```
REACT_APP_SECURE_LOCAL_STORAGE_DISABLED_KEYS=ScreenPrint|Plugins
```

Here is the list of all the supported values: `UserAgent|ScreenPrint|Plugins|Fonts|LocalStorage|SessionStorage|TimeZone|Language|SystemLanguage|Cookie|Canvas|Hostname`

> We strongly recommend not disabling any of the properties, as more properties you have, the more unique the browser fingerprint will be!

### How to Use Environment Variables for the Supported Languages

| Language | Key                                            | Usage                                                                            |
|----------|------------------------------------------------|----------------------------------------------------------------------------------|
| Default  | SECURE_LOCAL_STORAGE_HASH_KEY                  | Used to specify the user-specific hash key                                        |
| Default  | SECURE_LOCAL_STORAGE_PREFIX                    | Used to change the local storage prefix where the data will be finally saved     |
| Default  | SECURE_LOCAL_STORAGE_DISABLED_KEYS             | Used to disable individual properties from encryption/fingerprint key generation |
| React    | REACT_APP_SECURE_LOCAL_STORAGE_HASH_KEY        | Used to specify the user-specific hash key                                        |
| React    | REACT_APP_SECURE_LOCAL_STORAGE_PREFIX          | Used to change the local storage prefix where the data will be finally saved     |
| React    | REACT_APP_SECURE_LOCAL_STORAGE_DISABLED_KEYS   | Used to disable individual properties from encryption/fingerprint key generation |
| Vite     | VITE_SECURE_LOCAL_STORAGE_HASH_KEY             | Used to specify the user-specific hash key                                        |
| Vite     | VITE_SECURE_LOCAL_STORAGE_PREFIX               | Used to change the local storage prefix where the data will be finally saved     |
| Vite     | VITE_SECURE_LOCAL_STORAGE_DISABLED_KEYS        | Used to disable individual properties from encryption/fingerprint key generation |
| Next.js  | NEXT_PUBLIC_SECURE_LOCAL_STORAGE_HASH_KEY      | Used to specify the user-specific hash key                                        |
| Next.js  | NEXT_PUBLIC_SECURE_LOCAL_STORAGE_PREFIX        | Used to change the local storage prefix where the data will be finally saved     |
| Next.js  | NEXT_PUBLIC_SECURE_LOCAL_STORAGE_DISABLED_KEYS | Used to disable individual properties from encryption/fingerprint key generation |

## Sample Code

```js
import { useEffect } from "react";
import secureLocalStorage from "react-secure-storage";

const App = () => {
  useEffect(() => {
    secureLocalStorage.setItem("object", {
      message: "This is testing of local storage",
    });
    secureLocalStorage.setItem("number", 12);
    secureLocalStorage.setItem("string", "12");
    secureLocalStorage.setItem("boolean", true);
    let value = secureLocalStorage.getItem("boolean");
  }, []);

  return (
    <div>
      This is a sample code
    </div>
  );
}

export default App;
```

## Build Size: 7.6KB

## What's New in 1.3.2?

Regular bug fixes and resolution of issue [#39](https://github.com/sushinpv/react-secure-storage/issues/39).

## What's New | Previous?

- Added support for Vite and Next.js environment variables.
- Now you can disable individual fingerprint generation properties, discussed in enhancement [#14](https://github.com/sushinpv/react-secure-storage/issues/14).
- Fixed secure token returning null when the browser resizes. This was previously included as a security feature but was removed in the latest update, covered in issue [#9](https://github.com/sushinpv/react-secure-storage/issues/9).
- Included the browser hostname in the secure key to make it more unique, ensuring each website's key is distinct.
- Added support for updating the local storage prefix, configurable using .env.
- Resolved issue [#2](https://github.com/sushinpv/react-secure-storage/issues/2).
- Added support for `Cypress`.
- Added proper type definitions for the entire package.
- Added support for older ES versions and Next.js.
- Released the first version of React Secure Local Storage, supporting `setItem`, `getItem`, `removeItem`, and `clear` functions.

## How Do I Test This Library on My Local System & How Do I Contribute?

For local testing, make sure you install `react-scripts` using `npm i react-scripts` or `yarn add react-scripts`. The react-scripts were removed due to a vulnerability issue highlighted [here](https://github.com/sushinpv/react-secure-storage/issues/3).

To contribute to the library, create a development branch for your fix as `dev/{feature/fix}` and create a PR to the master branch.

Before creating the PR, please remove `react-scripts` from the `package.json` and create a production build for the library by running `yarn build:lib`.

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=sushinpv/react-secure-storage&type=Date)](https://star-history.com/#sushinpv/react-secure-storage&Date)
