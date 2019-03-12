Forked main v37 as a clear line of deprecation for Trezor firmware 1.6.1 ETH transaction signing support. 

[TL;DR](#too-long-didnt-read) quick fix towards the bottom. 

Backstory 
=======

I recently bailed out a bunch of coin from a Trezor with the ill-fated firmware version 1.6.1. Best I can tell, Trezor forced a firmware update due to some fundamental changes in the cryptocurrency ecosystem and demands for support from their users, rendering the older firmwares incapable of properly signing Ethereum transactions. MyEtherWallet, wallet.trezor.io, and other common bridge tools were at some point between June 2018 and March 2019 rendered useless for the purpose of extracting financial value from their trezor, had they not performed the required update.

A friend of mine did just that, a carpenter contractor type whom often found himself away from home on business. Quietly HODLing away all he could, like any of our fellows desirous of the death of the centralized banking systems he decided a Trezor store was the best place for them. He needed to extract the coins to update the firmware, but so it seemed he also needed to update the firmware to extract the coin.Trezor support was not as kind as we would have hoped, only offering some potential solutions such as vintage.myetherwallet.com, which also now fails to properly sign the transactions like their main app. 

Thankfully, reddit threads proved helpful, with gilded Internet citizens offering sincere help and direction, but much of it mirroring Trezor's lacklustre responses. Lots of focus on the python-trezor (trezorctl) project or building MyEtherWallet locally, but these were dead ends for us. WallEth was recommended, but fearing the worst with bugs in older versions potentially manhandling the wallets to oblivion, and  also knowing the latest version was no longer compatible with firmware 1.6.1, we started digging through the commit changelog hoping for clues, and fixed around the point of Trezor model 1 firmware 1.6.1 release. 

To save anyone else out there with ERC20 wallets hopelessly stuck on an old Trezor firmware, I present to you... what worked for us. We gratefully stand on the shoulders of the Trezor and reddit communities and are happy to let you know there is a (probably time-sensitive) escape from your financial woes. WallEth version 0.37 -released 2018 Jun 13 -build flavor no-GEth No-Analytics -For-FDroid -OnlineRelease works!


Build Environment 
=======

- Android Studio 3.3.2 Build #AI-182.5107.16.33.5314842, built on February 15, 2019
- JRE: 1.8.0_152-release-1248-b01 amd64
- JVM: OpenJDK 64-Bit Server VM by JetBrains s.r.o
- Windows 10 10.0

Bear with me as I am no more than an amateur developer, here we go:

- Install
 - Android Studio
 - Java RunTime Environment (JRE, traditional end-user Java)
 - Java SE (JDK)

should work for whatever base platform, Windows/Linux/Mac, though YMMV. 

Simplify the above installs by leveraging the https://Chocolatey.org platform. They have their simple installation methods posted there.

Once choco is a thing on your machine,  complete the above installations with a one-liner:

```choco install -y androidstudio jdk8 jre8``` 

- probably worth a reboot at this time just to start fresh, as environment variables get changed. 
- Open android studio and follow the generic instruction for setup, where it will start pulling a bunch of base frameworks for building things in the latest version of android.
- back to our task at hand - WallEth v0.37 is built on Oreo 8.0 (SDK 26) so we'll need to pull in that framework as well.

`File` > `Settings` > `System Settings` > `Android SDK` > Check Android 8.0 and then `apply`  

This gits that framework for building this Internet-ancient WallEth.

- Download/git/ssh the version that worked for us.  (Walleth 0.37 from master branch worked for Trezor 1.6.1)
[WallEth tag 0.37](https://github.com/walleth/walleth/releases/tag/0.37)

- if you grabbed the zip/tar file, unzip/tar the archive somewhere that can host a couple Gig's worth of disk space - the build dependencies that get auto-downloaded by Studio in during compiling are sprawling. 

- Back in android studio, 

`File` > `New` > `Import Project` 

Direct it towards the walleth-0.37 directory unzipped in the last step. Studio will start setting itself up to work with the codebase. 

- Once the initial sync is complete, we can build WallEth, but it has to be the right build flavor - on the very left-hand border of the Android Studio window you'll find a tab called 'Build Versions' that presents a dropdown menu.

Select the option for :

NoGEth - NoFirebase - ForFDroid - OnlineRelase

 - GEth is an extension for Walleth that lets us run a full ETH node - awesome, but unncessary for this purpose. 
 - FireBase is Google's analytics platform, again unnecessary. 
 - We can't use the Offline version because it won't connect to the mainnet, where we need our blockchain ledger updates written so our coins actually go somewhere.
 - FDroid (FOSS android app store, be sure to send Richard Stallman a coin donation when you recover!) is the only static variable in all the build flavors because at this stage of WallEth development, it was not ready for play store, and we shouldn't/wouldn't be able to sign the code for Play Store inclusion anyways.  


Build Time!
=======

We're halfway out of the woods:

- `Build` > `Generate Signed APK` 
- Walk through the prompts.
- create a code signing signature for yourself, save it somewhere you can find it again, and set/remember the password (so you can use the same code sig if you have to go through multiple build processes to get happy.
- Sign with the v2 method only, v1 is ancient and generally not required. If you're running an android version that requires this you shouldn't be messing with valuable data like crypto on that device and the WallEth app probably won't work.
- Android Studio will try to convince you to install a 'latest APK bundle thingy' - no. don't need it for this, we're not publishing to Play Store.

If today's a good day, after a few minutes you'll get a toast message in the bottom-right that build completed. Click 'LOCATE' and your File Browser should open to the walleth-0.37 directory. There will be a directory name there matching the build flavor you'd selected, and inside is an APK. 

Send that APK to your Android device, preferably one running Oreo 8.0 or 8.1. Use bluetooth, Google drive, USB cable, whatever. Open your File Manager of choice and find the APK, click to install. If prompted that 'for security, this app is prevented from installing other apps' then you'll need to allow that.

If you have a WallEth version installed already, you're going to have to uninstall that first. If you've got a bunch of precious configuration data, perhaps leverage TitaniumBackup if you're rooted. otherwise, move your coins around to get them away from your existing WallEth install, because the current instance must be destroyed to get this v37 version running.

If the installation fails, check
 - Did you tell Android System allow your File Manager app to install other apps?
 - Did you Build a SIGNED APK? It needs to be signed.
 - Did you back up and uninstall a currently installed WallEth instance?

Once you're set with v37 WallEth installed, connect your Trezor to your android with USB OTG cable, or leverage a USB-Micro-Male/USB-C-Male to USB-A-female adapter. (adapter's male goes in the Android's port, the cable goes between the adapter and the trezor.)

WallEth should recognize your Trezor, allow pin unlock, view wallets, and the value stored on them. If it's not reading the known value of the wallets, you might have to try a different WallEth version. This walkthrough only describes how v37 worked with firmware 1.6.1 


Bail out your coins!
=====

- Send your coin to an address that's explicitly meant for that coin, and one you control! Binance, BitStamp, CoinBase are all decent intermediaries until you've got your Trezor updated to the latest firmware and ready to receive the transfers back onto it. 
- I'd recommend sending VERY SMALL amounts of coin at first, as a few test cases, wait for the full node validation to complete, and move on from there. Even if all the software you're running is perfectly tuned, after all's said and done the Internets can corrupt or drop data transfers sometimes.
- **BE SURE to uninstall version 37** or whatever other ancient alpha build you're working with once you've recovered your gear. It's strongly urged to keep up with the latest version for compatibility and security purposes. 


Panhandling 
=======

If I saved you some hairpulling and/or time, I'd gladly accept some coin in lieu of cash/check/money order/cigarette cartons:

My Ethereum ETH-ONLY address, thanks in advance: 

**0x4Ea515dDfc03D833fDC202393621A89AB77F1D87**

[![TrezorBailout Walkthrough author's addess](https://github.com/mfsen10/walleth/blob/37-trezorbailout/assets/eth-addr-qr.png)

**Be sure to tip the WallEth devs as well**, their addresses are listed within the app. 


Too long; didn't read
=======

If you're not paranoid about where the code is coming from, or just don't want to fuss with build environments, here's a link to the build that worked for us. It's a build we compiled straight from v37 walleth/walleth/main branch with the process described above. This is verifiable, only difference is the code signature certificate it's signed with. Only reason I'm posting here is because FDroid repo doesn't go back into WallEth's pre-alpha days, and I don't feel like bothering the WallEth devs with this as they have enough on their hands. 

[PreBuilt WallEth-v37 APK](https://github.com/mfsen10/walleth/blob/37-trezorbailout/assets/WALLETH-0.37-noGeth-noFirebase-forFDroid-online-release.apk)
(right click download, or there's a download button on the linked page)


Standard WallΞTH README follows.
=======

[![on Google Play](https://ligi.de/img/play_badge.png)](https://play.google.com/store/apps/details?id=org.walleth)
[![on FDroid](https://ligi.de/img/fdroid_badge.png)](https://f-droid.org/repository/browse/?fdid=org.walleth)
![](https://github.com/ligi/walleth/blob/master/assets/1024x500.png)

WALLΞTH
=======

Native Android Ethereum wallet.

Features
========

 - Networks main(1), ropsten(3), rinkeby(4)
 - Tokens(ERC-20)
 - ERC-67 / ERC-681 URLS
 - ERC-55 Checksums
 - Offline signing (compatible to Parity signing flow)
 - JSON UTC key import/export
 - display value in fiat like EUR, NZD, USD, .. or MakerDAO DAI 
 - display function 
 - [TREZOR](https://trezor.io/?a=walleth.org) Support via USB-OTG - model 1 and model T are supported
 - watch only accounts 
 - one flavor contains go-ethereum light client

find more information on https://walleth.org

References
==========

* [ERC67](https://github.com/ethereum/EIPs/issues/67)
* [ERC681](https://eips.ethereum.org/EIPS/eip-681)
* [ERC20](https://eips.ethereum.org/EIPS/eip-20)
* [Import Geth - a Devcon2 talk](https://ethereum.karalabe.com/talks/2016-devcon.html#1)
* [go Mobile:-Account-management](https://github.com/ethereum/go-ethereum/wiki/Mobile:-Account-management)
* [Ethereum visual reference](https://www.ethereum.org/images/logos/Ethereum_Visual_Identity_1.0.0.pdf)

License
=======

GPL
