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

   ```sh
   smbclient \\\\10.10.11.152\\Shares
   ```

   ```sh
    LAPS_Datasheet.docx               LAPS.x64.msi
    LAPS_OperationsGuide.docx         winrm_backup.zip
    LAPS_TechnicalSpecification.docx

   ```






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
* []()
* []()

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
