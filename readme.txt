Welcome to Wakaba-reddy, pre-release version.

There's no proper documentation yet.

The script behaves a lot like wakaba. I stole most of the
translations from other wakaba repositories. Many thanks to parashevody for their work on that.

The script lives in a directory that needs to be writable by the web
server process. This might mean you need to set it to full permission,
777. It wants three subdirectories, src, thumb and res, and those need
to be writeable too.

Edit the config.pl files according to your needs. You should at least
configure the admin password and SQL settings.

For SQL backends, the script has been designed to be able to use either
mysql or sqlite. You'll need to install Perl drivers for the one you
choose. 

You should have ImageMagick "convert" command available for thumbnailing.

It also either needs the Digest::MD5 module, which is standard in
newer Perl versions but not in older, or the external "md5sum"
command. To make charset conversion work properly, you need Perl 5.8.0
or higher. On older perl versions, it tries to work entirely in ISO
Latin-1, but this is untested, like much else. Furthermore, you
should make your webserver send the proper charset code for the
pages the script creates. With Apache, make a .htaccess files with
"AddCharset utf-8 .html" in it.

When the script is run the first time, it tries to create its
databases, and make the first HTML page. Hopefully this works,
otherwise you might need to mess around with it in uncomfortable ways.
The admin interface as an option to re-make all the static HTML pages,
which is necessary if the script is moved, or if you change options in
the config file that affect the pages directly. If everything breaks
and you can't get to the admin interface, try accessing
http://.../wakaba.pl?action=rebuild&admin=ADMINPASSWORD

Well, good luck. Report problems and bugs to nobody. Nobody will help you.

- some1suspicious

















Now seriously, the purpose of this project is to make wakaba great aga... Wait, it's already great. Well, it's not perfect. Especially if you don't want to mess with it too much to just get it up and running.

* It lacks a lot of simple features that you might have seen earlier on imageboards using almost original wakaba. I'm not talking about fastCGI support and other stupid and useless (as for year 2019 small imageboard) things. But rather really simple features like noko and a bit more up to date HTML.
* Mod pannel is the most terrible thing you can imagine.
* For some reason the latest version (from !WAHa.06x36's server) contains a few typos that might cause confusion and prevent a newbie from even running it normally right after installing.
* It has several vulnerabilities. It's not about security and privacy. Just admin's minor pain in the ass.

This reasons are enough to modify it a bit and leave publically accessible.
* Not to completely rewrite it and make it a totaly different thing.
* Not to add fancy sh1t and think that someone might be interested in that bloated script with tons of dependencies while it's already outdated and complicated inside.
