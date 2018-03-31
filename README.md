# Project 7 - WordPress Pentesting

Time spent: **10** hours spent in total

> Objective: Find, analyze, recreate, and document **five vulnerabilities** affecting an old version of WordPress

## Pentesting Report

1. Unauthenticated Stored Cross-Site Scripting
  - [ ] Summary: 
    - A comment longer than the SQL "text" field that stores it can lead to truncation and malformed HTML, opening up opportunities for XSS.
    - Vulnerability types: XSS
    - Tested in version: 4.2
    - Fixed in version: 4.2.1
  - [ ] GIF Walkthrough: (1210KB, ~10s)
    - <img src="https://github.com/Lukanite/CP_wpvulns/raw/master/1.gif">
  - [ ] Steps to recreate: 
    - Create an "<a>" tag longer than 64k in a comment, which upon truncation renders as malformed HTML. Browsers try to fix this which leads to interpretation of the onmouseover exploit.
    - Sample exploit generator:
    ```python
    out = "<a title='x onmouseover=alert(unescape(/hello%20world/.source)) style=position:absolute;left:0;top:0;width:5000px;height:5000px  "
    for x in range(64*1024):
      out += "A"
    out += "</a>"
    print out
    ```
    - View the comment to get the XSS
  - [ ] Affected source code:
    - None, this is an SQL field size limitation that causes this exploit to work due to truncation
  - [ ] Reference:
    - https://wpvulndb.com/vulnerabilities/7945
    - http://klikki.fi/adv/wordpress2.html

2. Unauthenticated Genericons Cross-Site Scripting
  - [ ] Summary: 
    - A leftover example file in one of the default wordpress includes (Genericons) is vulnerable to XSS.
    - Vulnerability types: XSS
    - Tested in version: 4.2
    - Fixed in version: 4.2.2
  - [ ] GIF Walkthrough: (164KB, ~6s)
    - <img src="https://github.com/Lukanite/CP_wpvulns/raw/master/2.gif">
  - [ ] Steps to recreate: 
    - Create a malicious link using the "filter" feature of the genericons example page with XSS embedded in it for a post.
    - Example: 
    ```
    http://wpsite/wp-content/themes/twentyfifteen/genericons/example.html#1<img/ src=1 onerror= alert(1)>
    ```
    - Click the link to get the XSS
  - [ ] Affected source code:
    - https://core.trac.wordpress.org/browser/tags/4.2/src/wp-content/themes/twentyfifteen/genericons/example.html
    - Removed in WP 4.2.2
  - [ ] Reference:
    - https://wpvulndb.com/vulnerabilities/7979

3. Authenticated Shortcode Tags Cross-Site Scripting (CVE-2015-5714)
  - [ ] Summary: 
    - Shortcode tags can be mixed with HTML to lead to malformed HTML, bypassing KSES validation and opening up XSS. This can be combined with other vulnerabilities discovered by the same person to allow a read-only "Subscriber" user to escalate privilege and create persistent XSS.
    - Vulnerability types: XSS
    - Tested in version: 4.2
    - Fixed in version: 4.2.5
  - [ ] GIF Walkthrough: (223KB, ~8s)
    - <img src="https://github.com/Lukanite/CP_wpvulns/raw/master/3.gif">
  - [ ] Steps to recreate: 
    - Get funky with quotes and put HTML inside of shortcodes. Put this in a post.
    - Example:
    ```
    [caption width='1' caption='<a href="' ">]</a><a href="onClick='alert(1)'">
    ```
    - View the post to get the XSS
  - [ ] Affected source code:
    - https://github.com/WordPress/WordPress/commit/f72b21af23da6b6d54208e5c1d65ececdaa109c8
  - [ ] Reference:
    - https://blog.checkpoint.com/2015/09/15/finding-vulnerabilities-in-core-wordpress-a-bug-hunters-trilogy-part-iii-ultimatum/
4. Authenticated Stored Cross-Site Scripting via Image Filename
  - [ ] Summary: 
    - An attacker can use a social engineering attack with a malicious filename to provide persistent XSS on an attachment page (since the title of the atttachment page is set to the image filename by default).
    - Vulnerability types: XSS
    - Tested in version: 4.2
    - Fixed in version: 4.2.10
  - [ ] GIF Walkthrough: (714KB, ~30s)
    - <img src="https://github.com/Lukanite/CP_wpvulns/raw/master/4.gif">
  - [ ] Steps to recreate: 
    - Send an image with a malicious filename to the admin (victim)
      - Example: `zorua<img src=rekt onerror=alert(1)>.jpg`
    - Hope the admin is on a Mac or using Linux so that the filesystem supports the original filename
    - Get the admin to create a post including the file (as an attachment page)
    - Distribute the URL to the attachment page, which upon visit will execute your XSS
  - [ ] Affected source code:
    - https://github.com/WordPress/WordPress/commit/c9e60dab176635d4bfaaf431c0ea891e4726d6e0
  - [ ] Reference:
    - https://sumofpwn.nl/advisory/2016/persistent_cross_site_scripting_vulnerability_in_wordpress_due_to_unsafe_processing_of_file_names.html

## Assets

All assets are listed in the descriptions for each vulnerability involved

## Resources

- [WordPress Source Browser](https://core.trac.wordpress.org/browser/)
- [WordPress GitHub](https://github.com/WordPress/WordPress)

GIFs created with [FFmpeg](http://ffmpeg.org/) and [Windows 10 Game DVR](https://support.xbox.com/en-US/xbox-on-windows/social/record-game-clips-game-dvr-windows-10).

## Notes

- Virtualbox has ruined my networking setup on my workstation multiple times, so... I'm not a fan. It's a long story.
- Created a AWS EC2 instance instead running the latest Kali (as of March 15) and installed WordPress over that (LAMP + .tar.gz). This is why all of the URL's are for localhost.
- Used SSH [Local Port Forwarding](https://help.ubuntu.com/community/SSH/OpenSSH/PortForwarding) for most of the exploits, but since Exploit 4 (Image Filename XSS) doesn't work on Windows (my workstation OS), used [X over SSH](http://people.csail.mit.edu/wentzlaf/faq/ssh_X.html) to remotely run firefox from the EC2 instance.

Also, Licecap is blocked for download by Windows Defender, and this *is* a security-related class, so...

I'll take my chances with Windows 10 Game DVR + FFmpeg (plus, it's truly the better toolset anyways).

## License

    Copyright [2018] [Alan Zhang]

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
