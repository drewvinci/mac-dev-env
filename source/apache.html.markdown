---
title: Apache
---

> **Links:** [Homepage](http://httpd.apache.org/) | [Downloads](http://httpd.apache.org/download.cgi) | [Documentation](http://httpd.apache.org/docs/2.4/)  
> **Dependencies:** [APR](/apr/) | [APR-util](/apr-util/) | [PCRE](/pcre/)  
> **Version:** <span id="version">2.4.33</span>

The **Apache HTTP Server** is an open-source web server that was released in 1995.


### Get the Code

Switch to `/usr/local/src` and download the source package.

	cd /usr/local/src
	curl --remote-name http://apache.mirror.iweb.ca//httpd/httpd-VERSION.tar.gz

Extract the archive and move into the folder.

	tar -xzvf httpd-VERSION.tar.gz
	cd httpd-VERSION


### Compile and Install

Configure, compile and install into `/usr/local/mac-dev-env/apache-VERSION`.

	./configure --prefix=/usr/local/mac-dev-env/apache-VERSION --enable-so
	make
	make install

Create a symbolic link to `/usr/local/apache`.

	sudo ln -s mac-dev-env/apache-VERSION /usr/local/apache


### Configuration File

Edit Apache's configuration file.

	nano /usr/local/apache/conf/httpd.conf

Copy and paste the following text at the end of the aforementioned file. Make sure you replace the `<username>` string (3 times) with your macOS short username. This configuration only allows access from your local computer; all outside access is blocked.

	ServerName dev.local
	User <username>
	Group staff

	DocumentRoot "/Users/<username>/Sites"
	ErrorLog "/usr/local/var/log/apache.log"
	CustomLog "/usr/local/var/log/apache.log" common
	<Directory "/Users/<username>/Sites">
	  Options All
	  AllowOverride All
	  IndexOptions NameWidth=*

	  Require all denied
	  Require host localhost
	  Require host 127.0.0.1
	</Directory>

You also need to disable the `ErrorLog` and `CustomLog` directives in the Apache configuration file (leaving the above directives intact). Otherwise, Apache will log the same information in two separate locations.


### Web Root

Create a folder that will contain your web sites and applications. I keep my projects in `~/Sites`. You can place your projects wherever you'd like but make sure you update the path when mentioned in this article.

	mkdir -p ~/Sites


### Shell

Execute the following lines to update your [Bash](http://en.wikipedia.org/wiki/Bash_%28Unix_shell%29) startup script.

	echo 'export PATH=/usr/local/apache/bin:$PATH' >> ~/.bash_profile
	echo 'export MANPATH=/usr/local/apache/man:$MANPATH' >> ~/.bash_profile

Load the new shell configurations.

	source ~/.bash_profile


### Manual Start/Stop

To start the HTTPd.

	sudo apachectl start

To shut down the HTTPd.

	sudo apachectl stop


### Automatically Start the Server at Boot

Create a configuration file for [Launchd](http://en.wikipedia.org/wiki/Launchd).

	sudo nano /Library/LaunchDaemons/org.apache.apache2.plist

Do not name the file *org.apache.httpd.plist* as it collides with a similarly named file for loading the macOS web server.

Copy and paste the following text into the aforementioned file.

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
	<plist version="1.0">
	  <dict>
	    <key>Label</key>
	    <string>org.apache.apache2</string>

	    <key>ProgramArguments</key>
	    <array>
	      <string>/usr/local/apache/bin/httpd</string>
	      <string>-D</string>
	      <string>FOREGROUND</string>
	    </array>

	    <key>StandardOutPath</key>
	    <string>/usr/local/var/log/apache.log</string>
	    <key>StandardErrorPath</key>
	    <string>/usr/local/var/log/apache.log</string>

	    <key>RunAtLoad</key>
	    <true/>
	    <key>KeepAlive</key>
	    <true/>
	  </dict>
	</plist>

Register with Launchd and start the server.

	sudo launchctl load /Library/LaunchDaemons/org.apache.apache2.plist

Deregister with Launchd.

	sudo launchctl unload /Library/LaunchDaemons/org.apache.apache2.plist


### Verify the Installation

Verify that you have successfully installed **Apache**.

	httpd -v

Visit your web server root page.

[`http://localhost/`](http://localhost/)
