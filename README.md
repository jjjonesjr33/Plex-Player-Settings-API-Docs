# Plex-Player-Settings-API-Docs
This is to document the Plex player settings and API calls to hopefully find an easy way to control device quality settings for all connected devices.

### There are many steps that are required to replicate the API calls for plex player settings and those will be outlined below:

# Getting your plex device list:
There are three ways to do this:
1. Grabbing it from the list of logged in devices locally (you will need to do this for the other steps since they require atleast one device id)
`https://local ip of sever:port/clients?X-Plex-Token=yourtoken`
It will return something like this:

`<MediaContainer size="1">
<script/>
<Server name="Chrome" host="127.0.0.1" address="127.0.0.1" port="32400" machineIdentifier="xxxxxxxxxxxxxxxxxxxxxxxx" version="4.66.1" protocol="plex" product="Plex Web" deviceClass="pc" protocolVersion="3" protocolCapabilities="timeline,playback,navigation,mirror,playqueues"/>
</MediaContainer>`


2. Getting it from the plex backend API (This is where we start to see what the backend can do)

`https://plex.tv/api/v2/resources?includeHttps=1&includeRelay=1&X-Plex-Client-Identifier=(Machine or client ID)&X-Plex-Token=(token)&X-Plex-Language=en`

This will return a list of every single device connected to your account cool right?

3. Getting it from the network log (I will demo this below)

##### From this point we can now get into the fun stuff like pulling the device settings for every single client connected to your account 

`https://plex.tv/api/v2/user/settings?X-Plex-Client-Identifier=(machine or client ID)&X-Plex-Token=(token)&X-Plex-Language=e`

I won't post the output due to lots of senstive data in that but for our purposes there are only two lines that are really important

`"autoAdjustQuality":true,"remoteQuality":12,`

This tells the device if it should play auto quality and what the remote quality should be
#### The remote quality field for Maximum will be set to -1

Now for the fun part how to update it remotely. You will need to open up plex in a web browser and grab some network logs.
1. Open up the settings section of the plex app and navigate to the quality section

![image](https://user-images.githubusercontent.com/75536101/150021169-318037c1-9bf7-46c2-a745-5bb88f2c1e63.png)

2. Open your network log tab in dev settings
3. Change the quality to anything and save
4. Look for a request to `https://plex.tv/api/v2/user/settings?`
![image](https://user-images.githubusercontent.com/75536101/150021534-cab1eace-c7e4-4a77-baec-556dd46d73a7.png)

5. Open the request and select the payload tab 
![image](https://user-images.githubusercontent.com/75536101/150021812-2b6ce1b0-b73c-4eb3-9529-a77fbc033b68.png)
This has what we need for the payload and you will see your token, client ID, and the payload that was sent.

Click on view source and click show more at the bottom right until the entire payload is shown.
At this point you can copy that into a text editor and modify the two fields mentioned above and whatever else you want

`"autoAdjustQuality":true,"remoteQuality":12,`

Then use a program like postman and do a post request to this endpoint

`https://plex.tv/api/v2/user/settings?X-Plex-Client-Identifier=(machine or client ID)&X-Plex-Token=(token)&X-Plex-Language=e`

Then your device is updated. Change the machine/client ID out for each device you want to update and now you can control the layout and settings of any device on your account remotely

Example: *This is just part of the payload for an example, you need the entire thing unless you want to setup the layout all over again for that device :)*

![image](https://user-images.githubusercontent.com/75536101/150023191-ef141fe8-4f79-4d5b-afaa-6d08b85ce913.png)

Further tesing is needed to determine if adding the user as a home user will allow for other user devices to be modified. I did it once but could not replicate it.
Feel free to add on to this to help expand the knowledge
