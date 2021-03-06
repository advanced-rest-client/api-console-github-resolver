# api-console-github-resolver

[![Build Status](https://travis-ci.org/mulesoft-labs/api-console-github-resolver.svg?branch=master)](https://travis-ci.org/mulesoft-labs/api-console-github-resolver)

A npm module to get information about Mulesoft's API console release.

This module is mainly used in the [@api-components/api-console-builder](https://github.com/mulesoft-labs/api-console-builder).

## API

Shorthand functions:

-   `latestInfo(logger)` -> `new GithubResolver#getLatestInfo()`
-   `tagInfo(tag, logger)` -> `new GithubResolver#getTagInfo(tag)`
-   `releasesInfo(logger)` -> `new GithubResolver#getReleasesList()`

The module exposes 4 classes:

-   [Transport](lib/Transport.js)
-   [GithubResolver](lib/GithubResolver.js)
-   [GithubResolverOptions](lib/GithubResolverOptions.js)
-   [GithubCache](lib/GithubCache.js)

### Example

```javascript
import { latestInfo } from '@api-components/api-console-github-resolver';

const winstonLogger = new winston.createLogger({...});
const info = await latestInfo(winstonLogger);
console.log(info);
```

equivalent to

```javascript
import { GithubResolver }  from '@api-components/api-console-github-resolver';

const resolver = new GithubResolver({
  logger: winstonLogger
});
const info = await resolver.getLatestInfo();
console.log(info);
```

### Transport

GitHub transport class.
The transport is based on the HTTPS protocol.

#### `get(resource, headers)`

Gets a resource from given location. This function fallows redirects.

**resource** `String` - URL to the resource.

**headers** `Object` - A list of headers to send.

##### Returns `<Promise>`

A promise resolved to a JavaScript `Object` if compatible content type is detected or to `Buffer` otherwise.

#### Example

```javascript
import { Transport } from '@api-components/api-console-github-resolver';

const winstonLogger = new winston.createLogger({...});
const transport = new Transport(winstonLogger);
const response = await transport.get('https://...', {'etag': 'abc'});
console.log(response);
```

### GithubResolver

A class to resolve GitHub repositories versions. It allows to get latest release
version and the url to the release's zip file or list available versions.

#### `getLatestInfo()`

Gets information about latest release.

##### Returns `<Promise>`

Promise resolved to a JavaScript object with the response from GitHub.

#### `getReleasesList()`

Gets information about releases.
GitHub allows 30 items per page by default and this is exactly how much you
will get when calling this function.

##### Returns `<Promise>`

Promise resolves to an array of releases information.

#### `getTagInfo(tag)`

Gets information about tagged release.

**tag** `String` - Release tag name

##### Returns `<Promise>`

Promise resolved to an `Object` with release information.

## GitHub rate limit

GitHub allows to make up to 60 requests per hour. To increate the limit you can
use [GitHub personal token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) as an option passed to the `ApiConsoleGithubResolver` class constructor.

### Example

```javascript
import { GithubResolver } from '@api-components/api-console-github-resolver';

const TOKEN = 'ABC';
const resolver = new GithubResolver({
  token: TOKEN
});
const info = await resolver.getLatestInfo();
console.log(info);
```

Module's shorthand functions reads `GITHUB_TOKEN` environmental variable and
sets it as a configuration option by default.

### Example

```javascript
// index.js
import { latestInfo } from '@api-components/api-console-github-resolver';

const info = await latestInfo();
console.log(info);
```

```shell
$ export GITHUB_TOKEN="ABC"
$ node index.js
```
