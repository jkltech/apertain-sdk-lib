A(P)ertain SDK v2.2.3 for Android - Getting Started
=================================================

This document describes how to get started using the A(P)ertain SDK v2.2.3 for Android.

Before you Begin
----------------

Before implementing the SDK, make sure you have the following:

* Download the A(P)ertain SDK from [here](https://github.com/jkltech/apertain-sdk-lib).
* Import the SDK to the IDE Workspace and add reference to the Project.

(In Eclipse, Right click the project. Select properties -> Android. In Library section click Add button and select the A(P)ertain SDK library, then click Apply and Ok.)

Getting Started
---------------

### 1. Permissions for A(P)ertain

A(P)ertain tries to be as non-intrusive with minimal permissions as required to provide the developers the best possible App data as well as App User Analytics as possible.

The developers can update their project’s AndroidManifest.xml file by adding the following permissions:

    <!-- A(P)ertain Essential Permissions -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" /> 
    <!-- A(P)ertain Optional Permissions -->
    <uses-permission android:name="android.permission.GET_ACCOUNTS" />
	
### 2. Initialize the SDK

In the Activity that loads your app write the following code in onCreate(), to initialize the A(P)ertain SDK. This should be called only once in your App/Game on the first Activity loaded by the App/Game.

The App Unique ID and User App Signature are generated as you add the App in A(P)ertain SaaS backend. These unique ids will identify the App and the User registered with the A(P)ertain SaaS backend.

	String appUniqueID = "xxxxxxxxxx";
	String userAppSignature = "xxxxx";
	try {
		ApertainFactory.init(context, appUniqueID, userAppSignature);
		ApertainFactory.setUserName(userName); //Optional. If game/app has username add it here
	} catch (ApertainException ape) {
		Log.e(TAG, "Error while initializing A(P)ertain " + ape.getMessage(), ape);
	}

### 2.1 Destroy the A(P)ertain Object

In onDestroy() call the following code to cleanup the A(P)ertain instance created.

	ApertainFactory.destroyAPertainInstance(context);

### 3. Logging User Experience

From within the App/Game, a user tends to experience various levels of emotions as they pass-through various obstacles or processes intended to be executed within the App/Game. When the Mobile Developer records such experiences it enables the App to be more pro-active based on User Experiences to provide additional means to execute the App/Game processes. 

A(P)ertain allows such User Experiences to be recorded and then use those specific experience values to further add relevant process flows related to the App/Game.

### 3.1 Recording a Positive User Experience

Whenever a positive experience occurred (eg., Level Completion in a game or Achieving some Milestone in a fitness App, etc.,) the developer can add this code to record the positive experience gained by the user.

	Apertain.logUserXp(Activity, [Event_Name], XP);

	Example: Apertain.logUserXp(this, "WorkoutComplete", Apertain.POSITIVEXP);

Here, the [Event_Name] is used as a User Experience Parameter which can be further used in Rules defined for Pertain Engine as well as Smart Rating & Feedback Prompts..

### 3.2 Recording a Negative User Experience

When a user gets a negative experience (eg., Level Failed or invalid process input) you can add this code to record the negative experience for the user.

	Apertain.logUserXp(Activity, [Event_Name], XP);

	Example: Apertain.logUserXp(this, "LevelFailed", Apertain.NEGATIVEXP);

### 3.3 Recording a Neutral Experience

When a user gets a Neutral experience (neither positive or negative) you can add this code to record the neutral experience for the user.

	Apertain.logUserXp(Activity, [Event_Name], XP);

	Example: Apertain.logUserXp(this, "NameEntered", Apertain.NEUTRALXP);

### 4. Smart Rating Prompt Configuration

In-App Rating Prompt Flow an intelligent program flow that shows the Rating Prompt to each user according to their behavior & past experiences within the App/Game. The prompt flow is shown based on the experience user has gained in the App/Game according to the configuration made by the developer in A(P)ertain SaaS backend.

### 4.1 Showing Smart Rating Prompt

The following code shows the Smart Rating Prompt when the conditions as configured in A(P)ertain SaaS backend are met. 

When the Rating Prompt shows up, it takes the user to rate your app in Play Store (or Amazon App Store). Use the following code to show the Rating Prompt as the related conditions are met. This can be called from any part/process of your activity where you like to show the Rating Prompt.

	Apertain aPertainInstance = null;
	try {
		aPertainInstance = ApertainFactory.getApertainInstance(context);
	} catch (ApertainException ape) {
		Log.e(TAG, "Error while creating A(P)ertain Instance" + ape.getMessage(), ape);
	}


	if (aPertainInstance != null) {
		aPertainInstance.showRatingFlowIfConditionsAreMet("<User Experience Parameter>");
		// Empty String in the above method means a cumulative Positive Experience Count.
	}
	
If you would just like to show your rating prompt from a menu item or your own smart conditions, please use the following code,

	if (aPertainInstance != null) {
		aPertainInstance.showRatingPrompt();
	}

As showing the Rating Prompt popup will invade the flow of your App, if you would like to pause and resume your underlying Activity or Fragment code, please implement the ResumableActivity with methods pauseActivity() and resumeActivity() to add a callback for pausing and resuming the underlying activity or Fragment. 

### 5. A(P)ertain App Support Chat Interface

A(P)ertain App Support Chat Interface provides a means messenger style communication (conversation) interface for the user and the Mobile Developer. The below code provides a native A(P)ertain Support icon view which when clicked will open up the App Support Chat Interface. 

As a developer you can place this view in any layout of your choice to let the user open up the App Support Chat Interface.

	Apertain aPertainInstance = null;
	try {
		aPertainInstance = ApertainFactory.getApertainInstance(context);
	} catch (ApertainException ape) {
		Log.e(TAG, "Error while creating A(P)ertain Instance" + ape.getMessage(), ape);
	}

	View view = aPertainInstance.getAppSupportChatterView();
	layout.addView(view);

Most developers add the App Support Chat Icon to the layout defining their own Action Bar for the App/Game.

Alternatively you can have your own icon to show the Support Chat Interface and add the click listener to that layout or button and call the following code inside onClick:

	aPertainInstance.showApertainAppSupportChatter(); 

As showing the In-App Support Chat will invade the flow of your App, if you would like to pause and resume your underlying Activity or Fragment code, please implement the ResumableActivity with methods pauseActivity() and resumeActivity() to add a callback for pausing and resuming the underlying activity or Fragment.

### 6. Tracking Activities & Processes

A(P)ertain as part of the App Analytics feature needs to keep track of the Activities and Processes which are initiated by the App/Game. An Activity is an Android Activity within which there can be many processes initiated and concluded. 

Within the Activity’s class onStart, onStop, onPause, onResume methods can be overridden to include A(P)ertain’s Activity Tracking code. Similarly whenever the Developer initiates a special process within the Activity which needs to be tracked, specific code can be written by naming the process to keep track of it.

**NOTE**: A(P)ertain start, pause, resume, stop code should be called on all the activities.

### 6.1 Start A(P)ertain Activity Tracking

In onStart() add the following code to start tracking an Activity

	Apertain.start(this);

### 6.2 Pause A(P)ertain Activity Tracking

In onPause() add the following code to mention to A(P)ertain this Activity is paused 

	Apertain.pause(this);

### 6.3 Resume A(P)ertain Activity Tracking

In onResume() add the following code to mention to A(P)ertain this Activity is resumed

	Apertain.resume(this);

### 6.4 Stop A(P)ertain Activity Tracking

In onStop() add the following code to mention to A(P)ertain this Activity is stopped

	Apertain.stop(this);

### 6.5 Start A(P)ertain Process Tracking

Sometimes we create separate streams of work within the Activity which engage with the User in a manner to start and complete a process. For example, we may be in a screen where the user initiates in-app purchase, now even though the Activity is not just intended for in-app purchase, by creating a separate process within the Activity it becomes difficult for the developer to ascertain the result of this process and why User successfully purchased or not. 

Hence by tracking the process independent of the Activity as a named process, A(P)ertain enables the developer to track User engagement outside the initial screen which initiated the process.

Here, you have the code for starting the process tracking.

	Apertain.startProcess([processName], Activity);

	Example: Apertain.startProcess(“In-App-Purchase”, this);

### 6.6 Stop A(P)ertain Process Tracking

Here, you have the code for stopping the tracking of a process as it completes.

	Apertain.stopProcess([processName], Activity);

	Example: Apertain.stopProcess(“In-App-Purchase”, this);

**Note**: All Process tracking are ceased when the Activity which starts the process tracking is stopped and destroyed.

### 7. App Analytics Uploader Service

A(P)ertain uses a background process to initiate uploading of App Analytics parameters into the A(P)ertain Server to analyze and provide insights into App User Behavior. To enable this the Uploader Service has to be initiated within the AndroidManifest.xml on every App.

Please enter the below tags within the Application tag of your App to enable the UploadService for A(P)ertain.

	<service 
	    android:name="com.jkl.apertain.service.UploadService"
	    android:label="UploadService" >
	</service>

### 8. Social Share

Social Media is a wonderful way to let your Users Engage with their friends as well as publicize your App on your behalf among their network of friends.

To easily enable this type of sharing among Users, we have provided an intuitive User Interface. It is designed as a Popup that contains the share buttons on your interested Social Networks. Clicking this button allows users to share links and descriptions directly to Social Networks such as Twitter, Facebook & WhatsApp.

**NOTE**: We only Support the above Social Networks now!

If one of these social networks is not installed in the Device it will show default share options E-Mail & Messaging.

If the Device any of the above Social Networks and E-Mail & Messaging is also unavailable, then it would show up with “Device doesn’t have Supported Sharing Options” as a Toast Message.

This User Interface can be initiated with this function call having Four arguments:

1. Arg 1: Integer array of String Resource array that mention the social network software.
2. Arg 2 : String Resource that mention the App URL or any other to be shared URL.
3. Arg 3 : String Resource that mention Text to share.
4. Arg 4(Optional) : String Resource that mention Subject. Use this only if you want to share via E-Mail.

Example:

	int[] shareArray = {R.string.facebook, R.string.twitter, R.string.whatsapp};
	int url = R.string.sharing_app_url;
	int textToBeShared = R.string.text_to_be_share;
	int emailSubject = R.string.subject_to_share;
	   			 
	if (apertainInstance != null)
	{
		apertainInstance.showSocialSharePopupUI(shareArray, url, textToBeShare);
	
(or)

		apertainInstance.showSocialSharePopupUI(shareArray, url, textToBeShared, emailSubject);
	}

### 9. Customer On-Boarding UI

All Applications need a relevant on-boarding User Interface to explain What the App is about and How to use the App. Customer on-boarding is nothing but an inituitive way to introduce the App’s premise to the end users. This interface is your chance to inform, to inspire, and to get the user excited about using your App. This is one of the commonly used aspects of "User Experience" (UX) design, but is often overlooked by the App Developer.

The Customer On-Boarding UI will be used as a self explanatory introduction to your application. A(P)ertain provides a powerful On-Boarding UI which is completely customizable. All you have to do is to decide how many Screens needed for the app intro and choose of Images, Title Text, Description and Background color for each Screen respectively. 

Here is the Sample UI of a single screen in A(P)ertain On-Boarding UI.

![Image of Customer On-boarding UI Design Sample](https://apertain.com/app/prd/api/customer_onboarding_ui_sample.jpg)

## A(P)ertain OnBoardingUI has Four arguments:

1. Integer array of resource that mention images for each page.
2. Integer array of resource that mention Title text for each page.
3. Integer array of resource that mention Description Text for each page.
4. Integer array of resource that mention Background colors for each page.

The array size determines the number of Screens in the Customer On-Boarding UI. For example the array for each argument is four, you will get four Screens.

**NOTE**: It is recommended to keep the On-Boarding UI to 3-5 intriguing screenshot or usage introductions.

## Use the following import for FragmentManager:

	import android.support.v4.app.FragmentManager;

## To get the A(P)ertain Customer On-Boarding UI use the following code snippet: 

	int[] bgImages = {R.drawable.food, R.drawable.movie};
	int[] bgColors = {R.color.color1, R.color.color2, R.color.color3, R.color.color4};
	int[] titleText = {R.string.title1, R.string.title2, R.string.title3, R.string.title4};
	int[] descriptionText = {R.string.description1, R.string.description2, R.string.description3, R.string.description4};

	private void moveToAPertainOnBoardingFragment()
	{
		FragmentManager fragmentmanager = getFragmentManager();
		ShowUserOnboardUIFragment onboardingUIFragment = 
		new ShowUserOnboardUIFragment(bgImages, titleText, descriptionText, bgColors);
		onboardingUIFragment.setCancelable(false);
		onboardingUIFragment.show(fragmentmanager, null);	
	}

