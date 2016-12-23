Here's the steps and specific programs I had to install in order to get the dev environment for Jinteki.net running on a new Windows 10 laptop (Home Edition). There's nothing different from the general Development Instructions, but some additions specific for Windows.

## Cmder

Sure, PowerShell is pretty good, but you can't beat [Cmder](http://cmder.net/). Seriously, just use this as your terminal. It also comes with git bundled, so you can clone down your fork of Jinteki at this point too. Be sure to set up your upstream remote.

## Git Setup

Be sure to use a local-only, global .gitignore file. You can set this up now. [Here's what I'm using at the moment.](https://gist.github.com/mvelic/4a5ad6127c2789012b53263fac517a7f) You would put this in the root of your User Directory and tell git where it is.

```
git config --global core.excludesfile="%UserProfile%\.gitignore"
```

## Java

Install the [latest JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). I ended up using `jdk-8u111-windows-x64.exe`

## Node, NPM, and NPM Packages

Install the [latest version](https://nodejs.org/en/download/) (v6.9.2), but use the 32-bit version, even if you are on 64-bit version of Windows.

After it is installed, open a Cmder terminal with administrator privileges, head to your repo, and install the [windows-build-tools for NPM](https://github.com/felixrieseberg/windows-build-tools). This will help with the install process later for [node-gyp](https://github.com/nodejs/node-gyp).

```
npm install --global --production windows-build-tools
```

Once that's complete, you can close down your admin prompt, and open up a regular Cmder terminal. Head to your repo and run the following commands:

```
npm install -g bower

npm install -g coffee-script

npm install -g stylus
```

## MongoDB

Install the [latest version](https://www.mongodb.com/download-center?jmp=nav). I used `mongodb-win32-x86_64-2008plus-ssl-3.4.1-signed`

You also need to add the installation to your PATH. If you used the standard location, it will be this: `C:\Program Files\MongoDB\Server\3.4\bin`

I also had to go into the `data` directory and `mkdir db`

## Leiningen

Install the [latest version](http://leiningen-win-installer.djpowell.net/). I used `leiningen-installer-1.0`

## ZeroMQ

Install the [latest version](http://zeromq.org/distro:microsoft-windows). I used `ZeroMQ-4.0.4-miru1.0-x64`

**Note:** my antivirus didn't like the distribution site. Haven't noticed anything thus far, but be warned.

## Install NPM and Javascript Stuff

Open a Cmder terminal and go to your repo. Then run the following:

```
npm install

bower install
```

## RUNNING JNET LOCALLY

Here's what you've been waiting for - be sure to run each of these in its own Cmder terminal from inside the repo. Yes, by the end of this, you will have 5 terminals running at once.

```
# 1. Start up MongoDB
mongod --dbpath ./data

# 2. Download the card data, then Compile/watch Clojure files
npm run fetch

lein cljsbuild auto dev

# 3. Compile/watch CSS files
stylus -w src/css -o resource/public/css/

# 4. Compile server side Clojure files, then Launch game server
Lein uberjar

java -jar target/netrunner-standalone.jar

# 5. Launch Node Server
npm run start
```

You should now be able to [visit Jnet locally](http://localhost:1042) in your browser.