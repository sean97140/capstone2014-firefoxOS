capstone2014-firefoxOS
======================

[![Build Status](https://travis-ci.org/askore/capstone2014-firefoxOS.svg?branch=master)](https://travis-ci.org/askore/capstone2014-firefoxOS)

Javascript APIs for offline communications in Firefox OS. PSU CS Capstone 2014

Framework Setup / Development Practices
======================

Currently, we are using nodeJs (JavaScript on the command line) along  
with grunt (a node task runner). These combined with bower (a  
dependency manager) and Karma (unit testing) allow for a pretty good  
combination for testing and developing code.  

#### Git for Windows
It is necessary to have git in Windows' PATH else you won't be able to install bower later. If you already have git in Windows' PATH or else aren't using Windows, you may skip until the Node.js section.

1. Download Git (http://git-scm.com/download/win) 
2. Begin installing Git and during the install process select "Use Git from the Windows Command Prompts" which will put git in Windows' PATH

#### Node.js 
1. Download nodeJS (http://nodejs.org/)  
2. Open up the Node.js Command Prompt  
3. Navigate to your local firefox-OS repo  
4. Run the following commands:  
  1. `npm install`
  2. `npm install -g grunt-cli`  
  3. `npm install -g bower`  
  4. `bower install`
  5. `grunt build`

The build task will recompile the library and run the tests. If you've updated the test and only need to rerun them:
`grunt test`  

You'll see a bunch of tests get run and pass.  

#### Nightwatch
Nightwatch (http://nightwatchjs.org/) is used as the E2E testing solution for the example application provided with the library. To install Nightwatch:

`npm install -g nightwatch`

Selenium (https://selenium-release.storage.googleapis.com/2.44/selenium-server-standalone-2.44.0.jar) is also needed to run the Nightwatch tests. The included configuration file will automatically start Selenium when the tests are run.

Grunt is configured to run Nightwatch whenever `grunt build` or `grunt test` are executed. On `grunt build` the `examples/tests/test.js` folder is edited to the change the path to the battest app's `index.html` file to be the absolute path to the copy on your local machine.

You can use `nightwatch -t examples/tests/test.js` to run the tests manually, if desired.

#### NetBeans
##### Downloading NetBeans and Loading Project  
Download NetBeans from https://netbeans.org/downloads/  
Open NetBeans, create a "New Project", then select "HTML5 Application with Existing Sources"  
The "lib" folder will hold our API prototypes  
The "test/spec" folder will hold our tests files

##### Setting Up Tabbing Standards
1. Within NetBeans, select 'Tools' and then 'Options'
2. Select the 'Editor' icon at the top followed by the 'Formatting' tab
3. Set the 'Language:' tab to 'All Languages'
4. Make sure 'Expand Tabs to Spaces' is unchecked
5. Set 'Tab Size:' to 4
6. Save settings by clicking 'OK'

![Tabbing Standard](https://cloud.githubusercontent.com/assets/3056597/5869687/cb0882dc-a271-11e4-8464-d817ad645efa.png)

##### Tabbing Standards
Use double tabbing for indentation of code (e.g. body of a loop or function) inside files contained both in the "lib" and "test/spec" directories.

Deploying and Testing BatTest Demo App
======================

#### Deployment
##### Virtual Phone
1. If you haven't already, due to reliance on dist/firefoxos.js, run `grunt build` following the [Node.js instructions above](https://github.com/askore/capstone2014-firefoxOS/tree/master#nodejs)
2. Open up the Firefox browser
3. Open up WebIDE by pressing Shift + F8 (or the WebIDE button in the toolbar if you've used webIDE before)
4. Go to "Project" -> "Open Packaged App", navigate to and select the "battest" folder, and click "Select Folder"
5. Click on "Select Runtime" then "Install Simulator"
6. Install the most recent stable version of the Firefox OS Simulator (at time of writing, 2.0)
7. Click on "Select Runtime", then click the most recent stable version of the Firefox OS Simulator under "SIMULATORS"
8. Click the "Install and Run" button or CRTL+R to push the app to the phone 

##### Physical Phone
1. Make sure the battery harness is not connected. Connect the phone to your computer via USB.
2. Power the phone on. It should ask "An incoming request to permit remote debugging connection was detected. Allow connection?". Select "OK".
3. Go to "Project" -> "Open Packaged App" and select the folder of the BatTest App.
4. Click on "Select Runtime" then "Firefox OS" under "USB Devices"
5. Go to "Project" -> "Install and Run" or CRTL+R to push the app to the phone. 

#### Testing
##### Setting up Crapify
1. Set up and build the library using the [Node.js instructions above](https://github.com/askore/capstone2014-firefoxOS/tree/master#nodejs)
2. Open up the Node.js Command Prompt  
3. Navigate to your local firefox-OS repo  
4. Run the following commands:  
  1. `npm install crapify -g`
  2. `npm config set proxy http://127.0.0.1:5000`  
  3. Run crapify with desired configurations such as `crapify start --port=5000 --speed=3000 --concurrency=2` where
    * `port` is the port crapify should start on
	* `speed` is the connection speed in bytes/second
	* `concurrency` is the number of concurrent outbound connections allowed
	* `drop-frequency` is how often should bytes be dropped (`byte count` % `drop frequency`)
	  * Due to the definition of `drop-frequency` not being clear, and thus how to use it, an issue has been created on [crapify's GitHub repo](https://github.com/bcoe/crapify/issues/7)

##### Setting Up Environment For Battery Harness Testing
1. Install VirtualBox and open it up from here: https://www.virtualbox.org/  
2. Click the "New" button to start the wizard to create a new virtual machine  
3. Enter a name like "FxPowertool" and choose "Linux", "Debian" for the OS  
4. Give it a reasonable amount of RAM for your box, like 1GB if you can  
5. For the start up disk, plug in the USB drive and select the HD.vdi file  
6. Click Create to create the VM for the first time. The VM is now saved.  
7. Click Settings next to the created VM and go to the Serial Ports tab.  

###### If you are on a Linux Host
Click to "Enable Serial Port" for "Port 1", set "Port Mode" to "Host Device" and for the "Port/File Path" enter in /dev/ttyACM0.    

###### If you are on a Windows Host
1. Uncheck the "Enable Serial Port" option.
2. Select the USB tab check both "Enable USB Controller" and "Enable USB 2.0 (EHCI) Controller". It may prompt you to install the VirtualBox Extension Pack. If so download it here: https://www.virtualbox.org/wiki/Downloads

###### Making use of the VM from the USB stick  

1. If VirtualBox cannot detect the battery harness, unplug the USB and plug it back in. 
2. Once you get it connected to the computer turn the ammeter and it on.  
3. Select the VM that you have created previously and choose the Start option.  
4. When prompted for a login, enter "capstone" with the password as "firefox".  
5. Use Leafpad to create some file like "tests.json" and add the following:  

```javascript
{
  "title": "My Test Cases",
  "tests": [
    "My first test" 
  ]
}
```

*If you want to run multiple tests and save them to one CSV file, the JSON will look like this*

```javascript
{
  "title": "My Test Cases",
  "tests": [
    "My first test",
	"My second test",
	"My third test"
  ]
}
```

*If you are on windows, you will need to do a USB pass-through, otherwise skip these steps*  
+ In VirtualBox, select "Devices" then "USB Devices"   
+ Select "Dean Camera LUFA USB-RS232 Adapter[0001]". If you do not see this option, unplug the USB and plug it back in.  

Open a LXTerminal and run the following to execute your series of test(s):  

```
sudo powertool -d mozilla -p /dev/ttyS0 -u tk -s current -f
Desktop/tests.json -o Desktop/tests.csv
```

*If you are on windows, replace ttyS0 with ttyACM0 in the command above*

**Make sure the screen is off before testing! Having the screen on will cause the battery usage to increase, throwing off the data.**

1. Click Start to begin testing power. You can also switch to the next test.
2. Click Stop to finish collecting the data. 
3. Exit out of the fxPowertool program.
4. Open up "LibreOffice" from the desktop and navigate to the folder where your CSV files were saved.



API Usage
======================

All functionality is exposed through a global object called "AL" (for AJAX Library).

#### AJAX Requests
To make an AJAX request use the AL.ajax() method.

```AL.ajax(url [, data] [, success])```

An XMLHttpRequest object will be created using `url` as the endpoint. <br>
`data`, if provided, will be JSON-encoded and passed to the endpoint.<br>
`success` will be called when the request has completed successfully<br>

The API will use a GET request if there is no data, otherwise it will be a POST.

If the data argument is a function, it will be used as the success function. 

The success function is passed 3 arguments:<br>
`success(Object responseBody, String status, XMLHttpRequest xhr)`

`responseBody` will be the data that is received back from the endpoint.<br>
`status` is the status code of the request (200, 404, etc)<br>
`xhr` is the actual XMLHttpRequest object that was used to make the request<br>

#### Non-critical AJAX Requests
A non-critical request is added to a queue and waits until conditions are good enough to be fired. 

```AL.addNonCriticalRequest(url, data, callback)```

The parameters are the same as the AL.ajax method.

#### Latency Recording and Analysis
When the library is used for an AJAX request, information about the request is recorded
for later analysis.

##### Latest Access Timestamp
Retrieve the timestamp of the last time a request was completed.

`AL.getLatestAccessTimeStamp(callback)`

`callback` will be called with the latest access timestamp or null.

##### Request History
Retrieve the history of requests.<br>
A maximum of 10,000 requests will be logged. When the 10,001 request would be logged, the oldest entry will be removed first.

`AL.getHistory(callback)`

`callback` will be called with an array of objects representing all requests sent.<br>
Each history object is of the form:

```javascript
{
    begin: Timestamp,
    end: Timestamp,
    size: integer
    origin: string
    id: integer
}
```

`begin` is the timestamp of when the request was made.<br>
`end` is the timestamp of when the request was finished.<br>
`size` is the size of the request data (if any, if none, size is 0)
`origin` is a string containing either "critical" or "non critical"
`id` is a integer that increases every request starting at 1

Entries are in chronological order (newest ones are last).

`AL.getLatestAccessTimeStamp` is the same as calling `AL.getHistory`,
getting the last entry and reading the `end` timestamp. 

`AL.getNextID(callback)` returns an incremented number 1 higher then the id of the 
last request

`callback` will be called with an number one greater than the id of the last
request or 1 if no history exists.

##### Request History Within a Given Timeframe
Retrieve only entries in the history whose `end` falls within a certain range

`AL.trimHistoryByDate(callback, startDate, endDate)`

`callback` will be called with an array of objects representing all requests sent within the specified range.

`startDate` is a Javascript Date object representing the start of the range you are interested in. Any objects in the history with an `end` timestamp equal to the `startDate` object will NOT be included in the returned history.

`endDate` is a Javascript Date object representing the end of the range you are interested in. Any objects in the history with an `end` timestamp equal to the `endDate` object will NOT be included in the returned history.