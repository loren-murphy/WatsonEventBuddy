# Watson Event Buddy
In this developer journey we will build a Watson Event Buddy which is a cognitive chatbot designed to help users easily find and purchase event tickets based upon a machine learning model.

When the reader has completed this journey, they will understand how to:

* Build a Watson Event Buddy that converses using Watson Conversation
* Use Watson Text to Speech to converse with Event Buddy
* Use Watson Machine Learning to recommend what type of event is of interest to the user (ie: art, music, sport)
* Use Ticketmaster API to search for events based upon location, type and date
* Use Twilio to send SMS text messages with link to purchase tickets from Ticketmaster

![](images/architecture.png)

### Included Components

* [Watson Conversation](https://www.ibm.com/watson/developercloud/conversation.html): Build, test and deploy a bot or virtual agent across mobile devices, messaging platforms, or event on a physical robot.
* [Watson Text to Speech](https://www.ibm.com/watson/developercloud/text-to-speech.html): Converts written text into natural sounding audio in a variety of languages and voices.
* [Node-RED](https://console.bluemix.net/catalog/starters/node-red-starter): A flow-based development tool for wiring together hardware devices, APIs and services.
* [Watson Machine Learning](https://www.ibm.com/cloud/machine-learning): Create, train, and deploy self-learning models

### Featured Technologies

* [Twilio](https://www.twilio.com/): Integrate voice, messaging, and VoIP into your web and mobile apps.
* [Ticketmaster](https://developer.ticketmaster.com/products-and-docs/apis/getting-started/): A leading provider platform for purchasing and selling event tickets for concerts, sports, arts, theater and other events.

# Steps
1. [Clone the repo](#1-clone-the-repo)
2. [Create the Bluemix Services](#2-create-bluemix-services)
3. [Create Machine Learning Model](#3-create-machine-learning-Model)
4. [Configure Watson Conversation](#4-configure-watson-conversation)
5. [Register for Ticketmaster Service](#5-Register-for-ticketmaster-service)
6. [Register for Twilio Service](#6-register-for-twilio-service)
7. [Enable and Configure NodeRED Service](#7-Enable-and-Configure-NodeRED-service)
8. [Deploy Front-End Application](#8-Enable-NodeRED-service)

## 1. Clone the repo

Clone the 'WatsonEventBuddy' locally. In a terminal, run:

`$ git clone https://github.com/loren-murphy/WatsonEventBuddy`

We’ll be using the files within the [`docs`](docs) folder.
* EventBuddy-NodeRedFlow.json
* Ticket_Data.csv
* workspace_wcs-eventbuddy.json

## 2. Create Bluemix services

Create the following services:
  * [**Node-RED Starter**](https://console.bluemix.net/catalog/starters/node-red-starter)
  * [**Watson Conversation**](https://console.ng.bluemix.net/catalog/services/conversation)
  * [**Watson Text to Speech**](https://console.ng.bluemix.net/catalog/services/text-to-speech/)

## 3. Create Machine Learning Model

Create or login to [Data Science Experience](https://datascience.ibm.com/) account

Create a **new project** called **"Event Buddy"**. If not already done, provision an Object Storage and Spark service. ![](images/ML-Service/NewProject.png)

Click the **Assets** tab, and beside **Models** section, click **New Model**![](images/ML-Service/NewModel.png)

Name the model **"Event Buddy ML"**. Select the Spark Service, Machine Learning Service and model type as **Manual**. When done, select **Create**
![](images/ML-Service/NameMLModel.png)

 If you do not have a Machine Learning Service Instance associated with the project, **provision an Instance**.![](images/ML-Service/ML Service.png)

Now we need to upload the dataset to create the Machine Learning model. Select **Add Data Assets** and upload the [`Ticket_Data.csv`](docs/Ticket_Data.csv) file. When done, select **Next** ![](images/ML-Service/LoadData.png)

Select **Segment (String)** as the column value to predict.Feature columns is **All (default)**. Select **Multiclass Classification** as the technique. ![](images/ML-Service/TrainDataSets.png)

In the upper right corner, select **Add Estimators** and choose all 3 Estimators. Click **Add**![](images/ML-Service/Estimators.png)

Once the technique and estimators have been selected, click **Next** to start training the model.![](images/ML-Service/Train.png)

Watson will start training your model based upon the suggested technique and estimators. Once complete, **Select** and **Save** the model which performed best.![](images/ML-Service/TrainedData.png)

Upon completion, you will be taken to the **"Overview"** page where you can see information about the model and dataset.![](images/ML-Service/ModelOverview.png)

Select the **Deployments** tab, and click **Add Deployment** ![](images/ML-Service/Deployments.png)

Select the **Web Service** tab and name the deployment **"Event Buddy Deployment"**. Click **Save**. ![](images/ML-Service/CreateDeployment.png)

Your newly created deployment should now be listed.![](images/ML-Service/DeploymentList.png)

Click on the deployment, and select the **Implementation** tab. Copy & save the **Scoring End-point** URL as you will need this later.![](images/ML-Service/DeploymentImplementation.png)

## 4. Configure Watson Conversation

Launch the **Watson Conversation** tool. Use the **import** icon button the right
![](images/WCS/WCS-upload.png)

Find the local version of [`workspace-wcs-eventbuddy.json`](docs/workspace-wcs-eventbuddy.json) and select **Import**.

Upon successful import, you will see the pre-build Intents, Entities, and Dialog. ![](images/WCS/WCS-Intent.png)

Select **Back to Workspaces** to return to the homepage.

![](images/WCS/WCS-workspaces.png)

Within the "Ticketmaster3" tile, select **View Details**

![](images/WCS/WCS-ViewDetails.png)

Save the **workspace ID** for later use.

![](images/WCS/workspaceID.png)

## 5. Register for Ticketmaster Service
One of the main functions of `Watson Event Buddy` is to search Ticketmaster for an event based upon a Machine Learning model that recommends what type of event a user may like. To find events we use the Ticketmaster Service. Register for a free account at [https://developer-acct.ticketmaster.com/user/register](https://developer-acct.ticketmaster.com/user/register)

Under your user settings, select **My Apps**

 ![](images/Ticketmaster/MyApps.png)

There you will your **Consumer Key**. Copy and Save the key, as you will need it later.

 ![](images/Ticketmaster/TicketmasterKey.png)


## 6. Register for Twilio Service
One of the main functions of `Watson Event Buddy` is to text the link to purchase tickets to the user. To send SMS text messages, we use the Twilio service. Register for a free trial account at [https://www.twilio.com/try-twilio](https://www.twilio.com/try-twilio).

As part of the service, you will be assigned the following values, which you will need to save, they are accessible through your user account settings:
- Account SID
- Authentication Token

![](images/Twilio/twilio.png)

Lastly, you will need a Twilio supplied phone number, where all texts will originate from. To create one, navigate to the *Programmable SMS* option
in the context menu.

![](images/Twilio/number.png)

- Twilio supplied phone number

By default, you will only be able to send SMS text messages to the phone
number you provided to create your account. To send messages to other
numbers, you need to add them at [https://www.twilio.com/console](https://www.twilio.com/).
For each number you enter, the user of the number will be asked to verify
and then be sent a verification code. You will then need to enter this
verification code to complete the registration process.

## 7. Enable Node-RED Service

Launch [NodeRED Starter](https://console.bluemix.net/catalog/starters/node-red-starter)

Under Connections, **Create Connection** to the previous provisioned services: *Text to Speech*, *Watson Conversation*, *Machine Learning*

<p align="center">
  <img width="300" height="300" src="images/NodeRed/connections.png">
</p>



You connection list should look like the following. If asked to restage, select OK.
![](images/NodeRed/ConnectedServices.png)

Click **Visit App URL** to launch the nodeRED editor. Select a username and password to secure the editor

![](images/NodeRed/VisitAppURL.png)

In order for text-to-speech to play audio sounds, we need to install an audio node. From the menu, select **Manage Palette**

<p align="center">
  <img width="300" height="500" src="images/NodeRed/ManagePalette.png">
</p>

Install `node-red-contrib-play-audio` node

![](images/NodeRed/AudioNode.png)


Next we need to import the nodeRED flow [`EventBuddy-NodeRedFlow.json`](docs/EventBuddy-NodeRedFlow.json)

![](images/NodeRed/ImportNodeRedFlow.png)

Copy and paste the contents of the file into the clipboard and select **Import**

![](images/NodeRed/ImportedFlow.png)

You should now see the Event Buddy NodeRED flow

![](images/NodeRed/EventBuddyFlow.png)

We now need to configure nodeRED to connect to our Watson Conversation Service. Double click the **WCS-CONVO** node.

<p align="center">
  <img width="200" height="100" src="images/NodeRed/WCS node.png">
</p>

Input your **Workspace ID**. You saved this ID, in Step 4: Configure Watson Conversation. Once done, select **Done**

![](images/NodeRed/WCSID.png)

Next, we need our nodeRED flow to connect to our deployed Machine Learning Model. Double click the **WML-SparkML** node.


<p align="center">
  <img width="300" height="100" src="images/NodeRed/ML node.png">
</p>

Change the **URL** to the scoring end-point url for your deployed model. You saved this url in Step 3: Create Machine Learning Model. Once done, select **Done**

<p align="center">
  <img width="300" height="300" src="images/NodeRed/MLModel_URL.png">
</p>

Next, we need to setup Ticketmaster API to use your unique Consumer key. You saved this key in Step 5: Register for Ticketmaster Service.

Double click **Set headers for TicketMaster** node. Replace the variable `apikey` with your unique Consumer key. Click **Done**

<p align="center">
  <img width="300" height="100" src="images/NodeRed/TicketMasterNode.png">
</p>



 Lastly, we need to update Twilio Service to use your account. Double click **Text User Event URL** and select **Add new twilio-api**.

 <p align="center">
   <img width="300" height="100" src="images/NodeRed/TwilioNode.png">
 </p>

Enter your **Account SID** and **From Phone Number**. You saved these values in Step 6: Register for Twilio Service. Click **Add**


<p align="center">
  <img width="300" height="200" src="images/NodeRed/Add_Twilio_Account.png">
</p>


Your nodeRED Flow is Complete! Click **Deploy** to deploy your flow.

<p align="center">
  <img width="300" height="100" src="images/NodeRed/Deploy.png">
</p>


## 8. Deploy Front-End Application

The next step is to create a sample application that connects to your NodeRed flow. When you click on the button below, you will be taken to bluemix DevOps page. This will automatically create a new application for you, via the DevOps ToolChain. If you are not logged on to BlueMix you will need to logon.

[![Deploy to Bluemix](https://bluemix.net/deploy/button.png)](https://bluemix.net/deploy?repository=https://github.com/loren-murphy/WCS-Event-Client.git)


Click the deploy button, after about 1 minute or so, your screen will change to something like the following:

<p align="center">
  <img width="400" height="300" src="images/frontend/toolchain.png">
</p>

Click on the **Deliver** tile

<p align="center">
  <img width="300" height="300" src="images/frontend/DeliverTile.png">
</p>


Once the message in the deploy tile has changed to passed from Deploy running, click on the **Rocket** icon under **Last Executed Results**


<p align="center">
  <img width="400" height="400" src="images/frontend/DeployStage.png">
</p>

This will take you to the newly created applications. Click on the **Runtime** link on the left and your screen should change, then click on **environment variables** tab in the middle of the screen. It should look like the following:

![](images/frontend/AppRuntime.png)


In the CONVERSATION_URL value change the "xxxxxx" to the hostname of your NodeRed application. This comes from the NodeRed url where you created your NodeRed flow. Do not include "/red/#. Your values will be different.

<p align="center">
  <img width="500" height="100" src="images/frontend/ConversationURL.png">
</p>


Click the **Save** button. The application will restart.

Click on the Visit App URL. This is at the top of the page. You will land on the following page.

<p align="center">
  <img width="500" height="300" src="images/frontend/ClientWebpage.png">
</p>


# Sample Output

Textual version of a sample conversation with Watson Event Buddy:

<p align="center">
  <img width="400" height="400" src="images/frontend/Convo1.png">
</p>


<p align="center">
  <img width="400" height="400" src="images/frontend/Convo2.png">
</p>

Here is a recap of the services used to perform this conversation:
- **Node-RED:** Used to create a flow which tied together the various Watson services
- **Watson Conversation:** The conversation flow.
- **Watson Text-To-Speech:** Converts Event Buddy replies to sound.
- **Watson Machine Learning Model:** Based upon the user's information (age, gender, profession, martial status, and location) a type of event is recommended to the user (ie: music, arts, sports etc) using a ML model.
- **Ticketmaster Data:** Based upon when the user would like to attend the event, the city, and the type of event returned from ML model, Ticketmaster returns a list of upcoming events.  
- **Twilio:** Texts user the link to purchase the ticket for the show


# Troubleshooting

### Not hearing Watson read the text out-loud
Make sure a browser tab with the "Node-RED" flow is open. The audio output node selected within the flow requires the browser tab to be open.

### Ticketmaster does not return an event
Currently the start and end dates for ticketmaster API call are hard-coded. Within the node-RED flow, open the **Set headers for TicketMaster** node. Make sure the start and end date is set appropriately. The Ticketmaster API currently does not search for past events.

### Watson Conversation stops and does not return anything
Watson Conversation is currently coded for "happy-path" only. It is recommended you follow the sample output path above. You can further extend the Watson Conversation service to include "non-happy paths". For instance, the person does not want Watson's help in finding an event or they do not like the recommended type of event.

### Twilio is Not Texting user
Make sure the number you are texting has been registered within the Twilio Application. If not, add the number at [https://www.twilio.com/console](https://www.twilio.com/).
For each number you enter, the user of the number will be asked to verify
and then be sent a verification code. You will then need to enter this
verification code to complete the registration process.
