# U-SRV
Universal file server and file storage service for YOURLS

Provides links to files while obfuscating filesystem paths, allowing easy, secure access to files between plugins or from 3rd party scripts.

## In detail:
Given the following parameters:

1. key => Access key
2. id  => plugin ID
3. fn  => filename

This script will:

1. Retrieve the file store location per plugin, can be from a database
2. Retrieve a particular file from the store (in or preferably out of the server doc root)
3. Return a time limited link to that file

## Use (with YOURLS)
1. Copy the `usrv` folder of this repo into `/path/to/YOURLS/user/plugins/`
2. Enable the plugin as any other.

The script should automatically
1. Copy  `srv.php` to `/path/to/YOURLS/user/pages/`
2. Install a database table to store uploaded file data
3. Create the default cache location

At once you should be able to
1. Upload files via a web browser that will be accessible via U-SRV
2. Call U-SRV with a `GET` request from `https://sho.rt/srv/`
3. Use any plugin that utilizes U-SRV (listed below)

### Parameters
To be sent as a `GET` request. All parameters are required.
  - eg. `https://sho.rt/srv/?id=ID_VALUE&key=KEY_VALUE&fn=FILE_NAME_VALUE`

#### Access Key
The url's created with this script live for a maximum of 1 minute. This helps prevent unwanted hotlinking, etc.  
To set up your own access key just add something equivalent to the following to your plugin or script:

```
<?php
$now = date("YmdGi");
$id = 'My_Fancy_Plugin';
$key = md5($now . $id);
?>
```
U-SRV will also set a cookie for use with javascript, etc. as  
`$cname = "usrv_" . $id;`  
where `$cname` is the name of the cookie.

#### ID
Add a new case with an arbitrary ID and file store location to the ID section of `srv.php`. Send this same ID with the `GET` request.

* In this example the store location is retrieved from a database as a typical `YOURLS` option:
```
case 'ID_VALUE':
	$path = yourls_get_option('YOUR_CACHE_PATH');
	break;
```
* In this example the store filepath is set explicitly:
```
case 'ID_VALUE':
	$path = '/path/to/your/files/');
	break;
```

#### File info
The filename is set explicitly as a regular `GET` value.  
Mime types must be set expllicitly in the script in order to set header information correctly, and are restricted as an extra security measure. 
To add a new filetype, just add a new case to the Mime Types section of `srv.php`, check MIMETYPES.md for an exhaustive (?) list of examples.
* In order to allow the passing of a `tar.gz` file, add the following:
		- 'case "gz": $ctype="application/x-gzip"; break;`

### Use NOTES:
* Compatability with YOURLS 1.7.9 + (tested against [this](https://github.com/YOURLS/YOURLS/commit/cab0c150a0ce2b0f8775a029c6b47fa98d69598b) commit)
* v2.0.0 and on is a complete rewrite. It is still backwards compatible with plugins that have not yet been updated.
* U-SRV is pre-configured for the default filetypes used by the YOURLS [IQRCodes](https://github.com/joshp23/YOURLS-IQRCodes) and [Snapshot Visual Preview](https://github.com/joshp23/YOURLS-Snapshot) plugins. 
* As should be obviouse, this script can be easily modified for use outside of the YOURLS environment.
* Increasing apache's SSL Renegotiation Buffer Size may save you a headache. Simply add something like the following to the host file:
```
<location '/admin/plugins.php' >
	  	SSLRenegBufferSize 10486000
</location>
```
- set `define( 'USRV_DB_UPDATE', true );` in `config.php` in order to update DB from versions earlier than 1.5.0. After one page load delte this option.
## Plugins using this script
* [YOURLS-Snapshot](https://github.com/joshp23/YOURLS-Snapshot) - To serve images
* [YOURLS-IQRCodes](https://github.com/joshp23/YOURLS-IQRCodes) - To serve code images
* [YOURLS-Compliance](https://github.com/joshp23/YOURLS-Compliance) - To retrieve Snapshot images
* [YOURLS-rscrub](https://github.com/joshp23/YOURLS-rscrub) - To retrieve Snapshot images and deliver `rscrub.js`

### Support Dev
All of my published code is developed and maintained in spare time, if you would like to support development of this, or any of my published code, I have set up a Liberpay account for just this purpose. Thank you.

<noscript><a href="https://liberapay.com/joshu42/donate"><img alt="Donate using Liberapay" src="https://liberapay.com/assets/widgets/donate.svg"></a></noscript>

===========================

    Copyright (C) 2016 Josh Panter

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.

