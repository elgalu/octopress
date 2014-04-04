---
layout: post
title: "Run protractor against Internet Explorer VM"
date: 2014-04-03 22:59
comments: true
categories: [protractor, webdriver, selenium, IE, grid2, virtualbox]
published: true
---
## Step 1 - Get Virtualbox

Is free and available on many platforms

[https://www.virtualbox.org/wiki/Downloads]()


## Step 2 - Get a free Windows Virtual Machine

If you don't have already a running Windows with IE you can quickly and legally go with this:

[http://modern.ie/en-us/virtualization-tools#downloads]()

![VirtualBox IE images](/images/virtualbox_for_linux_modernIE.png "VirtualBox images for Linux")

If, like me, you need to test against IE9, IE10, IE11 then you'll need to download 3 VMs, one for each IE version, e.g.:

*   IE9 - Win7
*   IE10 - Win7 or Win8
*   IE11 - Win7 or Win8.1

## Step 3 - Install Java on the windows VM

Once you have your Windows VM up and running, you should ensure java is installed.
Install link here:

[http://www.java.com/en/download/index.jsp]()

You may need to retry this download a few times since Windows security scan gives troubles sometimes.


## Step 4 - Disable protected mode on IE options among other things

Go to Internet explorer options, follow these screen shots to get all setup:

![IE11_options1_delete_browsing_history_on_exit](/images/IE11_options1_delete_browsing_history_on_exit.png "IE11_options1_delete_browsing_history_on_exit")

![IE11_options2_disable_protected_mode_all_zones](/images/IE11_options2_disable_protected_mode_all_zones.png "IE11_options2_disable_protected_mode_all_zones")

![IE11_options3_turn_OFF_popup_bloquer](/images/IE11_options3_turn_OFF_popup_bloquer.png "IE11_options3_turn_OFF_popup_bloquer")

![IE11_options4_disable_autocomplete](/images/IE11_options4_disable_autocomplete.png "IE11_options4_disable_autocomplete")

![IE11_options5_dont_check_certificates_and_erase_temporary_files](/images/IE11_options5_dont_check_certificates_and_erase_temporary_files.png "IE11_options5_dont_check_certificates_and_erase_temporary_files")

![IE11_options6_leave_warnings_unchecked](/images/IE11_options6_leave_warnings_unchecked.png "IE11_options6_leave_warnings_unchecked")


## Step 5 - Download selenium standalone, latest

As of this writing, latest selenium is 2.41.0, search latest at:

[http://selenium-release.storage.googleapis.com/index.html]()

Download these files in you windows VM:

[http://selenium-release.storage.googleapis.com/2.41/selenium-server-standalone-2.41.0.jar]()

[http://selenium-release.storage.googleapis.com/2.41/IEDriverServer_Win32_2.41.0.zip]()

We also need the IE driver, so by using 32 bits version and placing the extracted file into your windows path should get that ready. FYI people had issues with 64 bits versions of the driver so on x64 Windows machine I suggest you avoid using x64 driver even on a 64 bits machine.

If your are unsure where to put the extracted file, copy `IEDriverServer.exe` to `C:\Windows\System32`


## Step 6 - Run IEDriverServer.exe manually

Windows protected mode might be enabled, so run manually the file `IEDriverServer.exe`, but run it at the location path where you copied it, e.g. `C:\Windows\System32`

Running that file will trigger the Windows security pop up so you can authorize the executable with **Run anyway** to get that remembered by Windows:

![IEDriverServer_run_anyway_windows](/images/IEDriverServer_run_anyway_windows.png "IEDriverServer_run_anyway_windows")

Then *Allow access* for it. This is more related to Windows firewall actually:

![IEDriverServer_enable_firewall](/images/IEDriverServer_enable_firewall.png "IEDriverServer_enable_firewall")


## Step 7 - Forward port 4411

We are going to use an arbitrary port number, let's say 4411, for this IE11 VM.

One way to go is leave VirtualBox network configuration as it is, i.e. NAT then enable port forwarding so we can connect to that port as if it were at localhost.

In the host machine, go to `VirtualBox menu -> Devices -> Network -> Network Settings...`

![virtualbox_ports_forwarding](/images/virtualbox_ports_forwarding.png "virtualbox_ports_forwarding")

Add that Port Forwarding Rule in the screen shot above.

Another way is to change the network setup to be **Bridged** instead of **NAT** and you will have to find out the IP address it gets from your home network. That requires more work on your side paying attention to your IP addresses.


## Step 8 - Write a .bat script to start selenium

Within your Window VM, you should place this `ie11.bat` file in the same directory where you downloaded the selenium standalone jar, for example in your downloads folder.

```bat
echo WIN8.1 Machine - IE11
set BROWSERCAPS="maxInstances=1,browserName=internet explorer,version=11"
java -jar selenium-server-standalone-2.41.0.jar -port 4411 -maxSession 1 -browser %BROWSERCAPS%
@pause
```

Max instances can be increased but i prefer to have 1 running IE11 at the same time only.


## Step 9 - Run ie11.bat

Note port 4411 is an arbitrary port we specified in the bat file. It needs to be open on that Windows VM, so open it in your Windows firewall or simply accept it when the Firewall screen appears after running `ie11.bat`

![Windows_firewall_for_java](/images/Windows_firewall_for_java.png "Windows_firewall_for_java")


## Step 10 - Configure protractor to use IE11

This is simpler, just the the configuration properly. Weir thing below is the port number **4411** you can switch back to **4444** if you want to use `webdriver-manager start` again, locally.

```js
exports.config = {
    seleniumAddress = 'http://localhost:4411/wd/hub';
    capabilities: {
        'browserName': 'internet explorer',
        'platform': 'ANY',
        'version': '11'
    },
};
```


## Final notes and extras

This isn't the only way to run against IE machine, you could install nodejs there too and install protractor.

Also note, you can go a step further and setup a [selenium standalone grid](https://code.google.com/p/selenium/wiki/Grid2) to replace `webdriver-manager` entirely and to have more control.

Steps to do that below.


### Alternative 1 - Install nodejs on Windows

Setting up protractor on IE11 is quite doable, download first:

[http://nodejs.org/download/]()

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

Start selenium on a custom port, the one we preferred before: **4411**

```bat
webdriver-manager start --seleniumPort=4411
```

Connect to that selenium started remotely to avoid having to run protractor binaries and nodejs infrastructure on that VM. In my case i run protractor from Linux or Mac OSX.

To to that, follow Step 10 in this post.


### Alternative 2 - Use Selenium Grid2

Within the host machine, in my case Linux. Instead of running `webdriver-manager start` you can switch to a standalone grid, a.k.a "hub" by running this simple command:

```bash
java -jar selenium-server-standalone-2.41.0.jar -role hub -port 6666 -newSessionWaitTimeout 25000
```

Note you can get selenium standalone jar file with wget:

```bash
wget http://selenium-release.storage.googleapis.com/2.41/selenium-server-standalone-2.41.0.jar
```

Also, you may need to open that port 6666 on your firewall, i did it with `ufw` in Ubuntu 13:

```bash
sudo ufw allow 6666
```

So leave `java -jar selenium-server-standalone` command running in an open terminal, this is the selenium grid that nodes will connect to.

If you want to switch to a selenium grid, you will need to change `ie11.bat` file like this:

```bat
echo WIN8.1 Machine - IE11
set HUBURL="http://10.0.2.2:6666/grid/register"
set BROWSERCAPS="maxInstances=1,browserName=internet explorer,version=11"
java -jar selenium-server-standalone-2.41.0.jar -role node -hub %HUBURL% -port 4411 -maxSession 1 -browser %BROWSERCAPS%
@pause
```

#### Troubleshooting Grid2

Check the selenium grid logs, on your host machine (in my case Linux) to see if the output contains the following error or similar:

```bash
WARNING: Failed to check status of node: Connect to 10.0.2.15:4411 timed out
```

That's the case when the firewall is blocking port 4411 on the guest VM.

If you can not work around it, considering moving your VM network setup from **NAT** to **Bridged**. Then you will have to find out the IP address of you host machine, in my case Linux, and replace `10.0.2.2` with that IP, e.g. `192.168.0.3`

Networking setup details is are little beyond the scope of this post.


### Goodbye

Hope this post helps you.

Have a good time writing e2e protractor tests !  :D
