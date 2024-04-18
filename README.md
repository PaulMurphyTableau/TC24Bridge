<p align="center">
  !IMAGE[TableauBridgeIcon.jpg](instructions258342/TableauBridgeIcon.jpg)


Hello there!

Welcome to your lab environment.

To log into your VM, you will need to use the following credentials:

Username: +++**@lab.VirtualMachine(Server2022-TC24(Server)).Username**+++

Password: +++**@lab.VirtualMachine(Server2022-TC24(Server)).Password**+++

Please note that the +++**<--Icon**+++ when clicked will paste directly into where the cursor is.

Tableau Cloud credentials:

Tableau Cloud Email: **@lab.CloudCredential(TC2024-HOT1807).Username**

Tableau Cloud Password: **@lab.CloudCredential(TC2024-HOT1807).Password**
 

---------------------------
## **Hands-On Exercise 1: Bridge Client Connection:**

The goal of this exercise is to connect the Bridge client successfully to Tableau Cloud. 

You will encounter a real life scenario of having to contend with a network firewall.

1. Log into the lab VM.

2. Start up the **Charles Proxy** application on the desktop

!IMAGE[CharlesProxy.jpg](instructions258342/CharlesProxy.jpg)

3. The application should already be set to record, but please check so in the application tool bar (Round Red Icon !IMAGE[CharlesProxyRecording.jpg](instructions258342/CharlesProxyRecording.jpg))


4. Double click the  Tableau Bridge shortcut on the desktop to open Tableau Bridge

!IMAGE[qk4zdimv.jpg](instructions258342/qk4zdimv.jpg)

5. If you get a message to "Do you want to allow this app to make changes to your device?" Click Yes

6. You should encounter an error when Tableau Bridge is opening. This is due to the outbound firewall blocking Tableau Bridge from getting to Tableau Cloud. **Click OK**.

!IMAGE[TableauBridgeLoginError.jpg](instructions258342/TableauBridgeLoginError.jpg)

7. Open up the **Firewall Control** folder on the desktop

!IMAGE[FirewallControl.jpg](instructions258342/FirewallControl.jpg)

8. We have two powershell scripts in this folder to open up outbound access to the US East and US West destination.

    - Double Click on the **1.Enable Access to Us East** script (click yes if prompted)
    - Double Click on the **2.Enable Access to Us West** script (click yes if prompted)

This has enabled outbound access to the the Tableau Cloud US East and West destinations

9. Double click the  Tableau Bridge shortcut on the desktop to open Tableau Bridge again

10. You will be presented with the Tableau Cloud login screen. Please enter the Tableau Cloud email below:

Tableau CLoud Email: **@lab.CloudCredential(TC2024-HOT1807).Username**

You will be re-directed to our identity provider Auth0. Please re-enter the same email below and password:

Tableau Cloud Email: **@lab.CloudCredential(TC2024-HOT1807).Username**

Tableau Cloud Password: **@lab.CloudCredential(TC2024-HOT1807).Password**

11. Double click on the Tableau Bridge desktop shortcut again to bring up the Bridge user interfact

You have now successfully connected your Bridge Client to the TC24Bridge Tableau Cloud Site!!

!IMAGE[BridgeSuccessConnect.jpg](instructions258342/BridgeSuccessConnect.jpg)



---------------------------



## **Hands-On Exercise 2: Admin Views:**
The goal here is to trace the extract refreshes we just did using the Tableau Cloud Admin Views

Let's log in to Tableau Cloud.

There are two ways to do this from the Bridge Server.
- Open up the Bridge Client
- One the right side it, there is a link with the name of the site (TC24Bridge). Click this link.
- If it does ask for email and password, please use the following
- Enter your Tableau Cloud Email: **@lab.CloudCredential(TC2024-HOT1807).Username**
- Enter your Tableau Cloud Password: **@lab.CloudCredential(TC2024-HOT1807).Password**
- Note: If it does ask you for a site URI, enter **TC24Bridge**

**OR**

- Open up the Browser (Edge browser is on the desktop)
- Type **online.tableau.com**
- Enter your Tableau Cloud Email: **@lab.CloudCredential(TC2024-HOT1807).Username**
- Enter your Tableau Cloud Password: **@lab.CloudCredential(TC2024-HOT1807).Password**
- Note: If it does ask you for a site URI, enter **TC24Bridge**

On the left menu, click on **Site Status** !IMAGE[SiteStatusLogo.jpg](instructions258342/SiteStatusLogo.jpg)

This will bring up the list of Admin Views

#### **Published Data Source Extract Refresh:**

Pubished data sources (PDS) are refreshed by handing off the extract job to the Bridge client. The refresh is then done independently on the Bridge client and published afterwards to Tableau Cloud.

There are 2 steps to check with this workflow. 1) Did the job get to a Bridge client successfully and 2) Did the extract get successfully refreshed on the Bridge client. Let's check these both now.

1. On the left menu, click on the Jobs menu !IMAGE[JobsMenuIcon.jpg](instructions258342/JobsMenuIcon.jpg)

This should show the list of jobs the Tableau Cloud backgrounder has worked/working on in the last 24 hours.

2. The PDS jobs would have a Task Type = **Bridge Refresh**, and a Status = **Sent to Bridge** if the job was succesfully sent to the Bridge Client

3. Alternatively, if you click on Site Status on the left menu again, and click on the **Background Tasks for Non Extracts** admin view !IMAGE[BackgroundTasksforNonExtracts.jpg](instructions258342/BackgroundTasksforNonExtracts.jpg)

Filter to the **Task** of **Refresh Extracts via Bridge** !IMAGE[RefreshExtractsViaBridge.jpg](instructions258342/RefreshExtractsViaBridge.jpg)

This will show the extracts handed off from the Cloud backgrounder to the Bridge client not only for the last 24 hours similar to the jobs page, but beyond that and with more details. Feel free to browse and hover over this data. Notice it gives the Bridge Client it was handed off to.

4. Click on Site Status on the left menu again, and click on the **Bridge Extracts** admin view 

!IMAGE[BridgeExtracts.jpg](instructions258342/BridgeExtracts.jpg)

This is the admin view that will show you the result of the PDS extract as reported by the Bridge client. This admin view will only show published data source extracts.

Feel free to click around this admin view to see if you can find the extract we refreshed during this session. You can also left click the table of results in the bottom right, and click on **view data** to see more information related to these refreshes


#### **Embedded Data Source Extract Refresh:**

Embedded data sources are akin to live queries in Tableau Cloud. The Tableau Cloud backgrounder is engaged throughout the refresh from start to finish, and the recordsets are streamed back to the Tableau Cloud backgrounders. This however changes which admin views you would check to validate the refresh was successful or not.

1. On the left menu, click on the Jobs menu !IMAGE[JobsMenuIcon.jpg](instructions258342/JobsMenuIcon.jpg)

This should show the list of jobs the Tableau Cloud backgrounder has worked/working on in the last 24 hours.

2. The embedded extract refresh jobs would have a Task Type = **Extract Refesh/Creation**, and a Status = **Completed** if the job was succesfully refreshed

3. Alternatively, if you click on the Site Status on the left menu again, and click on the **Background Tasks for Extracts** admin view
!IMAGE[BackgroundTasksForExtractsIcon.jpg](instructions258342/BackgroundTasksForExtractsIcon.jpg)

This admin view will show Tableau Cloud extracts performed by the cloud backgrounder, which includes embedded data source extracts via Bridge.

You will NOT find any embedded data source extracts via Bridge in the Bridge Extracts admin view!

Feel free to click around and hover to find out when the extract was queued/created, started and finished.


---------------------------

## **Hands-On Exercise 3: Bridge Configuration File:**

The goal here is to modify the Bridge configuration file to support the following:
- 20 concurrent extract refreshes
- Creation of an easily readable JSON log for extracts
- Proxy Server

The Tableau Bridge configuration file is located here. Feel free to open up the file below in NotePad or NotePad++
- C:\Users\Admin\Documents\My Tableau Bridge Repository\Configuration\TabBridgeClientConfiguration.txt

There are many different sections of this configuration file. We will focus on just a few of these.

#### **20 Concurrent Extract Refreshes**:
In order to change the Bridge client to support 20 concurrent extract refreshes (10 is the default), we need to adjust the values for the connection pool and the max concurrent refreshes values.

1. Open the TabBridgeClientConfiguration.txt file

2. Change the **size** of the **connectionPool** from **10** to **20** as seen below
!IMAGE[ConnectionPool.jpg](instructions258342/ConnectionPool.jpg)

3. Change the **MaxRemoteJobConcurrency** from **10** to **20** as seen below
!IMAGE[MaxRemoteJobConcurrency.jpg](instructions258342/MaxRemoteJobConcurrency.jpg)

4. Save the TabBridgeClientConfiguration.txt file

The next time the Bridge client is unlinked and relinked, it will now support 20 extract refreshes concurrently!

#### **Creation of an easily readable JSON log for extracts**:
Parsing logs is not fun. If you have a need to do analytics as close to the Bridge client as possible, there is a way to generate an easily readable JSON log file for extract refreshes.

This log file will record an entry AFTER every refresh, success or failure. To do this, do the following:

1. Open the TabBridgeClientConfiguration.txt file

2. Change the **JSONLogForExtractRefresh** to be **true** as seen below
!IMAGE[JSONLogForExtractRefresh.jpg](instructions258342/JSONLogForExtractRefresh.jpg)

3. Save the TabBridgeClientConfiguration.txt file

The next time the Bridge client is unlinked and relinked, a JSON log file will be generated, and will have one record per extract refresh!

#### **Proxy Server**:

It is relatively common to have Bridge communicate with a proxy server which may reside on the edge of the customer network. This allows Bridge to reside deeper inside the network. The proxy can then be responsble for any whitelisting of domains to communicate to Tableau Cloud.

The configuration for a proxy server is done in the **serviceConnectionSettings** object in the configuration file

1. Open the TabBridgeClientConfiguration.txt file

2. Add the following to the **serviceConnectionSettings** object as seen below
!IMAGE[ProxyServer.jpg](instructions258342/ProxyServer.jpg)

3. Save the TabBridgeClientConfiguration.txt file

The next time the Bridge client is unlinked and relinked, it will forward any request to the proxy server!



---------------------------

## **BONUS: Hands-On Exercise 4: Bridge Log Files:**

The goal of this exercise is to understand which log files can be used for further analysis of Bridge behavior.

Earlier in exercise 3, we turned on the creation of a JSON log file for extracts. We will use this and the Live query extract JSON log file as a means to do further analysis from the Bridge log point of view.

For convenience, there is a folder on the desktop called **JSON Logs**. We will be utilizing this for this exercise.

1. Open the **Tableau Log Viewer** application shortcut on the Desktop

2. Click on the **Open** button to present the windows explorer dialog box

!IMAGE[TLV_Open.jpg](instructions258342/TLV_Open.jpg)

3. Change the file extension dropdown, and choose **All files**
!IMAGE[TLV_All_Files.jpg](instructions258342/TLV_All_Files.jpg)

4. Open the **LiveQueryMetrics_** JSON file

5. Scroll through each event and notice the **Type** of each log entry (double click to bring up a seperate dialog box)

Notice that as each log entry advances in the timeline you can see the types change from **create.xml** to **release.xml** and overall how long that took.

6. Repeat the same steps above but open the **ExtractRefreshMetrics_** JSON file

Notice that each entry is an extract event. This log file has both a successfull and a failed extract. Hover over (and double click) each entry to look at the details.

For the successful entry, notice that it will give you the refresh duration (ms) as well as the upload and publish duration (ms) as well as the file size. This is important information!

For the unsuccessful entry, notice that it does give the error message, and how long it took to get to that end state!

