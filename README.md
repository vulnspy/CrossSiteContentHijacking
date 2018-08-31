Cross-Site Content (Data) Hijacking (XSCH) PoC Project 
=======================================

<a href="https://www.vsplate.com/?github=vulnspy/CrossSiteContentHijacking"><img alt="VSPLATE GO" src="https://raw.githubusercontent.com/vsplate/images/master/vsgo_btn.png" width="200px"></a>

**Click the `VSPLATE GO` button to launch a demo online / 点击`VSPLATE GO`按钮创建在线环境**

License
-------
Released under AGPL (see LICENSE for more information).

Description
-----------
This project can be used to provide a proof of concept for:
- Exploiting websites with insecure policy files (crossdomain.xml or clientaccesspolicy.xml) by reading their contents.
- Exploiting insecure file upload functionalities which do not check the file contents properly or allow to upload SWF or PDF files without having Content-Disposition header during the download process. In this scenario, the created SWF, XAP, or PDF file should be uploaded with any extension such as .JPG to the target website. Then, the "Object File" value should be set to the URL of the uploaded file to read the target website's contents.
- Exploiting CVE-2011-2461 (see the references for more details)
- Exploiting websites with insecure HTML5 cross-origin resource sharing (CORS) headers

Note: .XAP files can be renamed to any other extension but they cannot be load cross-domain anymore. It seems Silverlight finds the file extension based on the provided URL and ignores it if it is not .XAP. This can still be exploited if a website allows users to use ";" or "/" after the actual file name to add a ".XAP" extension.


Usage
-----
- Exploiting an insecure policy file:
  1) Host the ContentHijacking directory with a web server.
  2) Browse to the index.html page (will be redirected to ContentHijackingLoader.html).
  3) Change the "Object File" field in the HTML page to a suitable object from the "objects" directory ("xfa-manual-ContentHijacking.pdf" cannot be used).
- Exploiting an insecure file upload/download: 
  1) Upload an object file from the "objects" directory to the victim server. These files can also be renamed with another extension when uploaded to another domain (for this purpose, first use Flash and then PDF as Silverlight XAP files will not normally work with another extension from another domain).
  2) The "Object File" field should be set to the location of the uploaded file.
- Exploiting CVE-2011-2461
  1) The "Object File" field should be set to the vulnerable file.
  2) Select the "Flash CVE-2011-2461 Only" option from the drop-down list of the "Type" field.
- Exploiting an insecure CORS policy:
  1) The "Object File" field can be set to the local "ContentHijacking.html" file. If you can upload a HTML file in your target domain, you can exploit XSS issues much more easier than using CORS.
  
Note: .XAP files can be renamed to any other extension but they cannot be load cross-domain anymore. It seems Silverlight finds the file extension based on the provided URL and ignores it if it is not .XAP. This can still be exploited if a website allows users to use ";" or "/" after the actual file name to add a ".XAP" extension.

Note: When Silverlight requests a .XAP file cross-domain, the content type must be: application/x-silverlight-app.

Note: PDF files can only be used in Adobe Reader viewer (they will not work with Chrome and Firefox built-in PDF viewers)

Note: Reading static contents or data that is publicly accessible cannot be considered as an issue. It is important to remove false-positive results from your advisories. Note that the use of an astresik ("*") character alone in the "Access-Control-Allow-Origin" header is not an issue.

Usage Example:
- in IE with Adobe Reader:
https://15.rs/ContentHijacking/ContentHijackingLoader.html?objfile=https://15.rs/ContentHijacking/objects/ContentHijacking.pdf&objtype=pdf&target=https://0me.me/&postdata=param1=foobar&logmode=all&regex=owasp.*&isauto=1
- in any browsers that supports SWF:
http://15.rs/ContentHijacking/ContentHijackingLoader.html?objfile=http://0me.me/ContentHijacking/objects/ContentHijacking.swf&objtype=flash&target=http://0me.me/&postdata=&logmode=result&regex=&isauto=1

Generic Recommendation to Solve the Security Issue
--------------------------------------------------
The file types allowed to be uploaded should be restricted to only those that are necessary for business functionality.

The application should perform filtering and content checking on any files which are uploaded to the server. Files should be thoroughly scanned and validated before being made available to other users. If in doubt, the file should be discarded.

Adding the "Content-Disposition: Attachment" and "X-Content-Type-Options: nosniff" headers to the response of static files will secure the website against Flash or PDF-based cross-site content-hijacking attacks. It is recommended that this practice be performed for all of the files that users need to download in all the modules that deal with a file download. Although this method does not fully secure the website against attacks using Silverlight or similar objects, it can mitigate the risk of using Adobe Flash and PDF objects, especially when uploading PDF files is permitted.

Flash/PDF (crossdomain.xml) or Silverlight (clientaccesspolicy.xml) cross-domain policy files should be removed if they are not in use and there is no business requirement for Flash or Silverlight applications to communicate with the website.

Cross-domain access should be restricted to a minimal set of domains that are trusted and will require access. An access policy is considered weak or insecure when a wildcard character is used especially in the value of the "uri" attribute.

Any "crossdomain.xml" file which is used for Silverlight applications should be considered weak as it can only accept a wildcard ("*") character in the domain attribute.

Browser caching should be disabled for the corssdomain.xml and clientaccesspolicy.xml files. This enables the website to easily update the file or restrict access to the Web services if necessary. Once the client access policy file is checked, it remains in effect for the browser session so the impact of non-caching to the end-user is minimal. This can be raised as a low or informational risk issue based on the content of the target website and security and complexity of the policy file(s).

CORS headers should be reviewed to only be enabled for static or publicly accessible data. Otherwise, the "Access-Control-Allow-Origin" header should only contain authorised addresses. Other CORS headers such as "Access-Control-Allow-Credentials" should only be used when they are required. Items within the CORS headers such as "Access-Control-Allow-Methods" or "Access-Control-Allow-Headers" should be reviewed and removed if they are not required.

Note: Using "Referer" header cannot be a solution as it is possible to set this header for example by sending a POST request using Adobe Reader and PDF (see the "xfa-manual-ContentHijacking.pdf" file in the "objects" directory). Update: Setting the "referer" header has been addressed by Adobe unless you also find a bypass for it ;)

Project Page
------------
See the project page for the latest update/help: https://github.com/nccgroup/CrossSiteContentHijacking

Author
------
Soroush Dalili (@irsdl) from NCC Group

References
----------
Even uploading a JPG file can lead to Cross Domain Data Hijacking (client-side attack)!
https://soroush.secproject.com/blog/2014/05/even-uploading-a-jpg-file-can-lead-to-cross-domain-data-hijacking-client-side-attack/

Multiple PDF Vulnerabilities - Text and Pictures on Steroids
http://insert-script.blogspot.co.at/2014/12/multiple-pdf-vulnerabilites-text-and.html

HTTP Communication and Security with Silverlight
http://msdn.microsoft.com/en-gb/library/cc838250(v=vs.95).aspx

Explanation Of Cross Domain And Client Access Policy Files For Silverlight
http://www.devtoolshed.com/explanation-cross-domain-and-client-access-policy-files-silverlight

Cross-domain policy file specification
http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html

Setting a crossdomain.xml file for HTTP streaming
http://www.adobe.com/devnet/adobe-media-server/articles/cross-domain-xml-for-streaming.html

Exploiting CVE-2011-2461 on google.com
http://blog.mindedsecurity.com/2015/03/exploiting-cve-2011-2461-on-googlecom.html
