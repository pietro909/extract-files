# extract-files

[![npm version](https://badgen.net/npm/v/extract-files)](https://npm.im/extract-files) [![CI status](https://github.com/jaydenseric/extract-files/workflows/CI/badge.svg)](https://github.com/jaydenseric/extract-files/actions)

Clones a value, recursively extracting [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File), [`Blob`](https://developer.mozilla.org/en-US/docs/Web/API/Blob) and [`ReactNativeFile`](#class-reactnativefile) instances with their [object paths](#type-objectpath), replacing them with `null`. [`FileList`](https://developer.mozilla.org/en-US/docs/Web/API/Filelist) instances are treated as [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File) instance arrays.

Used by [GraphQL multipart request spec client implementations](https://github.com/jaydenseric/graphql-multipart-request-spec#implementations) such as [`graphql-react`](https://npm.im/graphql-react) and [`apollo-upload-client`](https://npm.im/apollo-upload-client).

## Installation

To install with [npm](https://npmjs.com/get-npm), run:

```sh
npm install extract-files
```

See the documentation for the function [`extractFiles`](#function-extractfiles) to get started.

## Requirements

- [Node.js](https://nodejs.org): `^12.22.0 || ^14.17.0 || >= 16.0.0`
- [Browsers](https://npm.im/browserslist): `> 0.5%, not OperaMini all, not IE > 0, not dead`
- [React Native](https://reactnative.dev)

## API

- [class ReactNativeFile](#class-reactnativefile)
- [function extractFiles](#function-extractfiles)
- [function isExtractableFile](#function-isextractablefile)
- [type ExtractableFile](#type-extractablefile)
- [type ExtractableFileMatcher](#type-extractablefilematcher)
- [type ExtractFilesResult](#type-extractfilesresult)
- [type ObjectPath](#type-objectpath)
- [type ReactNativeFileSubstitute](#type-reactnativefilesubstitute)

### class ReactNativeFile

Used to mark a [React Native `File` substitute](#type-reactnativefilesubstitute) in an object tree for [`extractFiles`](#function-extractfiles). It’s too risky to assume all objects with `uri`, `type` and `name` properties are files to extract.

| Parameter | Type | Description |
| :-- | :-- | :-- |
| `file` | [ReactNativeFileSubstitute](#type-reactnativefilesubstitute) | A [React Native](https://reactnative.dev) [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File) substitute. |

#### Examples

_How to import._

> ```js
> import ReactNativeFile from "extract-files/ReactNativeFile.mjs";
> ```

_An extractable file in [React Native](https://reactnative.dev)._

> ```js
> const file = new ReactNativeFile({
>   uri: uriFromCameraRoll,
>   name: "a.jpg",
>   type: "image/jpeg",
> });
> ```

---

### function extractFiles

Clones a value, recursively extracting [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File), [`Blob`](https://developer.mozilla.org/en-US/docs/Web/API/Blob) and [`ReactNativeFile`](#class-reactnativefile) instances with their [object paths](#type-objectpath), replacing them with `null`. [`FileList`](https://developer.mozilla.org/en-US/docs/Web/API/Filelist) instances are treated as [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File) instance arrays.

| Parameter | Type | Description |
| :-- | :-- | :-- |
| `value` | \* | Value (typically an object tree) to extract files from. |
| `path` | [ObjectPath](#type-objectpath)? = `''` | Prefix for object paths for extracted files. |
| `isExtractableFile` | [ExtractableFileMatcher](#type-extractablefilematcher)? = [isExtractableFile](#function-isextractablefile) | The function used to identify extractable files. |

**Returns:** [ExtractFilesResult](#type-extractfilesresult) — Result.

#### Examples

_How to import._

> ```js
> import extractFiles from "extract-files/extractFiles.mjs";
> ```

_Extract files from an object._

> For the following:
>
> ```js
> const file1 = new File(["1"], "1.txt", { type: "text/plain" });
> const file2 = new File(["2"], "2.txt", { type: "text/plain" });
> const value = {
>   a: file1,
>   b: [file1, file2],
> };
>
> const { clone, files } = extractFiles(value, "prefix");
> ```
>
> `value` remains the same.
>
> `clone` is:
>
> ```json
> {
>   "a": null,
>   "b": [null, null]
> }
> ```
>
> `files` is a [`Map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) instance containing:
>
> | Key     | Value                        |
> | :------ | :--------------------------- |
> | `file1` | `["prefix.a", "prefix.b.0"]` |
> | `file2` | `["prefix.b.1"]`             |

---

### function isExtractableFile

Checks if a value is an [extractable file](#type-extractablefile).

**Type:** [ExtractableFileMatcher](#type-extractablefilematcher)

| Parameter | Type | Description     |
| :-------- | :--- | :-------------- |
| `value`   | \*   | Value to check. |

**Returns:** boolean — Is the value an [extractable file](#type-extractablefile).

#### Examples

_How to import._

> ```js
> import isExtractableFile from "extract-files/isExtractableFile.mjs";
> ```

---

### type ExtractableFile

An extractable file.

**Type:** File | Blob | [ReactNativeFile](#class-reactnativefile)

---

### type ExtractableFileMatcher

A function that checks if a value is an [extractable file](#type-extractablefile).

**Type:** Function

| Parameter | Type | Description     |
| :-------- | :--- | :-------------- |
| `value`   | \*   | Value to check. |

**Returns:** boolean — Is the value an [extractable file](#type-extractablefile).

#### See

- [`isExtractableFile`](#function-isextractablefile) is the default extractable file matcher.

#### Examples

_How to check for the default exactable files, as well as a custom type of file._

> ```js
> import isExtractableFile from "extract-files/isExtractableFile.mjs";
>
> const isExtractableFileEnhanced = (value) =>
>   isExtractableFile(value) ||
>   (typeof CustomFile !== "undefined" && value instanceof CustomFile);
> ```

---

### type ExtractFilesResult

What [`extractFiles`](#function-extractfiles) returns.

**Type:** object

| Property | Type | Description |
| :-- | :-- | :-- |
| `clone` | \* | Clone of the original input value with files recursively replaced with `null`. |
| `files` | Map<[ExtractableFile](#type-extractablefile), Array<[ObjectPath](#type-objectpath)>> | Extracted files and their locations within the original value. |

---

### type ObjectPath

String notation for the path to a node in an object tree.

**Type:** string

#### See

- [`object-path` on npm](https://npm.im/object-path).

#### Examples

_Object path is property `a`, array index `0`, object property `b`._

>     a.0.b

---

### type ReactNativeFileSubstitute

A [React Native](https://reactnative.dev) [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File) substitute for when using [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData).

**Type:** object

| Property | Type    | Description        |
| :------- | :------ | :----------------- |
| `uri`    | string  | Filesystem path.   |
| `name`   | string? | File name.         |
| `type`   | string? | File content type. |

#### See

- [React Native `FormData` polyfill source](https://github.com/facebook/react-native/blob/v0.45.1/Libraries/Network/FormData.js#L34).

#### Examples

_A camera roll file._

> ```js
> const fileSubstitute = {
>   uri: uriFromCameraRoll,
>   name: "a.jpg",
>   type: "image/jpeg",
> };
> ```
