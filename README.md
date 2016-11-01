# Electron Release Server
[![GitHub stars](https://img.shields.io/github/stars/ArekSredzki/electron-release-server.svg)](https://github.com/ArekSredzki/electron-release-server/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/ArekSredzki/electron-release-server.svg)](https://github.com/ArekSredzki/electron-release-server/network)
[![Join the chat at https://gitter.im/ArekSredzki/electron-release-server](https://badges.gitter.im/ArekSredzki/electron-release-server.svg)](https://gitter.im/ArekSredzki/electron-release-server?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
>A node web server which serves & manages releases of the your [Electron](http://electron.atom.io) App, and is fully compatible with [Squirrel](https://github.com/Squirrel) Auto-updater (which is built into Electron).

[![Electron Release Server Demo](https://j.gifs.com/wpyY1X.gif)](https://youtu.be/lvT7rfB01iA)

_Note: Despite being advertised as a release server for Electron applications, it would work for **any application using Squirrel**._

If you host your project on your Github **and** do not need a UI for your app, then [Nuts](https://github.com/GitbookIO/nuts) is probably what you're looking for. Otherwise, you're in the same boat as I was, and you've found the right place!

## Features
- :sparkles: Awesome release management interface powered by [AngularJS](https://angularjs.org)
    - Authenticates with LDAP, easy to modify to another authentication method if needed
- :sparkles: Store assets on server disk, or Amazon S3 (with minor modifications)
    - Use pretty much any database for persistence, thanks to [Sails](http://sailsjs.org) & [Waterline](http://waterlinejs.org)
- :sparkles: Simple but powerful download urls (**NOTE:** when no assets are uploaded, server returns `404` by default):
    - `/download/latest`
    - `/download/latest/:platform`
    - `/download/:version`
    - `/download/:version/:platform`
    - `/download/:version/:platform/:filename`
    - `/download/channel/:channel`
    - `/download/channel/:channel/:platform`
- :sparkles: Support pre-release channels (`beta`, `alpha`, ...)
- :sparkles: Auto-updates with [Squirrel](https://github.com/Squirrel):
    - Update URLs provided: `/update/:platform/:version[/:channel]`
    - Mac uses `*.dmg` and `*.zip`
    - Windows uses `*.exe` and `*.nupkg`
- :sparkles: Serve the perfect type of assets: `.zip` for Squirrel.Mac, `.nupkg` for Squirrel.Windows, `.dmg` for Mac users, ...
- :sparkles: Release notes endpoint
    - `/notes/:version`

**NOTE:** if you don't provide the appropriate type of file for Squirrel you won't be able to update your app since the update endpoint will not return a JSON. (`.zip` for Squirrel.Mac, `.nupkg` for Squirrel.Windows).

## Deploy it / Start it

[Follow our guide to deploy Electron Release Server](docs/deploy.md).

## Auto-updater / Squirrel

This server provides an endpoint for [Squirrel auto-updater](https://github.com/atom/electron/blob/master/docs/api/auto-updater.md), it supports both [OS X](docs/update-osx.md) and [Windows](docs/update-windows.md).

## Documentation
[Check out the documentation](docs/) for more details.

## Building Releases
I highly recommend using [electron-builder](https://github.com/loopline-systems/electron-builder) for packaging & releasing your applications. Once you have built your app with that, you can upload the artifacts for your users right away!

## Maintenance
You should keep your fork up to date with the electron-release-server master.

Doing so is simple, rebase your repo using the commands below.
```bash
git remote add upstream https://github.com/ArekSredzki/electron-release-server.git
git fetch upstream
git rebase upstream/master
git pull
git push -u origin master 
```

## Some issues which I face during deployment
1. Uploading assets - xyzrelease.com/api/asset gives 404 error
    - Solution:Fixed by adding following in iisnode web.config
```
 <security>
        <requestFiltering>
          <requestLimits maxAllowedContentLength="524288000"/>
        </requestFiltering>
      </security>
``` 
2. Failed: Error during WebSocket handshake: Unexpected response code: 400
    - Error 
```
"WebSocket connection to 'wss://electronreleaseserver.xyz.abc.edu/socket.io/?__sails_io_sdk_version=0…tform=browser&__sails_io_sdk_language=javascript&EIO=3&transport=websocket' failed: Error during WebSocket handshake: Unexpected response code: 400
sails.io.js:438  
        Socket is trying to reconnect to Sails...
_-|>_-  (attempt #5)
```
- Solution: [from original repo](https://github.com/ArekSredzki/electron-release-server/issues/40)

3. A hook ('orm') failed to load!
```
error: A hook (`orm`) failed to load!
error: error: password authentication failed for user "electron_release_server_user"
    at Connection.parseE (D:\Project\electron-release-server\node_modules\sails-postgresql\node_modules\pg\lib\connection.js:539:11)
    at Connection.parseMessage (D:\Project\electron-release-server\node_modules\sails-postgresql\node_modules\pg\lib\connection.js:366:17)
    at Socket.<anonymous> (D:\Project\electron-release-server\node_modules\sails-postgresql\node_modules\pg\lib\connection.js:105:22)
    at emitOne (events.js:96:13)
    at Socket.emit (events.js:188:7)
    at readableAddChunk (_stream_readable.js:172:18)
    at Socket.Readable.push (_stream_readable.js:130:10)
    at TCP.onread (net.js:542:20) { error: password authentication failed for user "electron_release_server_user"
    at Connection.parseE (D:\Project\electron-release-server\node_modules\sails-postgresql\node_modules\pg\lib\connection.js:539:11)
    at Connection.parseMessage (D:\Project\electron-release-server\node_modules\sails-postgresql\node_modules\pg\lib\connection.js:366:17)
    at Socket.<anonymous> (D:\Project\electron-release-server\node_modules\sails-postgresql\node_modules\pg\lib\connection.js:105:22)
    at emitOne (events.js:96:13)
    at Socket.emit (events.js:188:7)
    at readableAddChunk (_stream_readable.js:172:18)
    at Socket.Readable.push (_stream_readable.js:130:10)
    at TCP.onread (net.js:542:20)
  name: 'error',
  length: 117,
  severity: 'FATAL',
  code: '28P01',
  detail: undefined,
  hint: undefined,
  position: undefined,
  internalPosition: undefined,
  internalQuery: undefined,
  where: undefined,
  schema: undefined,
  table: undefined,
  column: undefined,
  dataType: undefined,
  constraint: undefined,
  file: 'auth.c',
  line: '304',
  routine: 'auth_failed' }

Error creating a connection to Postgresql using the following settings:
 { host: 'localhost',
  port: 5432,
  schema: true,
  ssl: false,
  adapter: 'sails-postgresql',
  user: 'electron_release_server_user',
  password: '[redacted]',
  database: 'electron_release_server',
  identity: 'postgresql' }

* * *
Complete error details:
 { Error: read ECONNRESET
    at exports._errnoException (util.js:953:11)
    at TCP.onread (net.js:563:26) code: 'ECONNRESET', errno: 'ECONNRESET', syscall: 'read' }


Troubleshooting tips:

 -> You appear to be trying to use a Postgresql install on localhost. Maybe the database server isn't running, or is not installed?

 -> Is your Postgresql configuration correct?  Maybe your `poolSize` configuration is set too high? e.g. If your Postgresql database only supports 20 concurrent connections, you should make sure you have your `poolSize` set as something < 20 (see http://stackoverflow.com/a/27387928/486547). The default `poolSize` is 10. To override default settings, specify the desired properties on the relevant Postgresql "connection" config object where the host/port/database/etc. are configured. If you're using Sails, this is generally located in `config/connections.js`, or wherever your environment-specific database configuration is set.

 -> Maybe your `poolSize` configuration is set too high? e.g. If your Postgresql database only supports 20 concurrent connections, you should make sure you have your `poolSize` set as something < 20 (see http://stackoverflow.com/a/27387928/486547). The default `poolSize` is 10.
```

- Solution: Replacing Node 6.x with 5.1.x fixed above issue
    
## Postgres DB Installation in production environment
- Install Postgres DB for [windows](http://www.enterprisedb.com/products/pgdownload.do#windows)
```
For example, after a windows installation of postgres, I needed to run the following commands

C:\Program Files\PostgreSQL\9.5\bin>psql.exe --username=postgres
Password for user postgres:
psql (9.5.2)
WARNING: Console code page (437) differs from Windows code page (1252)
         8-bit characters might not work correctly. See psql reference
         page "Notes for Windows users" for details.
Type "help" for help.

Hint: if you need a password, use this for (ENCRYPTED PASSWORD)[https://www.grc.com/passwords.htm] (choose 63 random alpha-numeric characters)

postgres=# CREATE ROLE electron_release_server_user ENCRYPTED PASSWORD 'MySecurePassword' LOGIN;
CREATE ROLE
postgres=# CREATE DATABASE electron_release_server OWNER "electron_release_server_user";
CREATE DATABASE
postgres=# CREATE DATABASE electron_release_server_sessions OWNER "electron_release_server_user";
CREATE DATABASE
postgres=#
```

## config/local.js override with Postgres information
```
Hint: You now need to ensure that these settings are reflected in the config/local.js file.
    connections: {
        postgresql: {
        adapter: 'sails-postgresql',
        host: 'localhost',
        user: 'electron_release_server_user',
        password: 'MySecurePassword', //63 char long above encrypted
        database: 'electron_release_server'
        }
    },

    session: {
        // Recommended: 63 random alpha-numeric characters
        // Generate using: https://www.grc.com/passwords.htm
        secret: 'EB9F0CA4414893F7B72DDF0F8507D88042DB4DBF8BD9D0A5279ADB54158EB2F0',
        database: 'electron_release_server',
        host: 'localhost',
        user: 'electron_release_server_user',
        password: 'MySecurePassword',  //63 char long above encrypted
        port: 5432
    }
    
    appUrl: <place your release server url here>,
    
    port: process.env.PORT || 1338,
    
    files: {
    // Folder must exist and user running the server must have adequate perms
    // Once you generate your release files from electron app, copy those nuget and delta pkg from your project folder into this location. Upload assets from this folder on this release server website.
    dirname: 'D:\\Websites\\electronreleaseserver.abc.edu\\site\\releases', //place where you want to put your files to publish.e.g. nuget, exe, release file etc.
    // Maximum allowed file size in bytes Defaults to 500MB. more to override in iisnode's web.config
    maxBytes: 524288000 
  },
```

## Run after postgres and local.js configuration done
- ```$ SET NODE_ENV=production```
- goto project folder & Run  ``` $ npm start ```
- or run ```npm start --prod```

## IISNode web.config 
```
<configuration>
  <!-- This configuration file is needed for IIS support. -->
    <system.webServer>
      <handlers>
        <add name="iisnode" path="app.js" verb="*" modules="iisnode" />
      </handlers>
      <rewrite>
        <rules>
          <rule name="electronreleaseserver">
            <match url="/*" />
            <action type="Rewrite" url="app.js" />
          </rule>
        </rules>
      </rewrite>
      <directoryBrowse enabled="false" />
      <iisnode
        devErrorsEnabled="true"
        debuggingEnabled="true"
        loggingEnabled="true"
        debuggerPathSegment="debug"
        nodeProcessCommandLine="C:\Program Files\nodejs\node.exe"
        promoteServerVars="APPL_MD_PATH">
        <!-- NOTE: promote server vars is for middleware : iis-baseUrl which is in middlewares folder-->
      </iisnode>
	  <security>
        <requestFiltering>
          <!-- Uploading asset size limit -->
          <requestLimits maxAllowedContentLength="524288000"/>
        </requestFiltering>
      </security>
    </system.webServer>
</configuration>

```

## Credit
This project has been built from the Sails.js up by Arek Sredzki, with inspiration from [nuts](https://github.com/GitbookIO/nuts).

## Updated by: Bhavin Patel

## License
[MIT License](LICENSE.md)
