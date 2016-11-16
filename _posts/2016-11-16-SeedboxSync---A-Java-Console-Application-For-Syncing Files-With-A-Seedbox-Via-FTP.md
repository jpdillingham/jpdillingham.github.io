---
title:  "SeedboxSync - A Java Console Application For Synching Files with a Seedbox via FTP"
excerpt: "Enables download automation (via Sonarr, SickRage, etc.) by monitoring a local folder for new uploads (.torrents) and a remote FTP server for new downloads."
header:
  teaser: "seedboxsync.png"
categories: "Applications"
---

I've been using a [seedbox](http://seedboxgui.de/guides/what-is-a-seedbox/) for torrents for a while; The increased privacy is important to me and the convenience is nice too.  I'm not a big downloader 
anymore, but years ago when I was using private trackers a seedbox really would have come in handy to keep my ratios in the green.

Most modern torrent clients have a "black hole" feature; you drop .torrent files into a folder, the client detects them, loads them, downloads them, then moves the completed files to a specific folder.
If you're manually adding torrents this doesn't have a huge benefit, however if you have download automation software like Sonarr, SickRage, CouchPotato, or even the now-defunct Sickbeard, this can 
streamline the downloading process much like SABnzbd has done for usenet downloads.

These automated downloaders can't take advantage of a seedbox, however, as the "black hole" folder lives on an FTP server, and none of the software I've found supports FTP integration.  For this reason,
I wrote [SeedboxSync](https://github.com/jpdillingham/SeedboxSync).  

This Java console application scans a local "black hole" folder and uploads anything in it to the "watch" or "black hole" folder on the FTP server for your seedbox.  
It then scans the remote downloads directory and downloads anything in the folder that hasn't been downloaded yet.

The sections below walk you through the setup for the application, or you can head over to the [SeedboxSync GitHub repo](https://github.com/jpdillingham/SeedboxSync) and read the instructions and download the latest release there.  Have a look through the code,
and let me know of any issues via the GitHub Issues feature.

# Prepare

Configure the torrent client from your seedbox provider to move completed downloads to a specific folder upon completion, and to monitor a folder
for new torrent files.  The configuration for the ruTorrent is shown below as an example.

![ruTorrent Example](http://jpdillingham.github.io/images/rutorrent-setup.PNG)

Your client settings may differ.

# Install

This application requires the [Java JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html), so you'll need to download and
install that first.

Download the latest release from the Releases tab above and place the file in a folder on the host machine.  You'll create a configuration file, and the application
creates a database file and a log directory for log files, all stored in the root directory.

# Configure

Choose (and/or create) two folders: one to contain downloaded files and another to contain files to upload.  You'll also want to gather the login information
for your seedbox account.

Using your favorite text editor, create a file named ```config.json``` and paste the following text into it:

```
{
	"server": "[server address]",
	"port": [port, probably 21],
	"username": "[your username]",
	"password": "[your password]",
	"interval": [number of seconds between synchronizations, in seconds],
	"remoteDownloadDirectory": "[path to the completed downloads folder on your FTP]",
	"localDownloadDirectory": "[path to your chosen downloads folder]",
	"remoteUploadDirectory": "[path to the watch folder on your FTP]",
	"localUploadDirectory": "[path to your chosen upload folder]"
}
```

Replace the values in brackets with your settings.

# Launch

From a command prompt, issue the command ```java -jar SeedboxSync-XXX.jar``` (where XXX is the current version) to launch the application.

![SeedboxSync Startup](http://jpdillingham.github.io/images/seedboxsync-startup.PNG)

# Sync

To download a new torrent automatically, save the .torrent file to the local upload directory.  The application will upload the file on the next synchronization and the torrent
client running on your seedbox should add it and start the download.  When the download is finished the client should move the files to the completed folder and the application
will locate and download the files to your local download directory the next time the synchronization is executed.