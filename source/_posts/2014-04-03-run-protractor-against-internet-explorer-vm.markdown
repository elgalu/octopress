---
layout: post
title: "Run protractor against Internet Explorer VM"
date: 2014-12-21 17:25
comments: true
categories: [protractor, webdriver, selenium, IE, grid2, virtualbox]
published: true
---
<h2 id="step1">Step 1 - Get Virtualbox</h2>

Is free and available on many platforms

[https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)


<h2 id="step2">Step 2 - Get a free Windows Virtual Machine</h2>

If you don't have already a running Windows with IE you can quickly and legally go with this:

[http://modern.ie/en-us/virtualization-tools#downloads](http://modern.ie/en-us/virtualization-tools#downloads)

![VirtualBox IE images](/images/virtualbox_for_linux_modernIE.png "VirtualBox images for Linux")

If, like me, you need to test against IE9, IE10, IE11 then you'll need to download 2 VMs, one for each IE version, e.g.:

*   IE9 - Win7
*   IE10 - Win7 (yes you can use good old Windows 7 instead of Win8)
*   IE11 - Win7 (yes you can use good old Windows 7 instead of Win8.1)

<h2 id="step3">Step 3 - Install Java on the windows VM</h2>

Once you have your Windows VM up and running, you should ensure java is installed.
Install link here:

[http://www.java.com/en/download/index.jsp](http://www.java.com/en/download/index.jsp)

You may need to retry this download a few times since Windows security scan gives troubles sometimes.


<h2 id="step4">Step 4 - Disable protected mode on IE options among other things</h2>

Go to Internet explorer options, follow these screen shots to get all setup:

![IE11_options1_delete_browsing_history_on_exit](/images/IE11_options1_delete_browsing_history_on_exit.png "IE11_options1_delete_browsing_history_on_exit")

![IE11_options2_disable_protected_mode_all_zones](/images/IE11_options2_disable_protected_mode_all_zones.png "IE11_options2_disable_protected_mode_all_zones")

![IE11_options3_turn_OFF_popup_bloquer](/images/IE11_options3_turn_OFF_popup_bloquer.png "IE11_options3_turn_OFF_popup_bloquer")

![IE11_options4_disable_autocomplete](/images/IE11_options4_disable_autocomplete.png "IE11_options4_disable_autocomplete")

![IE11_options5_dont_check_certificates_and_erase_temporary_files](/images/IE11_options5_dont_check_certificates_and_erase_temporary_files.png "IE11_options5_dont_check_certificates_and_erase_temporary_files")

![IE11_options6_leave_warnings_unchecked](/images/IE11_options6_leave_warnings_unchecked.png "IE11_options6_leave_warnings_unchecked")

<h6 id="step4-ie10-or-less-only">Following an IE10 / IE9 / IE8 necessary option:</h6>

![IE10_disable_accelerator_button_on_selection](/images/IE10_disable_accelerator_button_on_selection.png "IE10_disable_accelerator_button_on_selection")

<h6 id="step4-ie11-auto-updates">Check "Install new versions automatically"</h6>

![IE11_check_Install_new_versions_automatically](/images/IE11_check_Install_new_versions_automatically.png "IE11_check_Install_new_versions_automatically")


<h2 id="step5">Step 5 - Download selenium standalone, latest</h2>

As of this writing, latest selenium is 2.44.0, search latest at:

[http://selenium-release.storage.googleapis.com/index.html](http://selenium-release.storage.googleapis.com/index.html)

Download these files in you windows VM:

[http://selenium-release.storage.googleapis.com/2.44/selenium-server-standalone-2.44.0.jar](http://selenium-release.storage.googleapis.com/2.44/selenium-server-standalone-2.44.0.jar)

[http://selenium-release.storage.googleapis.com/2.44/IEDriverServer_Win32_2.44.0.zip](http://selenium-release.storage.googleapis.com/2.44/IEDriverServer_Win32_2.44.0.zip)

We also need the IE driver, so by using 32 bits version and placing the extracted file into your windows path should get that ready. FYI people had [issues with 64 bits versions of the driver](http://stackoverflow.com/questions/21612643/protractor-internet-explorer-slowness) so even on an 64 bits Windows machine I suggest you avoid using x64 driver, stick to 32 bits.

If your are unsure where to put the extracted file, copy `IEDriverServer.exe` to `C:\Windows\System32`


<h2 id="step6">Step 6 - Run IEDriverServer.exe manually</h2>

Windows protected mode might be enabled, so run manually the file `IEDriverServer.exe`, but run it at the location path where you copied it, e.g. `C:\Windows\System32`

Running that file will trigger the Windows security pop up so you can authorize the executable with **Run anyway** to get that remembered by Windows:

![IEDriverServer_run_anyway_windows](/images/IEDriverServer_run_anyway_windows.png "IEDriverServer_run_anyway_windows")

Then *Allow access* for it. This is more related to Windows firewall actually:

![IEDriverServer_enable_firewall](/images/IEDriverServer_enable_firewall.png "IEDriverServer_enable_firewall")

**IMPORTANT** Close running `IEDriverServer.exe` now, this run was only to trigger Windows security and firewall dialogs.


<h2 id="step7">Step 7 - Find out VM IP address</h2>

You can do so by opening the Windows command prompt cmd.exe then type `ipconfig` a common port number given by VirtualBox to the VM is **10.0.2.15** and VMWare might look more like **192.168.240.131** but it varies on each environment.

Take note of the IP address and within your host machine edit **/etc/hosts** file too add a new entry, I use custom domain names **ie11.dev** and so on like this:

```bash
192.168.240.131   ie11.dev
192.168.240.132   ie10.dev
```

So within your Protractor project you will need a config file that points selenium to the proper URL, e.g. a file **ie11.conf.js**

```js
config.capabilities.browserName = 'internet explorer';
config.capabilities.version = '11';
config.seleniumAddress = 'http://ie11.dev:4444/wd/hub';
```


<h2 id="step8">Step 8 - Write a .bat script to start selenium</h2>

Within your Window VM, you should place this `ie11.bat` file in the same directory where you downloaded the selenium standalone jar, for example in your downloads folder.

```bat
@echo IE11 selenium machine
java -jar selenium-server-standalone-2.44.0.jar -port 4444
@pause
```


<h2 id="step9">Step 9 - Run ie11.bat</h2>

Note port 4444 is the default selenium port, you can change it if needed. We need Windows firewall to add a rule to allow incoming connections to that port; accept all when the Firewall screen appears after running `ie11.bat`

![Windows_firewall_for_java](/images/Windows_firewall_for_java.png "Windows_firewall_for_java")

**IMPORTANT** Leave this script running on Windows machine. When you restart the VM you'll have to manually start `ie11.bat` again unless you add it to the startup program list.


<h2 id="step10">Step 10 - Configure protractor to use IE11</h2>

We already saw this on step 7 but let's look again with an alternative syntax:

```js
exports.config = {
    seleniumAddress = 'http://ie11.dev:4444/wd/hub';
    capabilities: {
        'browserName': 'internet explorer',
        'platform': 'ANY',
        'version': '11'
    },
};
```


<h2 id="final-notes">Final notes and extras</h2>

This isn't the only way to run against IE machine, you could install nodejs there too and install protractor.

Also note, you can go a step further and setup a [selenium standalone grid](https://code.google.com/p/selenium/wiki/Grid2) if you want to maintain a more complex infrastructure.

Steps to do that below.


<h2 id="nodejs-win">Alternative 1 - Install nodejs on Windows</h2>

Setting up protractor on Windows is quite doable, download first:

[http://nodejs.org/download/](http://nodejs.org/download/)

And select Windows Installer of course. I recommend you choose 32 bits installers.
Follow setup wizard.

Once installed, open a Command Prompt or `CMD.EXE`

Install protractor globally:

```bat
npm install -g protractor
```

Make protractor download selenium and IE Driver for you:

```bat
webdriver-manager update --ie
```

You can start selenium on a custom port if need by adding `--seleniumPort=4444`

```bat
webdriver-manager start
```


<h2 id="grid2">Alternative 2 - Use Selenium Grid2</h2>

Within the host machine, in my case Linux. Instead of running `webdriver-manager start` you can switch to a standalone grid, a.k.a "hub" by running this simple command:

```bash
java -jar selenium-server-standalone-2.44.0.jar -role hub -port 6666 -newSessionWaitTimeout 25000
```

Port *6666* is just an arbitrary one, you can stick with *4444* if you like but I prefer to state some difference.

Note you can get selenium standalone jar file with wget:

```bash
wget http://selenium-release.storage.googleapis.com/2.44/selenium-server-standalone-2.44.0.jar
```

Also, you may need to open that port 6666 on your firewall, I did it with `ufw` in Ubuntu 13:

```bash
sudo ufw allow 6666
```

So leave `java -jar selenium-server-standalone` command running in an open terminal, this is the selenium grid that nodes will connect to.

If you want to switch to a selenium grid, you will need to change `ie11.bat` file like below. Also note IP **10.0.2.2** is usually VirtualBox host machine IP address, VMWare IP might be **192.168.240.1** when using the NAT interface.

```bat
@echo IE11 selenium hub machine
set HUBURL="http://10.0.2.2:6666/grid/register"
set BROWSERCAPS="maxInstances=1,browserName=internet explorer,version=11"
java -jar selenium-server-standalone-2.44.0.jar -role node -hub %HUBURL% -port 4444 -maxSession 1 -browser %BROWSERCAPS%
@pause
```

<h4 id="grid2-issues">Troubleshooting Grid2</h4>

Check the selenium grid logs, on your host machine (in my case Linux) to see if the output contains the following error or similar:

```bash
WARNING: Failed to check status of node: Connect to 10.0.2.15:4444 timed out
```

That's the case when the firewall is blocking port 4444 on the guest VM.

If you can not work around it, considering moving your VM network setup from **NAT** to **Bridged**. Then you will have to find out the IP address of you host machine, in my case Linux, and replace `10.0.2.2` with that IP, e.g. `192.168.0.3`

Networking setup details is are little beyond the scope of this post.


### Goodbye

Hope this post helps you.

Have a good time writing e2e protractor tests !  :D
