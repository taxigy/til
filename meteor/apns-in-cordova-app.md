# APNS in Cordova App

## Ingredients

1. One Mac.
1. One iPhone.
1. One Meteor project.
1. One Apple Developer Program participation for your Apple ID.

## Preparing APN certificate and key

Let's pretend you already have a Meteor project that, likely, is targeted to
iOS. Make the app build for iOS with

```bash
$ meteor add-platform ios
```

and run a test build with

```bash
$ meteor run ios-device
```

The Cordova app will be transfered to Xcode, and the latter will be launched. No
need to actually build and run the app, but just grab the Bundle Identifier from
the project settings.

Then go to the Apple Developer dashboard, open **Certificates, Identifiers &
Profiles** and then **App IDs** under **Identifiers** group. Add a new app
identifier with whatever description and such and in the **Explicit App ID**
field, paste previously retrieved bundle identifier. At the same form, in **App
Services** group, check the **Push Notifications** box.

Next, you'll need a pair of certificates, actually a certificate and a private
key, to enable push notifications in the app. And this is the most tricky part
I've spent a dozen hours beating my head against the wall.

Launch Keychain Access app and find the **Request a Certificate From a
Certificate Authority** item. Run it. The process there is pretty
straightforward, with the only point that you need to _save the certificate
request in the file system_, not to email it to the CA.

After certificate request is ready and saved to the file system (by default,
~/Desktop), go back to Apple Developer dashboard, open the list of app
identifiers (as of 2016, [this
link](https://developer.apple.com/account/ios/identifier/bundle) should work),
find previously created app, expand it and click **Edit**. In the **iOS App ID
Settings**, there should be a form group titled **Push Notifications**, and two
"cards" in it: one for development, one for production.

In the **Development SSL Certificate** fieldset, click **Create Certificate...**
and, well, create a certificate. No input is required, and in few seconds,
you'll be having a link to download the certificate.

Download it.

Then, import it into the Keychain Access, for example, simply double-clicking
the file or dragging it into the Keychain Access window. It will appear in the
**login** keychain, under **My Certificates** category, presumably named "Apple
Development IOS Push Services: (your bundle identifier following)". Expand it
and you'll see that the top level entity is of "certificate" kind, while nested
one is of "private key".

There, in fact, may be several certificates in the list. Logically, one that
appeared last, by date of creation, is the right one.

Okay, it's close.

Now, first, control-click the certificate and select "Export (the name of the
certificate)". Export it somewhere into the file system and call it `cert.p12`.
Oh, yes, choose **Personal Information Exchange** format with .p12 file
extension.

When exporting, you'll be asked to set passphrase for the certificate. Simply
leave it empty, no passphrase is needed for development.

Second, do the same with the private key. Control-click it and export to the
file system with the name `key.p12`.

Now, there are

* `cert.p12`, the certificate, and
* `key.p12`, the private key.

Open terminal at their location and run

```bash
$ openssl pkcs12 -clcerts -nokeys -in cert.p12 -out cert.pem
$ openssl pkcs12 -nocerts -in key.p12 -out key.pem
```

There's [a nice guide](http://stackoverflow.com/a/27942504/1287643) on how to
generate the PEMs from .p12 files.

Once PEMs are ready, check if those are valid:

```bash
$ openssl s_client -connect gateway.sandbox.push.apple.com:2195 \
  -cert cert.pem -key key.pem
```

There should be some output which, in the end, should tell that there's no error
(0 OK). If so, time to go to the next step.

## Setting up the Meteor app

Take `cert.pem` and `key.pem` and place them into private/ folder of your Meteor
app:

```bash
$ cp path/to/{cert,key}.pem ./private/
```

In the Meteor app, add the [raix:push](https://github.com/raix/push) package:

```bash
$ meteor add raix:push
```

This package exports the `Push` global object that makes sending push
notifications possible.

The best way to set it up is to create a config. Place it inside the app folder,
at topmost level and call it `config.push.json`:

```bash
$ touch config.push.json; vim config.push.json
```

Create a simple config that would only target APN service and only in
development mode:

```json
{
  "apn-dev": {
    "key": "key.pem",
    "cert": "cert.pem",
    "gateway": "gateway.sandbox.push.apple.com"
  },
  "production": false
}
```

## Checking

Now, check if push notifications work, simply running

```bash
$ meteor run ios-device
```

and running the app on the device. As the app is launched, it will show an alert
asking to enable push notifications. Once you accept, it should receive a push
token from the Apple Push Notifications Service and transfer it to the server.

To test a push notification, let's broadcast
one. To do that, in the terminal, do the following:

```bash
$ meteor shell
> Push.send({from: 'push', title: 'The title', text: 'The text', query: {}});
```

The `query` object is responsible for selecting certain recipients. Since it's
empty, all the registered recipients will receive this push notification. At
least they should.

If this doesn't happen, check if the app received an APN token:

```bash
$ meteor shell
> Push.appCollection.find().fetch()
[ { _id: 'ph5jNJpk5fyMyDoQj',
    appName: 'main',
    userId: null,
    enabled: true,
    createdAt: Sat Mar 12 2016 00:36:47 GMT+0400 (SAMT),
    updatedAt: Sat Mar 12 2016 00:41:55 GMT+0400 (SAMT),
    token: { apn: '8ab8c2f476038e460860c219e2aec2a623bb3d8755ed967a4723xxxxxxxxxxxx' } } ]
```

Which means that the device that's having this app instance received this
particular token will receive push notifications.

And that's it.
