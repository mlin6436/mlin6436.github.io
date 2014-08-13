---
layout: post
title: "Setup FTP Server and Consume It Programmatically"
tags: ["ftp", "server", "client", "windows server 2008", "c#", ".net", "programmatically"]
---

#Setup a FTP server on Windows Server 2008 R2

Open `Server Manager`, and add new `Role` to it.

{% include image.html url="/media/2013-08-13-setup-ftp-server-and-consume-it-programmatically/ftp-server-setup-01.png" width="100%" description="Add new role into server manager" %}

In `Add Roles Wizard`, tick `Web Server (IIS)`.

{% include image.html url="/media/2013-08-13-setup-ftp-server-and-consume-it-programmatically/ftp-server-setup-02.png" width="100%" description="Enable IIS on the server" %}

In `Role Services`, tick everything under `FTP Server` section. Follow the wizard to the end to install all the features required.

{% include image.html url="/media/2013-08-13-setup-ftp-server-and-consume-it-programmatically/ftp-server-setup-03.png" width="100%" description="Enable FTP on the server" %}

Open IIS, tick `Add FTP Site` when right-click on `Sites` to add a new FTP site.

{% include image.html url="/media/2013-08-13-setup-ftp-server-and-consume-it-programmatically/ftp-server-setup-04.png" width="100%" description="Add FTP site" %}

Fill in the desirable site name and file location.

{% include image.html url="/media/2013-08-13-setup-ftp-server-and-consume-it-programmatically/ftp-server-setup-05.png" width="100%" description="Configure FTP site details" %}

Choose `All Unassigned` for `IP Address`, and do not SSL if you just want a simple FTP site.

{% include image.html url="/media/2013-08-13-setup-ftp-server-and-consume-it-programmatically/ftp-server-setup-06.png" width="100%" description="Configure FTP site IP" %}

Choose `Basic Authentication` and allow `All Users` to `Read` and `Write`. And carry on finishing the wizard.

{% include image.html url="/media/2013-08-13-setup-ftp-server-and-consume-it-programmatically/ftp-server-setup-07.png" width="100%" description="Configure FTP site authentication" %}

Open `Windows Firewall with Advanced Security` to add a new rule to the FTP site.

{% include image.html url="/media/2013-08-13-setup-ftp-server-and-consume-it-programmatically/ftp-server-setup-08.png" width="100%" description="Configure firewall" %}

Specify a port for FTP communication, and leave the rest as default.

{% include image.html url="/media/2013-08-13-setup-ftp-server-and-consume-it-programmatically/ftp-server-setup-09.png" width="100%" description="Specify firewall port" %}

To verify the FTP is working, navigate to `ftp://<yourftpsiteaddress>` in your browser.

#Consume FTP programmatically

{% highlight csharp %}
//using System;
//using System.IO;
//using System.Net;
//using System.Text;

void Main()
{
   ListFtpServerDirectoryContents();
   UploadToFtpServer();
   ListFtpServerDirectoryContents();
   DownloadFromFtpServer();
}

public void ListFtpServerDirectoryContents()
{
   var request = (FtpWebRequest)WebRequest.Create("ftp://ftpserveraddresss/");
   request.Method = WebRequestMethods.Ftp.ListDirectoryDetails;

   request.Credentials = new NetworkCredential("myusername", "mypassword");

   var response = (FtpWebResponse)request.GetResponse();

   var responseStream = response.GetResponseStream();
   var reader = new StreamReader(responseStream);
   Console.WriteLine(reader.ReadToEnd());

   Console.WriteLine("Directory List Complete, status {0}", response.StatusDescription);

   reader.Close();
   response.Close();
}

public void UploadToFtpServer()
{
   var request = (FtpWebRequest)WebRequest.Create("ftp://ftpserveraddresss/targetfilename");
   request.Method = WebRequestMethods.Ftp.UploadFile;

   request.Credentials = new NetworkCredential("myusername", "mypassword");

   var sourceStream = new StreamReader("filepathhere");
   var fileContents = Encoding.UTF8.GetBytes(sourceStream.ReadToEnd());
   sourceStream.Close();
   request.ContentLength = fileContents.Length;

   Stream requestStream = request.GetRequestStream();
   requestStream.Write(fileContents, 0, fileContents.Length);
   requestStream.Close();

   var response = (FtpWebResponse)request.GetResponse();

   Console.WriteLine("Upload File Complete, status {0}", response.StatusDescription);

   response.Close();
}

public void DownloadFromFtpServer()
{
   var request = (FtpWebRequest)WebRequest.Create("ftp://ftpserveraddresss/targetfilename");
   request.Method = WebRequestMethods.Ftp.DownloadFile;

   request.Credentials = new NetworkCredential("myusername", "mypassword");

   var response = (FtpWebResponse)request.GetResponse();

   var responseStream = response.GetResponseStream();
   var reader = new StreamReader(responseStream);
   Console.WriteLine(reader.ReadToEnd());

   Console.WriteLine("Download Complete, status {0}", response.StatusDescription);

   reader.Close();
   response.Close();
}
{% endhighlight %}
