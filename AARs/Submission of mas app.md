Prepare & Sign Your Electron App for the Mac App Store (MAS)

Two days; the amount of time it took me to figure out how to properly sign and submit an Electron app to the MAS. After the two days of learning I am glad to report that it currently takes minutes. I hope that sharing this knowledge will make the process easier for someone else.

Preparation Work:

This part of the process takes the longest, but once completed for you app you will not have to touch it again until you certificate expires. Or you have a new app to submit to the store.

- The first step: enroll in the apple developer program. Go to https://developer.apple.com/programs/ to enroll (it costs \$99). The instructions here are simple to follow. You can enroll as an individual or an organization chose whichever suits your situation.
- On a mac device create a request for a certificate by going to your Keychain Access App then navigating to Keychain Access -> Certificate Assistant -> “Request a Certificate From a Certificate Authority…” (CSR). Fill in the first two fields and save the certificate to your disk.

In the next few steps we are going to actual use the CSR to generate the certificates that we will later use to sign your App. These a 3rd party certificates are required.

- Login to your developer account (https://developer.apple.com) -> select macOS from the dropdown. The click the “+” button in the top right of the section to request a new certificate.

Under the production sub-section section select “Mac Appstore” -> click “Continue” button then select “Mac App Distribution” then click the “Continue” button. You will be asked to upload the CSR; Upload it then click the “Generate” button. Once it is done click the “Download” button to save the MAS certificate to hard drive.

- You will need to repeat the previous step but this time select the “Mac Installer Distribution” option.
- Find the downloaded certificates on your hard drive and double click on them both in-order to install them to your keychain. Verify the certificates were installed by double checking for their existence on the keychain.

We still need to create you App ID and provision profile under your developer account.

- Under the Identifiers section of your developer account click on App IDs then click the “+” button in the top right of the section. Fill in the form (example a App ID: “com.myOrganization.appName”). This App ID must be set as you “appId” in your package.json.
- Now under the provisioning profile section click on All -> then click the “+” in the top-right of the section. Create and download a provisioning profile for Mac Appstore distribution. Rename the profile to “embedded.provisionprofile”
- Login to https://itunesconnect.apple.com/login. Here you must create a new application by following the on-screen instructions. Fill in ALL the app data and information.

Xcode and to a package called “electron-osx-sign” are needed on your machine as well.

- Download and install Xcode; you won’t use it directly but an application named Application loader will be installed alongside it.
- Globally install “electron-osx-sign” so we can use the command line to run the code-signing commands.

Time to get your plists ready.

- Create your parent.plist and child.plist but if you don’t have them you can default templates from the electron-osx-sign repository https://github.com/electron-userland/electron-osx-sign.
- Don’t forget to add “<string>CertId.bundle.id</string>” to the bottom of you parent.plist. To add more entitlements, visit https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW6

Signing:

- Build & Package the MAS version of your application. Once it is done add <key>ElectronTeamID</key> <string>CertId</string> to the bottom of you Info.plist which is located in your app bundle.
- Sign the application by running this “codesign --deep --verbose --force --sign "yourThirdPartyCertificateName" /PATH/TO/theAPP”. The certificate name can be found in your Keychain Access. Important\* this is not the 3rd party installer name. You can verify the signature by running “codesign --verify -vvvv /PATH/TO/theAPP”
- Run “export DEBUG=electron-osx-sign\*”; this will display the logs during the electron-osx-sign process. It will help you determine iff something went wrong.
- Now let’s get to signing run “electron-osx-sign '/PATH/TO/theAPP ' '/PATH/TO/theAPP /Contents/Resources/app.asar' --entitlements='/PATH/TO/parent.plist' --entitlements-inherit='PATH/TO/child.plist ' --identity='yourThirdPartyCertificateName' --platform='mas'”
- If all goes well your app should be signed. Read thought the logs real wuqik to ensure that there we no errors.
- Now you can create a production .pkg file in order to upload to the Appstore. Run “productbuild --component “PATH/TO/APP" /Applications --sign "yourThirdPartyInstallerCertificateName"./appName.pkg"”
- Open Application Loader select the app and upload it. If all goes well your application build will be available on itunesconnect for submission. Fill in ALL the information about your app and under the App Store section add the build you just uploaded.
- Finally submit your app for review.

If something goes wrong during the upload process read the errors and because the problem may be written there. If it all looks good check the path and the 3rd party certificate name you are using. The errors I encountered were either due to an incorrect path or file issue.

If for some reason you deleted a previous build of the app ensure you trash can is emptied. This will make certain that an old build is not referenced while signing the app.

If all goes will your app will pass review.

Sources:
Mac App Store Submission Guide. (2015). Retrieved March 17, 2019, from https://electronjs.org/docs/tutorial/mac-app-store-submission-guide

How to submit an Electron app to the mac appstore? (2017). Retrieved March 18, 2019, from https://ademcan.net/blog/2017/06/17/how-to-submit-an-electron-app-to-the-mac-appstore/
electron-osx-sign guide (n.d.). Retrieved March 18, 2019, from https://mintkit.net/electron-userland/electron-osx-sign/guide/
