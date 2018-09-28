Pre requisites:
1. XAMPP v3.2.2 - 32 bit (no available 64bit for windows)
2. Visual Studio C++ 2017 (VC15) (If you don't have this you can download from https://aka.ms/vs/15/release/VC_redist.x64.exe)

INSTALL MOD_SECURITY
1. Extract mod_security-2.9.2-win32-VC15.zip, Copy libcurl.dll and yajl.dll to the /bin directory (xampp/apache/bin)
2. Create a folder on /modules directory and name it mod_security2. (xampp/apache/modules)
3. Copy mod_security2.so to the /modules/mod_security2 directory.
4. Copy modsecurity.conf-recommended to /conf directory, rename the file and remove -recommended. (xampp/apache/conf)
5. Edit in a text editor like notepad: modsecurity.conf
	a. Change SecRuleEngine DetectionOnly to SecRuleEngine On
	   and below it add SecDefaultAction "deny,phase:2,status:403"
	b. Below the line: SecResponseBodyLimit 524288
	   add the line: SecRule ARGS "\.\./" "t:normalisePathWin,id:99999,severity:4,msg:'Drive Access'"
	c. Change SecAuditLog /var/log/modsec_audit.log to SecAuditLog logs/modsec_audit.log
	   Save the file.
6. Copy unicode.mapping file on your /conf path.
7. Edit in a text editor like notepad: httpd.conf
	a. Enable the module unique_id by uncommenting (remove the '#' that preceeds it) this line :
	   LoadModule unique_id_module modules/mod_unique_id.so
	b. Add this line at the bottom of Load Modules section:
	   LoadModule security2_module modules/mod_security2/mod_security2.so
	c. Add this line at the bottom of Include conf/.. section :
	   Include conf/modsecurity.conf
	   Save the file.
8. Copy modsec_audit.log file on your /logs path.

INSTALL OWASP ModSecurity Core Rule Set (CRS)
1. Create a folder under /apache, name it owasp-modsecurity-crs. (xampp/apache)
2. Clone the CRS repository into the owasp-modsecurity-crs using:
	git clone https://github.com/SpiderLabs/owasp-modsecurity-crs .
3. Rename the file crs-setup.conf.example file to crs-setup.conf
   Please take the time to go through this file and customize the settings
   for your local environment. Failure to do so may result in false
   negatives and false positives.
4. Rename rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example and
   rules/RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf.example to remove the
   '.example' extension. This will allow you to add exclusions without updates
   overwriting them in the future.
5. Edit in a text editor like notepad: httpd.conf
	a. After Include conf/modsecurity.conf, add the following:
<IfModule security2_module>
    Include owasp-modsecurity-crs/crs-setup.conf
    Include owasp-modsecurity-crs/rules/*.conf
</IfModule>
6. Restart apache and ensure it starts without errors.
7. Make sure your websites are still running fine.



REFERENCES:
https://stackoverflow.com/questions/41056361/installing-mod-security2-so-on-apache-2-4-23-on-windows-7
http://mewbies.com/how_to_install_mod_security_for_apache_tutorial.htm
https://modsecurity.org/crs/
https://raw.githubusercontent.com/SpiderLabs/owasp-modsecurity-crs/v3.0/master/INSTALL