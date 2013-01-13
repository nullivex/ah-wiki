# File Server

## General

actionHero comes with a file server which clients can make use of to request files on the actionHero server.  actionHero is not meant to be a 'rendering' server (like express or rails), but can serve static files.

When using the fileserver within an action, be sure to respond with `toContinue` as `false`, as the file server will have already returned headers and content down to the client (IE: `next(connection, false)`).  Yes, you still need to invoke the callback.

You can use the method `api.fileServer.deliver(connection, next)` to send files to clients.  The callback will receive `(connection, toContinue)`.  `toContinue` should always be `false`.  `connection.params.fileName` is used to lookup the file requested.  If the file is not found, the error defined in `api.configData.general.flatFileNotFoundMessage` will be rendered

If a directory is requested rather than a file, actionHero will look for the file in that directory defined by `api.configData.commonWeb.directoryFileType` (which defaults to `index.html`).  Failing to find this file, an error will be returned defined in `api.configData.general.flatFileIndexPageNotFoundMessage`

On .nix operating system's symlinks for both files and folders will be followed. 

## Web Clients

- `Cache-Control` and `Expires` headers will be sent, as defined by `api.configData.commonWeb.flatFileCacheDuration`
- Content-Types for files will attempt to be determined using the [mime package](https://npmjs.org/package/mime)
- web clients may request `connection.params.fileName` directly within an action which makes use of  `api.sendFile`, or if they are  under the `api.configData.commonWeb.urlPathForFiles` route, the file will be looked up as if the route matches the directory structure under `flatFileDirectory`.

## Non-web Clients

- file data is sent `raw`, and is likely to contain binary content and line breaks.  Parse your responses accordingly! 