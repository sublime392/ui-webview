<!-- ⚠️ This README has been generated from the file(s) "blueprint.md" ⚠️-->This monorepo contains multiple packages:<br><br><details>
<summary><b>webview</b></summary>
﻿# @nativescript-community/ui-webview

Extended WebView for NativeScript which adds many options.
The code is originally based on [nativescript-webview-ext](https://github.com/m-abs/nativescript-webview-ext)


[](#features)

## Features
* Adds a custom-scheme handler for x-local:// to the webview for loading of resources inside the webview.
    * Note: For iOS 11+ WKWebView is used, but for iOS <11 UIWebView is used
* Adds support for capturing URLs.
    *  This allows the app to open external links in an external browser and handle tel-links
* Added functions like:
    - `executeJavaScript(code: string)` for executing JavaScript-code and getting result.
    - `executePromise(code: string)` for calling promises and getting the result.
    - `getTitle()` returns document.title.
* Adds functions to inject `css`- and `javascript`-files.
    * Into the current page.
    * Auto-injected on page load.

* Supports:
    * Android 19+
    * iOS 9+


[](#installation)

## Installation

Describe your plugin installation steps. Ideally it would be something like:

```bash
tns plugin add @nativescript-community/ui-webview
```

### Angular support

Import `AWebViewModule` from `@nativescript-community/ui-webview/angular` and add it to your `NgModule`.

This registers the element `AWebView`. Replace the <WebView> tag with <AWebView>

### Vue support

```js
import Vue from 'nativescript-vue';
import WebViewPlugin from '@nativescript-community/ui-webview/vue';

Vue.use(WebViewPlugin);
```
This registers the element `AWebView`. Replace the <WebView> tag with <AWebView>



[](#usage)

## Usage


[](#limitations)

## Limitations

In order to intercept requests for the custom scheme, we use `UIWebView` for iOS 9 and 10 and `WKWebView` for iOS 11+.

iOS 11 added support for setting a `WKURLSchemeHandler` on the `WKWebView`.
Prior to iOS 11 there isn't support for intercepting the URL with `WKWebView`, so we use a custom `NSURLProtocol` + `UIWebView`.

### Important:
The custom `NSURLProtocol` used with UIWebView is shared with all instances of the AWebView, so mapping `x-local://local-filename.js` => `file://app/full/path/local-filename.js` is shared between them.


[](#api)

## API

### NativeScript View

| Property | Value | Description |
| --- | --- | --- |
| readonly isUIWebView | true / false | Is the native webview an UIWebView? True if `iOS <11` |
| readonly isWkWebView | true / false | Is the native webview an WKWebView? True if `iOS >=11` |
| src | | Load src |
| autoInjectJSBridge | true / false | Should the window.nsWebViewBridge be injected on `loadFinishedEvent`? Defaults to true |
| builtInZoomControls | true / false | Android: Is the built-in zoom mechanisms being used |
| cacheMode | default / no_cache / cache_first / cache_only | Android: Set caching mode. |
| databaseStorage | true / false | Android: Enable/Disabled database storage API. Note: It affects all webviews in the process. |
| debugMode | true / false | Android: Enable chrome debugger for webview on Android. Note: Applies to all webviews in App |
| displayZoomControls | true / false | Android: displays on-screen zoom controls when using the built-in zoom mechanisms |
| domStorage | true / false | Android: Enable/Disabled DOM Storage API. E.g localStorage |
| scalesPageToFit | UIWebView: Should webpage scale to fit the view? Defaults to false |
| scrollBounce | true / false | iOS: Should the scrollView bounce? Defaults to true. |
| supportZoom | true / false | Android: should the webview support zoom |
| viewPortSize | false / view-port string / ViewPortProperties | Set the viewport metadata on load finished. **Note:** WkWebView sets initial-scale=1.0 by default. |
| limitsNavigationsToAppBoundDomains | false | iOS: allows to enable Service Workers **Note:** If set to true, WKAppBoundDomains also should be set in info.plist. |
| scrollBarIndicatorVisible | false | Allow to hide scrollbars. |

| Function | Description |
| --- | --- |
| loadUrl(src: string): Promise<LoadFinishedEventData> | Open a URL and resolves a promise once it has finished loading. |
| registerLocalResource(resourceName: string, path: string): void; | Map the "x-local://{resourceName}" => "{path}". |
| unregisterLocalResource(resourceName: string): void; | Removes the mapping from "x-local://{resourceName}" => "{path}" |
| getRegisteredLocalResource(resourceName: string): void; | Get the mapping from "x-local://{resourceName}" => "{path}" |
| loadJavaScriptFile(scriptName: string, filepath: string) | Inject a javascript-file into the webview. Should be called after the `loadFinishedEvent` |
| loadStyleSheetFile(stylesheetName: string, filepath: string, insertBefore: boolean) | Loads a CSS-file into document.head. If before is true, it will be added to the top of document.head otherwise as the last element |
| loadJavaScriptFiles(files: {resourceName: string, filepath: string}[]) | Inject multiple javascript-files into the webview. Should be called after the `loadFinishedEvent` |
| loadStyleSheetFiles(files: {resourceName: string, filepath: string, insertBefore: boolean}[]) | Loads multiple CSS-files into the document.head. If before is true, it will be added to the top of document.head otherwise as the last element |
| autoLoadJavaScriptFile(resourceName: string, filepath: string) | Register a JavaScript-file to be injected on `loadFinishedEvent`. If a page is already loaded, the script will be injected into the current page. |
| autoLoadStyleSheetFile(resourceName: string, filepath: string, insertBefore?: boolean) | Register a CSS-file to be injected on `loadFinishedEvent`. If a page is already loaded, the CSS-file will be injected into the current page. |
| autoExecuteJavaScript(scriptCode: string, name: string) | Execute a script on `loadFinishedEvent`. The script can be a promise |
| executeJavaScript(scriptCode: string) | Execute JavaScript in the webpage. *Note:* scriptCode should be ES5 compatible, or it might not work on 'iOS < 11' |
| executePromise(scriptCode: string, timeout: number = 500) | Run a promise inside the webview. *Note:* Executing scriptCode must return a promise. |
| emitToWebView(eventName: string, data: any) | Emit an event to the webview. Note: data must be stringify'able with JSON.stringify or this throws an exception. |
| getTitle() | Returns a promise with the current document title. |


[](#events)

## Events
| Event | Description |
| --- | --- |
| loadFinished | Raised when a loadFinished event occurs. args is a `LoadFinishedEventData` |
| loadProgress | Android only: Raised during page load to indicate the progress. args is a `LoadProgressEventData` |
| loadStarted | Raised when a loadStarted event occurs. args is a `LoadStartedEventData` |
| shouldOverrideUrlLoading | Raised before the webview requests an URL. Can cancelled by setting args.cancel = true in the `ShouldOverrideUrlLoadEventData` |
| titleChanged | Document title changed |
| webAlert | Raised when `window.alert` is triggered inside the webview, needed to use customs dialogs for web alerts. args in a `WebAlertEventData`. `args.callback()` must be called to indicate alert is closed. **NOTE:** Not supported by UIWebView |
| webConfirm | Raised when `window.confirm` is triggered inside the webview, needed to use customs dialogs for web confirm boxes. args in a `webConfirmEvent`. `args.callback(boolean)` must be called to indicate confirm box is closed. **NOTE:** Not supported by UIWebView |
| webConsole | Android only: Raised when a line is added to the web console. args is a `WebConsoleEventData`. |
| webPrompt | Raised when `window.prompt` is triggered inside the webview, needed to use customs dialogs for web prompt boxes. args in a `webConfirmEvent`. `args.callback(string | null)` must be called to indicate prompt box is closed. **NOTE:** Not supported by UIWebView |
| Events emitted from the webview | Raised when nsWebViewBridge.emit(...) is called inside the webview. args in an `WebViewEventData` |

### WebView

Inside the WebView we have the `nsWebViewBridge` for sending events between the `NativeScript`-layer and the `WebView`.
**Note:** The bridge will only be available `DOMContentLoaded` or `onload` inside the WebView.

| Function | Description |
| --- | --- |
| window.nsWebViewBridge.on(eventName: string, cb: (data: any) => void) | Registers handlers for events from the native layer. |
| window.nsWebViewBridge.off(eventName: string, cb?: (data: any) => void) | Unregister handlers for events from the native layer. |
| window.nsWebViewBridge.emit(eventName: string, data: any) | Emits event to NativeScript layer. Will be emitted on the AWebView as any other event, data will be a part of the WebViewEventData-object |


[](#possible-features-to-come)

## Possible features to come:

* Cookie helpers?
* Setting view-port metadata?
* Share cache with native-layer?

### Android
* Settings
    * AppCache?
    * User agent?

#### iOS
* Settings?


[](#demo-and-unit-tests)

## Demo and unit tests

### Running the demo

To run the demo-project, the plugin must be build locally and a http-server must be running.

The easiest way to run the demo is to follow these steps:
- Clone the git repository from https://github.com/nativescript-community/ui-webview.git
- Go into the `src`-folder
- Use the npm-scripts:
  - npm run demo.ios
  - npm run demo.android

### Running the unit-tests

- Clone the git repository from https://github.com/nativescript-community/ui-webview.git
- Go into the `src`-folder
- Use the npm-scripts:
  - npm run test.ios
  - npm run test.android


[](#license)

## License

Apache License Version 2.0, January 2004


[](#about-nota)

## About Nota

Nota is the Danish Library and Expertise Center for people with print disabilities.
To become a member of Nota you must be able to document that you cannot read ordinary printed text. Members of Nota are visually impaired, dyslexic or otherwise impaired.
Our purpose is to ensure equal access to knowledge, community participation and experiences for people who're unable to read ordinary printed text.

</details><details>
<summary><b>webview-rtc</b></summary>

[](#nativescript-webview-rtc)

# NativeScript WebView RTC
[![npm downloads](https://img.shields.io/npm/dm/@nativescript-community/ui-webview-rtc.svg)](https://www.npmjs.com/package/@nativescript-community/ui-webview-rtc)
[![npm downloads](https://img.shields.io/npm/dt/@nativescript-community/ui-webview-rtc.svg)](https://www.npmjs.com/package/@nativescript-community/ui-webview-rtc)
[![npm](https://img.shields.io/npm/v/@nativescript-community/ui-webview-rtc.svg)](https://www.npmjs.com/package/@nativescript-community/ui-webview-rtc)

A NativeScript Plugin to add webRTC support to `@nativescript-community/ui-webview`


[](#installation)

## Installation
Run the following command from the root of your project:

`tns plugin add @nativescript-community/ui-webview-rtc`

This command automatically installs the necessary files, as well as stores @nativescript-community/ui-webview-rtc as a dependency in your project's package.json file.


[](#configuration)

## Configuration

To install the plugin run 
```typescript
import install from '@nativescript-community/ui-webview-rtc';
install();
```

then simply use the `webRTC="true"` as a webview property

</details>

[](#demos-and-development)

## Demos and Development


### Repo Setup

The repo uses submodules. If you did not clone with ` --recursive` then you need to call
```
git submodule update --init
```

The package manager used to install and link dependencies must be `pnpm` or `yarn`. `npm` wont work.

To develop and test:
if you use `yarn` then run `yarn`
if you use `pnpm` then run `pnpm i`

**Interactive Menu:**

To start the interactive menu, run `npm start` (or `yarn start` or `pnpm start`). This will list all of the commonly used scripts.

### Build

```bash
npm run build.all
```
WARNING: it seems `yarn build.all` wont always work (not finding binaries in `node_modules/.bin`) which is why the doc explicitly uses `npm run`

### Demos

```bash
npm run demo.[ng|react|svelte|vue].[ios|android]

npm run demo.svelte.ios # Example
```

Demo setup is a bit special in the sense that if you want to modify/add demos you dont work directly in `demo-[ng|react|svelte|vue]`
Instead you work in `demo-snippets/[ng|react|svelte|vue]`
You can start from the `install.ts` of each flavor to see how to register new demos 


[](#contributing)

## Contributing

### Update repo 

You can update the repo files quite easily

First update the submodules

```bash
npm run update
```

Then commit the changes
Then update common files

```bash
npm run sync
```
Then you can run `yarn|pnpm`, commit changed files if any

### Update readme 
```bash
npm run readme
```

### Update doc 
```bash
npm run doc
```

### Publish

The publishing is completely handled by `lerna` (you can add `-- --bump major` to force a major release)
Simply run 
```shell
npm run publish
```

### modifying submodules

The repo uses https:// for submodules which means you won't be able to push directly into the submodules.
One easy solution is t modify `~/.gitconfig` and add
```
[url "ssh://git@github.com/"]
	pushInsteadOf = https://github.com/
```

[](#questions)

## Questions

If you have any questions/issues/comments please feel free to create an issue or start a conversation in the [NativeScript Community Discord](https://nativescript.org/discord).