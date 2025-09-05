# 💿 Album-Tracker [MAKE A COPY OF ME!](https://docs.google.com/spreadsheets/d/1OEY8XzBXkchjrP_uRs8bLQuyihuDyMn_Ib1NR_rP_YU/edit?usp=sharing)

Album-Tracker helps you keep track of all the music you promise you will "get around to one day"!

## 🤔Why do I need to keep track of my music?

If you want to listen to more songs on a daily basis than there is time in the day, you need to keep track of the old and the new. This tool lets you maintain a digital shelf to all the albums you have heard and all the ones you want to hear!

## 🎤 I want to know how close I am to finshing my favorite artist's discography!

No worries! You can actively check the percentage of an artists discography you have listened to so far, and when its all done, they will appear in your completed artists tab!

## 🎸What if I am wondering what songs are on the album I want to start listening to?

Album-Tracker uses the [MusicBrainz API](https://musicbrainz.org/doc/MusicBrainz_API) to get you the best matching tracklist for all albums in your list!

## 🥁This seems like a lot of maintenence, won't this be time consuming to keep track of?

All you need to manually input is what albums you would like to listen to. The default tracker already comes equipped with lots of popular artists amongst many genres, but feel free to add anything you want!

## 🎹 What kind of automations does it have?

The tracker will actively track the percentage of albums you have listened to based on the checks to amount of albums there are. By default, it is set to update the list at midnight. When updating, it will sort all albums top to bottom from newest to latest release date, place a "to be released" line for future albums, reset the daily progress boxes, keep a counter of how many albums are celebrating their anniversary today, keep a counter of how many albums you are behind on to a set date, and update your completed artists list!

## ⚙️ Setup Instructions

> [!NOTE]
> All you need is a Google account to get started, follow the instructions to get ready!

1. Click the link at the top of the README or [HERE](https://docs.google.com/spreadsheets/d/1OEY8XzBXkchjrP_uRs8bLQuyihuDyMn_Ib1NR_rP_YU/edit?usp=sharing)
2. Click on "File" > "Make a copy" and then name it whatever you want
3. Click the teal "UPDATE" button to initialize the script
4. A pop up will say "Authorization required" hit "OK"
5. In the next window, select the same Google account you are currently using the spreadsheet with
6. There will be a notice that says "Google hasn't verified this app", this app is completely independent to your Google account and is not connected to any servers, (please feel free to check the source code to double check!). This notice is mainly to let the code run alongside the app, it just isn't verified, nor needs to be verified. Please proceed with caution when trusting code from unknown sources on the internet
7. Hit the "Advanced" text on the bottom left and then "Go to Albums Scripts (unsafe)" - The unsafe marker is there because the script is not verified
8. Click "Select all" for what it can access, and continue
> [!WARNING]
> Please note that if you modify the script it may make unwanted edits to your Google documents including the Album-Tracker
9. The script is all setup! Please follow the instructions in the spreadsheet to explore and happy listening!

## ☀️ Daily Update

1. Once the spreadsheet is setup, you can make it automatically update each day!
2. Hit "Extensions" > "Apps Script"
3. On the new tab lefthand side hit "Triggers"
4. On the bottom right select "Add Trigger"
5. The function will be "customDailyReset", deployment:"Head", event source:"Time-driven", type:"Day timer", time:"Midnight to 1 am", you can optionally choose for it to send you a notice if the trigger fails
6. Click Save
