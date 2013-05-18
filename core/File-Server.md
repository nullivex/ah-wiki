# File Server

## General

actionHero comes with a file server which clients can make use of to request files on the actionHero server.  actionHero is not meant to be a 'rendering' server (like express or rails), but can serve static files.

If a directory is requested rather than a file, actionHero will look for the file in that directory defined by `api.configData.commonWeb.directoryFileType` (which defaults to `index.html`).  Failing to find this file, an error will be returned defined in `api.configData.general.flatFileIndexPageNotFoundMessage`

You can use the `api.staticFile.get(connection, next)` in your actions (where `next(connection, error, fileStream, mime, length)`).  Note that fileStream is a stream which can be `pipe`d to a client.  You can use this in actions if you wish, but be sure to send `next(connection, false)` because you will have already rendered a response.

On .nix operating system's symlinks for both files and folders will be followed. 

## Web Clients

- `Cache-Control` and `Expires` headers will be sent, as defined by `api.configData.commonWeb.flatFileCacheDuration`
- Content-Types for files will attempt to be determined using the [mime package](https://npmjs.org/package/mime)
- web clients may request `connection.params.file` directly within an action which makes use of  `api.sendFile`, or if they are  under the `api.configData.servers.web.urlPathForFiles` route, the file will be looked up as if the route matches the directory structure under `flatFileDirectory`.

## Non-web Clients

- the param `file` should be used to request a path
- file data is sent `raw`, and is likely to contain binary content and line breaks.  Parse your responses accordingly! 