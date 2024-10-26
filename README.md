# Overview

burpsuite-project-file-parser is a Burp Suite extension to parse project files 
from the command line and output the results as JSON. 
It uses the Montoya Extender API so it should be cleanly compatible with most versions of Burp. 
Given a project file this can:

- Print all Audit Items 
- Print all requests/responses from the proxy history
- Print all requests/responses from the site map
- Given a regex search the response headers or response bodies from the proxy history and site map
- Print all Request URLs from proxy history and site map
- On all of the above, you can have the output show only items in scope

# Blog Posts

[Building an AppSec Pipeline with Burp Suite Data](https://www.silentrobots.com/building-an-appsec-pipeline-with-burpsuite-data/)

[8 Bug Hunting Exampes with burpsuite-project-parser](https://www.silentrobots.com/pushing-burp-suite-data-into-your-testing-pipeline-part-2/)

# Installation

1. Compile the code as described in [Build Information](https://github.com/sparky23172/burpsuite-project-file-parser/tree/Release?tab=readme-ov-file#build-information)
   - Alternatively, you can download the jar file from the [release](https://github.com/sparky23172/burpsuite-project-file-parser/releases)
2. Install the extension in Burp
3. **Make sure to set the Output and Errors to system console**

![Set console output](output_to_console.png?raw=true)

4. Close Burp Suite and follow examples below to parse the project file.

# Example Usage

Notes:
- Flags can be combined. For example, print audit items and site map; `auditItems siteMap`; 
  check options below for more information
- `[PATH_TO burpsuite_pro.jar]` is required; my path is: `~/BurpSuitePro/burpsuite_pro.jar` if you need an example. 
- `[PATH TO PROJECT FILE]` requires a project file and it's recommended to give the full path to the project file
- You may need `--add-opens=java.desktop/javax.swing=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED` 
depending on your version of Java
- Adding `scope` will do all of the expected features as listed. The only difference is it will run a check against isInScope. If the url is not in scope, it will not be printed.

## siteMap and proxyHistory

The siteMap and proxyHistory flags also support sub-components to speed up parsing. They are:

- request.headers
- request.body
- response.headers
- response.body

So, for example, to print out only the request body and headers from proxyHistory you would use:

```bash
java -jar -Djava.awt.headless=true [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] \
  proxyHistory.request.headers, proxyHistory.request.body
```

This massively speeds up parsing as the response bodies (which can be quite large) are ignored.

## Print Audit items

Use the `auditItems` flag, for example:

```bash
java -jar -Djava.awt.headless=true [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] \
  auditItems 
```
<br>
Use the `loud` flag to add the Base64 encoding of background and details of Audit Items.

```json
{"severity":"LOW","protocol":"https","port":443,"confidence":"CERTAIN","name":"Strict transport security not enforced","host":"example.com","url":"https://example.com/test/5"}
```
vs
```json
{"severity":"LOW","protocol":"https","port":443,"background":"QXVkaXRJc3N1ZURlZmluaXRpb257bmFtZT0nU3RyaWN0IHRyYW5zcG9ydCBzZWN1cml0eSBub3QgZW5mb3JjZWQnLCBiYWNrZ3JvdW5kPSc8cD4gVGhlIGFwcGxpY2F0aW9uIGZhaWxzIHRvIHByZXZlbnQgdXNlcnMgZnJvbSBjb25uZWN0aW5nICB0byBpdCBvdmVyIHVuZW5jcnlwdGVkIGNvbm5lY3Rpb25zLiAgQW4gYXR0YWNrZXIgYWJsZSB0byBtb2RpZnkgYSBsZWdpdGltYXRlIHVzZXIncyBuZXR3b3JrIHRyYWZmaWMgY291bGQgYnlwYXNzIHRoZSBhcHBsaWNhdGlvbidzIHVzZSBvZiBTU0wvVExTIGVuY3J5cHRpb24sIGFuZCB1c2UgdGhlIGFwcGxpY2F0aW9uIGFzIGEgcGxhdGZvcm0gZm9yIGF0dGFja3MgYWdhaW5zdCBpdHMgdXNlcnMuIFRoaXMgYXR0YWNrIGlzIHBlcmZvcm1lZCBieSByZXdyaXRpbmcgSFRUUFMgbGlua3MgYXMgSFRUUCwgc28gdGhhdCBpZiBhIHRhcmdldGVkIHVzZXIgZm9sbG93cyBhIGxpbmsgdG8gdGhlIHNpdGUgZnJvbSBhbiBIVFRQIHBhZ2UsIHRoZWlyIGJyb3dzZXIgbmV2ZXIgYXR0ZW1wdHMgdG8gdXNlIGFuIGVuY3J5cHRlZCBjb25uZWN0aW9uLiBUaGUgc3Nsc3RyaXAgdG9vbCAgYXV0b21hdGVzIHRoaXMgcHJvY2Vzcy4gPC9wPgo8cD4KVG8gZXhwbG9pdCB0aGlzIHZ1bG5lcmFiaWxpdHksIGFuIGF0dGFja2VyIG11c3QgYmUgc3VpdGFibHkgcG9zaXRpb25lZCB0byBpbnRlcmNlcHQgYW5kIG1vZGlmeSB0aGUgdmljdGltJ3MgbmV0d29yayB0cmFmZmljLlRoaXMgc2NlbmFyaW8gdHlwaWNhbGx5IG9jY3VycyB3aGVuIGEgY2xpZW50IGNvbW11bmljYXRlcyB3aXRoIHRoZSBzZXJ2ZXIgb3ZlciBhbiBpbnNlY3VyZSBjb25uZWN0aW9uIHN1Y2ggYXMgcHVibGljIFdpLUZpLCBvciBhIGNvcnBvcmF0ZSBvciBob21lIG5ldHdvcmsgdGhhdCBpcyBzaGFyZWQgd2l0aCBhIGNvbXByb21pc2VkIGNvbXB1dGVyLiBDb21tb24gZGVmZW5zZXMgc3VjaCBhcyBzd2l0Y2hlZCBuZXR3b3JrcyBhcmUgbm90IHN1ZmZpY2llbnQgdG8gcHJldmVudCB0aGlzLiBBbiBhdHRhY2tlciBzaXR1YXRlZCBpbiB0aGUgdXNlcidzIElTUCBvciB0aGUgYXBwbGljYXRpb24ncyBob3N0aW5nIGluZnJhc3RydWN0dXJlIGNvdWxkIGFsc28gcGVyZm9ybSB0aGlzIGF0dGFjay4gTm90ZSB0aGF0IGFuIGFkdmFuY2VkIGFkdmVyc2FyeSBjb3VsZCBwb3RlbnRpYWxseSB0YXJnZXQgYW55IGNvbm5lY3Rpb24gbWFkZSBvdmVyIHRoZSBJbnRlcm5ldCdzIGNvcmUgaW5mcmFzdHJ1Y3R1cmUuIDwvcD4nLCByZW1lZGlhdGlvbj0nPHA+VGhlIGFwcGxpY2F0aW9uIHNob3VsZCBpbnN0cnVjdCB3ZWIgYnJvd3NlcnMgdG8gb25seSBhY2Nlc3MgdGhlIGFwcGxpY2F0aW9uIHVzaW5nIEhUVFBTLiBUbyBkbyB0aGlzLCBlbmFibGUgSFRUUCBTdHJpY3QgVHJhbnNwb3J0IFNlY3VyaXR5IChIU1RTKSBieSBhZGRpbmcgYSByZXNwb25zZSBoZWFkZXIgd2l0aCB0aGUgbmFtZSAnU3RyaWN0LVRyYW5zcG9ydC1TZWN1cml0eScgYW5kIHRoZSB2YWx1ZSAnbWF4LWFnZT1leHBpcmVUaW1lJywgd2hlcmUgZXhwaXJlVGltZSBpcyB0aGUgdGltZSBpbiBzZWNvbmRzIHRoYXQgYnJvd3NlcnMgc2hvdWxkIHJlbWVtYmVyIHRoYXQgdGhlIHNpdGUgc2hvdWxkIG9ubHkgYmUgYWNjZXNzZWQgdXNpbmcgSFRUUFMuIENvbnNpZGVyIGFkZGluZyB0aGUgJ2luY2x1ZGVTdWJEb21haW5zJyBmbGFnIGlmIGFwcHJvcHJpYXRlLjwvcD4KPHA+Tm90ZSB0aGF0IGJlY2F1c2UgSFNUUyBpcyBhICZxdW90O3RydXN0IG9uIGZpcnN0IHVzZSZxdW90OyAoVE9GVSkgcHJvdG9jb2wsIGEgdXNlciB3aG8gaGFzIG5ldmVyIGFjY2Vzc2VkIHRoZSBhcHBsaWNhdGlvbiB3aWxsIG5ldmVyIGhhdmUgc2VlbiB0aGUgSFNUUyBoZWFkZXIsIGFuZCB3aWxsIHRoZXJlZm9yZSBzdGlsbCBiZSB2dWxuZXJhYmxlIHRvIFNTTCBzdHJpcHBpbmcgYXR0YWNrcy4gVG8gbWl0aWdhdGUgdGhpcyByaXNrLCB5b3UgY2FuIG9wdGlvbmFsbHkgYWRkIHRoZSAncHJlbG9hZCcgZmxhZyB0byB0aGUgSFNUUyBoZWFkZXIsIGFuZCBzdWJtaXQgdGhlIGRvbWFpbiBmb3IgcmV2aWV3IGJ5IGJyb3dzZXIgdmVuZG9ycy48L3A+Jywgc2V2ZXJpdHk9TE9XLCB0eXBlSW5kZXg9MTY3Nzc5ODR9","confidence":"CERTAIN","name":"Strict transport security not enforced","host":"example.com","details":"None","url":"https://example.com/test/5"}
```


## Print site map and proxy history

Combine the `siteMap` and `proxyHistory` flags to dump out all requests/responses from the site map and proxy history:

```bash
java -jar -Djava.awt.headless=true [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] \
    siteMap proxyHistory 
```

## Search Response Headers using Regex

Use the `responseHeader=regex` flag. For example to search for any nginx or Servlet in response header:

```bash
java -jar -Djava.awt.headless=true [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] \
    responseHeader='.*(Servlet|nginx).*'
...
{"url":"https://example.com/something.css","header":"x-powered-by: Servlet/3.0"}
{"url":"https://spocs.getpocket.com:443/spocs","header":"Server: nginx"}
...
```

## Search Response Body using Regex

Note, searching through a response body is memory expensive. It is recommended to store requests/responses and search that. 

Use the `responseBody=regex` flag. For example to search for `<form` elements in response bodies:
```bash
java -jar -Djava.awt.headless=true [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] \
    responseBody='.*<form.*'
```

If you want to clean up the results to something more manageable (rather than the entire response), YMMV with a second grep pattern for the 80 characters around the match:
```bash
java -jar -Djava.awt.headless=true [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] \
  responseBody='.*<form.*'| grep -o -P -- "url\":.{0,100}|.{0,80}<form.{0,80}"
```

## Dumping all of the URLs in the project file

Use the `urlDump` flag, for example:

```bash
java -jar -Djava.awt.headless=true [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] urlDump
```

As a personal recommendation, using the following commands will allow easier usage:
```bash
java -jar -Djava.awt.headless=true [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] \
  urlDump scope > urls.txt
cat urls.txt | grep url\" | cut -d '"' -f 4 | sort -u 
```

# Suggestions

- Use a custom User Options file (Burp > User options > Save user options) from Burp Suite with only this extension enabled. This can speed up Burp Suite loading speed because only one extension is loaded. Include the `--user-config-file` flag:
```bash
java -jar -Djava.awt.headless=true [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] --user-config-file=[PATH TO CONFIG FILE]
```

- Set the max amount of memory used by burp with `-Xmx` flag:
```bash
java -jar -Djava.awt.headless=true -Xmx2G [PATH_TO burpsuite_pro.jar] --project-file=[PATH TO PROJECT FILE] 
```

# Build Information

## Option 1:
Run `gradle fatJar` from the root directory. This expects you have gradle and all dependencies installed.

## Option 2:
Build the jar from the Dockerfile.

From the root directory of the project run:
```bash
mkdir build
docker build -t burpsuite-project-file-parser .
docker run --name burpsuite-project-file-parser -v [ADD THE FULLPATH TO YOUR CWD]/build:/tmp burpsuite-project-file-parser
```

The jar file should now be in the build directory of the project.

## Option 3:

1. Import into [idea](https://www.jetbrains.com/idea/)
2. Right side. Click on elephant
3. Project's name
4. Tasks
5. build
6. build

# Annoying issue:
Do you get the below error??
```
java -jar -Djava.awt.headless=true ~/BurpSuitePro/burpsuite_pro.jar --project-file=testApp.burp proxyHistory scope 

Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
Error: LinkageError occurred while loading main class burp.StartBurp
        java.lang.UnsupportedClassVersionError: burp/StartBurp has been compiled by a more recent version of the Java Runtime (class file version 65.0), this version of the Java Runtime only recognizes class file versions up to 61.0
```

Change the java file you are using. 
```
java --version
                                                                                                                
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
openjdk 17.0.13 2024-10-15
OpenJDK Runtime Environment (build 17.0.13+11-Debian-2)
OpenJDK 64-Bit Server VM (build 17.0.13+11-Debian-2, mixed mode, sharing)
```

vs

```
~/Downloads/idea-IC-242.23726.103/jbr/bin/java --version

Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
openjdk 21.0.4 2024-07-16
OpenJDK Runtime Environment JBR-21.0.4+13-509.26-jcef (build 21.0.4+13-b509.26)
OpenJDK 64-Bit Server VM JBR-21.0.4+13-509.26-jcef (build 21.0.4+13-b509.26, mixed mode)
```

Result:
```
~/Downloads/idea-IC-242.23726.103/jbr/bin/java -jar -Djava.awt.headless=true ~/BurpSuitePro/burpsuite_pro.jar --project-file=testApp.burp proxyHistory scope

Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
Your JRE appears to be version 21.0.4 from JetBrains s.r.o.
Burp has not been fully tested on this platform and you may experience problems.
proxyHistory scope
{"Message":"Only logging in-scope items"}
...
```
