---
layout: post
title: Compiling For Testing and Submission of the Ionic App for Apple
---

Using the CLI, run the command `ionic add platform ios` to add Xcode files to the project.

Using the Finder, open the `PROJECTNAME.xcodeproj` located in `{PATH_TO_PROJECT}/platforms/ios`.

Once in Xcode, go to `Product > Compile` and from there you can compile for testing.

### New Project

- NOTE: make sure your account is the highest role if you run into problems in creating certain parts.
- NOTE: connect an Apple Mobile during the entire process or once you have registered it to XCode Organizer.

- Using Apple Developer and ITunes Connect accounts, create your projects. (App IDS for Apple Developer and My Apps for ITunes Connect.)
- Create a certificate for the development and production releases.
- Create development and distribution provisioning profiles for development and production releases.
- `ionic prepare ios` to compile your project `config.xml`
- In `XCode` go to `XCode > Perferences` and add your account.
- in `XCode` go to `Product > Archive` to create an archive.
- In `XCode` go to `Window > Organizer` and click on `Archives`, and complete both `Validate` and `Distribute`.
- NOTE: if problems occurr, you either don't have enough privileges, did not create your `distribution` provisioning profile or you set an incorrect App ID in your `config.xml`
- Once done, go into ITunes Connect, complete all the required fields and submit for review.

- Clone the project
- Pull the latest `develop` branch to get the latest files.
- Install all the required packages using `npm install` and `bower install`. (NOTE: There might be some packages that are manually installed atm.)
- Make sure the `config.xml` file is correct and contains the correct name for the APP ID. (NOTE: Do not add the random ID into the name, it will do it automatically. For this project, it was added it by mistake, so the ID is always be 4GAJFJCHB4.4GAJFJCHB4.com as the full string.)
- `ionic prepare ios` to compile the ID

### Existing Project

- Pull the latest `develop` branch to get the latest files.
- Install all the required packages using `npm install` and `bower install`. (NOTE: There might be some packages that are manually installed atm.)
- Make sure the `config.xml` file is correct and contains the correct name for the APP ID. (NOTE: Do not add the random ID into the name, it will do it automatically. For this project, it was added it by mistake, so the ID is always be 4GAJFJCHB4.4GAJFJCHB4.com as the full string.)
- `ionic prepare ios` to compile the ID
- Using the Finder, open the `PROJECTNAME.xcodeproj` located in `{PATH_TO_PROJECT}/platfroms/ios`.
- Once in XCode, go `Product > Archive` to compile the project using correct credentials.
- Next, go `Window > Organizer` and go `Archives` to view your project so it can be Validated or Distributed.

Manaul Compile

- `xcodebuild`
