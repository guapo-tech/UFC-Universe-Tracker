# UFC-Universe-Tracker

## About
UFC Universe Tracker provides the tools to transform your Salesforce Developer org to a user-friendly UFC tracker.

## Configuration
**Note: It is really important that you match your field names with the names provided in this manual, they have to be exact matches, or you will run into problems**
**If your org is in Classic, you will have to switch to Salesforce Lightning, via the link in the top right.**

1. Go to https://developer.salesforce.com/signup and sign up for a free Developer Edition.
2. After confirming your e-mail address, you should be taken to the Setup page, if not, click on the gear icon in the right top, and click 'Setup'.
![Setup](/screen1.png?raw=true)

3. In the search bar on the left, type 'App', and click on 'App Manager'.
![App Manager](/screen2.png?raw=true)

4. In the top right, click 'New Lightning App'. Enter the name you want, add a logo (optional) and change your primary branding color (optional).
![New app](/screen3v2.png?raw=true)

5. Click next a few times until you reach the 'User Profiles' screen. Add System Administrator from the 'Available Profiles' list to the 'Selected Profiles' list. Click 'Save & Finish'.
![New app](/screen5.png?raw=true)

6. You should return to the Setup overview page, if not, click on the gear icon in the right top, and click 'Setup'. Click on 'Object Manager' on the top of your screen. From the Object Manager page, click on 'Create' on the top right, and click 'Custom Object'.
![Object Manager](/screen6.png?raw=true)

7. Enter 'Event' in the Label field, and 'Events' in the Plural Label field. Make sure you have the following checkboxes ticked, and save.
![New object](/screen7.png?raw=true)

8. This takes you to the object overview page. Click on 'Fields & Relationships' in the left hand menu. Click new on the top right.
![New field](/screen8.png?raw=true)

9.Select 'Date' as Data Type, click Next. Enter 'Date' as Field Label. Click Next twice, and the click 'Save & New'.
![New field](/screen9.png?raw=true)

10. Repeat this process for the next two fields: 'Venue' (Datatype: picklist) and 'Finished' (Datatype: Checkbox).
![New field](/screen10.png?raw=true)

11. Create a new object 'Fighter' (refer to step 6 if you forgot how), with the fields 
- 'Champion' (datatype: Checkbox) 
- 'Country' (datatype: text)
- 'Wins' (datatype: number)
- 'Losses' (datatype: number)
- 'Draws' (datatype: number)
- 'No Contest' (datatype: number)
- 'Injury' (datatype: picklist, you can create your own injuries here, same with the venues picklist before.)
- 'Return to Fitness' (datatype: date)
- 'Weightclass' (datatype: multi-select picklist, add your weightclasses here)
- 'Rank' (datatype: number)
- Photo URL' (datatype: text)

11. The next fields you'll have to create a are little different, so-called 'Formula fields'. Create a new field on the 'Fighter' object, select 'Formula' as datatype. Field label should be 'Record', and Formula return type 'Text'. The formula should be: 
>TEXT(Wins__c) & ' - ' & TEXT(Losses__c) & ' - ' & TEXT(Draws__c) & ', ' & TEXT(No_Contest__c)
Make sure the API names of the fields are matching with those of the fields you just created.

12. Create another Formula field, called 'Photo', with Formula return type 'Text'. The formula should be:
>IF(NOT(ISBLANK(Photo_URL__c )),IMAGE( Photo_URL__c , "PHOTO found", 450,296),IMAGE( "https://1.bp.blogspot.com/-uy0g7RCqnAc/W0F3sv_LX-I/AAAAAAAAsfg/MGxG_ErXLh0Fnqlb_BQPVJPbWi__1nMKQCLcBGAs/s1600/Gokhan-Saki_634220_right30.png", "NO INSTRUCTIONS"))
This adds a 'Unknown Fighter' image to all fighters in your universe, you can change the image URL if you want to something else.

13. Next we are going to create 'Lookup Relationships'. Create a new field on the 'Fight' object, called: 'Event' (datatype: Lookup Relationship). Select 'Related To Event', and click Next. Field Label 'Event', and click Next(3x) and click 'Save & New'.

14. You'll have to create two more of these 'Lookup Relationship' now.
- Red Corner (related to: 'Fighter')
- Blue Corner (related to: 'Fighter')

15. Onto the creation of our final Custom Object. Create a new Custom Object called 'Fight' with the fields
- Boutorder (datatype: number)
- Championship / Main Event (datatype: checkbox)
- Result (datatype: picklist, with the following values:
  - 'Red - Decision'
  - 'Red - (T)KO'
  - 'Red - Submission'
  - 'Blue - Decision'
  - 'Blue - (T)KO'
  - 'Blue - Submission'
- Fight Result (datatype: formula, see below)
>CASE(Result__c,
"Red - Decision", Red_Corner__r.Name & " via " & "Decision",
"Red - (T)KO", Red_Corner__r.Name & " via " & "(T)KO",
"Red - Submission", Red_Corner__r.Name & " via " & "Submission",
"Blue - Decision", Blue_Corner__r.Name & " via " & "Decision",
"Blue - (T)KO", Blue_Corner__r.Name & " via " & "(T)KO",
"Blue - Submission", Blue_Corner__r.Name & " via " & "Submission",
"TBD")



We are done with the configuration of Objects and Fields, for now. Now you can import your roster. I have added UFCRoster.csv to the repository, you can use this as template. I recommend to add another column for the fighter ranks.
From Setup, enter Data Import Wizard in the Quick Find box, then select Data Import Wizard.
Click Launch Wizard.
Click Custom Objects.
Select Fighters, then select Add new records.
Set Match by Name.
Select the CSV file that contains your import data, and click Next.
Map the Fighter Name field to the Fighter column in your CSV file, and map the other fields.
Click Next.
Review the import settings, and then click Start Import.
That should do it.

16. Open the 'Developer Console', by clicking the gear icon in the top right.
![dev console](/screen12.png?raw=true)

17. Click File > New > 'Apex Class'. Name: 'EventTriggerHandler', and copy/paste the contents of /classes/EventTriggerHandler from this repository, and save.
18. Click File > New > 'Apex Class'. Name: 'FightTriggerHandler', and copy/paste the contents of /classes/FightTriggerHandler from this repository, and save.
19. Click File > New > 'Apex Class'. Name: 'EventOverviewController', and copy/paste the contents of /classes/EventOverviewController from this repository, and save.
20. Click File > New > 'Apex Class'. Name: 'RankingOverviewController', and copy/paste the contents of /classes/RankingOverviewController from this repository, and save.

21. Click File > New > 'Apex Trigger'. Name: 'Event', sObject: 'Event__c', and copy/paste the contents of /triggers/Event.apxt from this repository, and save.
22. Click File > New > 'Apex Trigger'. Name: 'Fight', sObject: 'Fight__c', and copy/paste the contents of /triggers/Fight.apxt from this repository, and save.

23. Click File > New > 'Visualforce Page'. Name: 'EventOverview', and copy/paste the contents of /visualforce/EventOverview.vfp from this repository, and save.
24. Click File > New > 'Visualforce Page'. Name: 'RankingsOverview', and copy/paste the contents of /visualforce/RankingsOverview.vfp from this repository, and save.

25. Go to the setup page, and enter 'tabs' in the quick find box. Click on 'Tabs' under 'User Interface'. Click on 'New' in the 'Custom Object Tabs' area, and create tabs for the objects you created (Event, Fight, Fighter).

26. Go back to the App Manager (step 3), find your UFC app in the list and click 'Edit'.

27. Under 'Navigation Items' on the left hand side, find your created tabs under 'Available Items' and move them over to the right 'Selected Items'.
![app manager](/screen13.png?raw=true)

28. On the Setup page, enter 'Visualforce' in the quick find box, and click 'Visualforce Pages' under 'Custom Code'. You should see the pages you created. Click edit (need to do this for both!), and check the 'Available for Lightning Experience, Lightning Communities, and the mobile app'.
![vf](/screen14.png?raw=true)

29. Click on 'Object Manager' from Setup page, and navigate to the 'Fighter' object, click on it. Click on 'Page Layouts' in the left side menu and click on 'Fighter Layout', and make sure it matches the screenshot below. You can drag and drop items.
![page layout](/screen15.png?raw=true)

30. Still on the object page, in the left hand menu, select 'Lightning Record Pages', and edit the default one. On the left side, you should see a list with components. Look for the component 'Visualforce' and drag it to the right of your page, as in the screenshot below.
![page layout](/screen16.png?raw=true)

31. Repeat step 29 and 30, but now for the Event page layout, screenshot below for reference. **Note: There is also a standard object called 'Event', we don't use this one. Always check that the API name of your Event object is 'Event__c'.**

![page layout](/screen17.png?raw=true)
![page layout](/screen18.png?raw=true)

## Use
The tool consists of these features
- Event creator
- Automated ranking
- Injury generator

### Set up an Event
First of all, make sure you are in the right App. Click on the 9 dots on the top left, this opens the App Launcher, search for your app and launch it!

Click on the downward arrow on the Event tab, and click 'New Event'.

![page layout](/screen21.png?raw=true)

This is is pretty self explanatory, pick a Name, Date and Venue, and do not check 'Finished' yet, and click save. This takes you to the Event Overview page, but there is not a lot to see yet.

On the right hand side, you should see 'Fights (0)', with an option to add Fights.

![page layout](/screen22.png?raw=true)

Click on New.

This is the New Fight Wizard, also pretty self explanatory. 

![page layout](/screen23.png?raw=true)

You can fill in anything for Fight Name, it will automaticly update to 'Red Corner Name vs Blue Corner Name'. Result doesn't have to be filled in right away. In the boutorder field, you can determine where you want to place this fight on the card.

Once you've added all your fights, and the results are in, you can 'finish the event'.

On the Event Overview page click Edit. Check the 'Finished' checkbox and click save. This triggers the automated rankings and the injury generator.

**NOTE: Do this only one time, because every time you save an event with the checkbox 'Finished' checked, it will run the automated rankings and the injury generator. Do you want to edit an event after it has finished? Just uncheck the finished box.**

At the moment, the rankings are only visible on the Fighter overview page, but I'm working on a separate object.

## Rankings
Right now the automated rankings work as follows;

If winning fighter has lower rank than losing fighter, winning fighter will get the rank of losing fighter, losing fighter and everyone below drops one spot.

If winning fighter wins a championship match as a challenger, the original champion will get the rank of the challenger.

If winning fighter has higher rank than losing fighter, nothing happens.

If winning fighter is unranked and beats a ranked fighter, winning fighter gets the rank of losing fighter and everyone below drops one spot.
