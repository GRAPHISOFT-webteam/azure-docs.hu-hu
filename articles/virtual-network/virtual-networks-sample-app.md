<properties
   pageTitle="與安全性界限環境搭配使用的範例應用程式 | Microsoft Azure"
   description="在建立 DMZ 來測試流量案例後部署這個簡單的 Web 應用程式"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="jonor"/>

# 與安全性界限環境搭配使用的範例應用程式

[返回 [安全性界限最佳作法] 頁面][HOME]

這些 PowerShell 指令碼可以在 IIS01 和 AppVM01 伺服器本機上執行，以安裝和設定一個極簡單的 Web 應用程式，顯示來自前端 IIS01 伺服器的 html 網頁和來自後端 AppVM01 伺服器的內容。

此應用程式提供簡單的測試環境，可測試許多 DMZ 範例，以及端點、NSG、UDR 及防火牆規則的變更如何影響流量。

## 允許 ICMP 的防火牆規則
這個簡單的 PowerShell 陳述式可以在任何 Windows VM 上執行，以允許 ICMP (Ping) 流量。 由於這可允許 ping 通訊協定通過 Windows 防火牆，因此讓測試和疑難排解變得更輕鬆 (ICMP 在多數的 Linux 散發版本上預設為開啟)。

    # Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
        -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

**注意 ︰** 如果您使用以下指令碼，此防火牆規則加入是第一個陳述式。

## IIS01 - Web 應用程式安裝指令碼
此指令碼將：

1.  在本機伺服器 Windows 防火牆開啟 IMCPv4 (Ping) 以方便測試
2.  安裝 IIS 和 .Net Framework v4.5
3.  建立 ASP.NET 網頁和 Web.config 檔案
4.  變更預設應用程式集區以方便存取檔案
5.  將匿名使用者設定為您的系統管理員帳戶和密碼

在透過遠端桌面 (RDP) 存取 IIS01 時，這個 PowerShell 指令碼應該會在本機執行。

    # IIS Server Post Build Config Script
    # Get Admin Account and Password
        Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
        $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
        $thePassword = Read-Host -Prompt "The Admin Password"
        
    # Turn On ICMPv4
        Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
        
    # Install IIS
        Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
        add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console
        
    # Create Web App Pages
        Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
        $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
        <%@ Import Namespace="System.IO" %>
        <script language="vb" runat="server">
            Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
                Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
                Dim objStreamReader As StreamReader
                objStreamReader = File.OpenText(FILENAME)
                Dim contents As String = objStreamReader.ReadToEnd()
                lblOutput.Text = contents
                objStreamReader.Close()
                lblTime.Text = Now()
            End Sub
        </script>
            
        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head runat="server">
            <title>DMZ Example App</title>
        </head>
        <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
          <form id="frmMain" runat="server">
            <div>
              <h1>Looks like you made it!</h1>
              This is a page from the inside (a web server on a private network),<br />
              and it is making its way to the outside! (If you are viewing this from the internet)<br />
              <br />
              The following sections show:
              <ul style="margin-top: 0px;">
                <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
                <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
                <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
              </ul>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Image File Linked from the Internet</b>:<br />
                <br />
                <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
            </div>
          </form>
        </body>
        </html>'
        
        $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
        <configuration>
          <system.web>
            <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
            <httpRuntime targetFramework="4.5" />
            <identity impersonate="true" />
            <customErrors mode="Off"/>
          </system.web>
          <system.webServer>
            <defaultDocument>
              <files>
                <add value="Home.aspx" />
              </files>
            </defaultDocument>
          </system.webServer>
        </configuration>'
            
        $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
        $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii
    
    # Set App Pool to Clasic Pipeline to remote file access will work easier
        Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
        c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
        c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost
        
    # Make sure the IIS settings take
        Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
        Restart-Service -Name W3SVC
        
        Write-Host
        Write-Host "Web App Creation Successfull!" -ForegroundColor Green
        Write-Host


## AppVM01 - 檔案伺服器安裝指令碼
此指令碼會設定這個簡單應用程式的後端。 此指令碼將：

1.  在防火牆開啟 IMCPv4 (Ping) 以方便測試
2.  建立新目錄
3.  建立上述網頁要遠端存取的文字檔
4.  將目錄和檔案的權限設為匿名以允許存取
5.  關閉 IE 增強式安全性以允許更輕鬆地從這部伺服器瀏覽 

>[AZURE.IMPORTANT] **最佳作法**︰ 永遠不會關閉 IE 增強式安全性的實際執行伺服器上，再加上它通常是個好主意，從生產伺服器瀏覽網頁。 此外，最好不要開啟檔案共用來供匿名存取，這裡這樣做是為了簡單起見。

在透過遠端桌面 (RDP) 存取 AppVM01 時，這個 PowerShell 指令碼應該會在本機執行。 PowerShell 必須以系統管理員身分執行，才能成功執行。
    
    # AppVM01 Server Post Build Config Script
    # PowerShell must be run as Administrator for Net Share commands to work
    
    # Turn On ICMPv4
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
    
    # Create Directory
        New-Item "C:\WebShare" -ItemType Directory
    
    # Write out Rand.txt
        $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
        $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii
    
    # Set Permissions on share
        $Acl = Get-Acl "C:\WebShare"
        $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
        $Acl.SetAccessRule($AccessRule)
        Set-Acl "C:\WebShare" $Acl
    
    # Create network share
        Net Share WebShare=C:\WebShare "/grant:Everyone,READ"
    
    # Turn Off IE Enhanced Security Configuration for Admins
        Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0
    
        Write-Host
        Write-Host "File Server Setup Successfull!" -ForegroundColor Green
        Write-Host
    

## DNS01 - DNS 伺服器安裝指令碼
此範例應用程式中未包含設定 DNS 伺服器的指令碼。 如果測試防火牆規則、NSG 或 UDR 時需要包含 DNS 流量，必須手動安裝 DNS01 伺服器。 這兩個範例的網路組態 XML 檔都包含 DNS01 做為主要 DNS 伺服器，而由層級 3 託管的公用 DNS 伺服器則做為備份 DNS 伺服器。 層級 3 DNS 伺服器會是非本機流量使用的實際 DNS 伺服器，若未安裝 DNS01，就不會有本機 DNS。

<!--Link References-->
[HOME]: ../best-practices-network-security.md

