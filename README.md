# HackTheBox-Squashed
A walkthrough/ write-up of the "Squashed" box following the CREST pentesting pathway

## Enumeration

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/012deff9-d90f-4761-bacf-164b0cb61047)


`showmount` to get NFS shares:

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/30806c8e-ec0c-4d20-b8ee-1a01e472df48)

Nfs nmap script:

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/677cf147-985a-4d73-85e4-21c92c968a7b)

Owner of the files that are currently unaccesible has the UID of 2017.

After downloading the shares `tree` shows all contained files in an easy to read format:

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/56377cd5-c7fb-4dfe-a099-fc9e392bba35)

Understandably, the `/var/www/html/` share containes files for a web server - potentially the one hosted on port 80.

Server is hosting a simple shop website:

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/28567a26-ca1f-4ede-9776-eb129ede10a6)

Switch user to the account with the "correct" UID to access the web server files locally:

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/d6e1e0d5-50d1-42eb-be50-0f1cc03a1f1c)

Placed a reverse PHP shell within the files of the webserver and accessed it to get a foothold:

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/44c9b5c0-efa4-48dd-a62b-40ae111d2892)

Looking through files I noticed an instance of X within Ross' home folder.
Some research indicated that X is management software for a windowed GUI. Futhermore, the .Xauthority file is used to store credentials via cookies when
authenticating X sessions. Potential cookie steal/impersonation.


Cant access the file as-is:
![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/a281b437-52a5-4cee-ba70-2f42246d88bf)

Returning to NFS shares, it's apparent that I need a UID of 1001 to access the file so I repeated the steps of creating a user with this ID


Cookie enocded onto victim machine for decoding and set by pointing the env `XAUTHORITY` at the file:

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/181f0603-6cab-4898-9f00-e8deb9a6a1bd)
Grab a  screenshot and place in /tmp folder

`xwd -root -screen -silent -display :0 > /tmp/screen.xwd`
-root: select root window
-screen: send GetImage request to root window
-silent: operate silently
-display: specify server to connect to


Pull image from victim:

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/f48c43bd-ee6e-4230-9278-862d99f249d1)

Screenshot can be converted locally to reveal the password of the root account:

![image](https://github.com/HattMobb/HackTheBox-Squashed/assets/134090089/f3cb7829-7145-4542-8bf9-6c0aa3fbb13f)



