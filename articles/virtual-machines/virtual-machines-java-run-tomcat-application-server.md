<properties
    pageTitle="虛擬機器上的 Tomcat | Microsoft Azure"
    description="本教學課程使用的傳統部署模型，以建立資源，並示範如何建立 Windows 虛擬機器，並將它設定為執行 Apache Tomcat 應用程式伺服器。"
    services="virtual-machines"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="jimbe"
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015"
    ms.author="robmcm"/>


# 如何在以傳統部署模型建立的虛擬機器上執行 Java 應用程式伺服器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


Azure 可讓您利用虛擬機器來提供伺服器功能。 例如，於 Azure 上執行的虛擬機器在經過設定後可代管 Apache Tomcat 等 Java 應用程式伺服器。 完成本指南的內容後，您將了解如何建立於 Azure 上執行的虛擬機器，以及該如何設定才能執行 Java 應用程式伺服器。

您將了解：

* 如何建立已安裝 Java Development Kit (JDK) 的虛擬機器。
* 如何從遠端登入虛擬機器。
* 如何將 Java 應用程式伺服器安裝在虛擬機器上。
* 如何為虛擬機器建立端點。
* 如何在防火牆中為應用程式伺服器開啟連接埠。

本教學課程的目的是在虛擬機器上安裝 Apache Tomcat 應用程式伺服器。 完成安裝作業會產生如下的 Tomcat 安裝。

![執行 Apache Tomcat 的虛擬機器][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## 建立虛擬機器

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 依序按一下 [新增]****、[運算]****、[虛擬機器]**** 及 [從組件庫]****。
3. 在 [虛擬機器映像選取]**** 對話方塊中，選取 [JDK 7 Windows Server 2012]****。
請注意，如果您的舊版應用程式尚未準備好在 JDK 7 中運作，可以使用 [JDK 6 Windows Server 2012****]。
4. 按 [下一步]****。
5. 在 [虛擬機器組態]**** 對話方塊中：
    1. 指定虛擬機器的名稱。
    2. 指定要用於虛擬機器的大小。
    3. 在 [使用者名稱]**** 欄位中輸入系統管理員的名稱。 請記住您即將輸入的名稱和密碼，因為當從遠端登入此虛擬機器時將會需要用到。
    4. 在 [新增密碼]**** 欄位中輸入密碼，然後在 [確認]**** 欄位中再輸入一次。 此為系統管理員帳戶的密碼。
    5. 按 [下一步]****。
6. 在下一個 [虛擬機器組態]**** 對話方塊中：
    1. 對於 [雲端服務]****，請使用預設值 [Create a new cloud service]****
    2. [Cloud service DNS name]**** 的值在整個 cloudapp.net 中必須是唯一的。 必要時請修改此值，使 Azure 指出該值是唯一的。
    2. 指定區域、同質群組或虛擬網路。 為達成本教學課程的目的，請指定**美國西部**之類的區域。
    2. 對於 [儲存體帳戶]****，請選取 [Use an automatically generated storage account]****。
    3. 對於 [可用性設定組]****，請選取 [(無)]****。
    4. 按 [下一步]****。
7. 在最終的 [虛擬機器組態]**** 對話方塊中：
    1. 接受預設的端點項目。
    2. 按一下 [完成]****。

## 從遠端登入虛擬機器

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下 [虛擬機器]****。
3. 按一下要登入的虛擬機器名稱。
4. 在啟動虛擬機器後，頁面底部的快顯功能表可允許連接。
5. 按一下 [連接]****。
6. 視需要回應提示以連接虛擬機器。 這應會需要儲存或開啟包含連線詳細資料的 .rdp 檔案。 您可能必須將 url:port 複製為 .rdp 檔案中第一行的最後一個部分，並將其貼入遠端登入應用程式中。

## 將 Java 應用程式伺服器安裝在虛擬機器上

您可以將 Java 應用程式伺服器複製到虛擬機器，或您也可以透過安裝程式安裝 Java 應用程式伺服器。

基於本教學課程的目的，我們將安裝 Tomcat。

1. 當您登入您的虛擬機器時，開啟的瀏覽器工作階段 [Apache Tomcat](http://tomcat.apache.org/download-70.cgi)。
2. 按兩下 [32-bit/64-bit Windows Service Installer]**** 的連結。 在使用本技術的前提下，Tomcat 將以 Windows 服務的形式安裝。
3. 出現提示時，選擇執行安裝程式。
4. 在 [Apache Tomcat Setup]**** 精靈中，按照提示安裝 Tomcat。 基於本教學課程的目的，接受預設值即可。 待 [完成 Apache Tomcat 設定精靈****] 對話方塊出現時，您可以選擇性勾選 [執行 Apache Tomcat****]，以立即啟動 Tomcat。 按一下 [完成]****，完成 Tomcat 安裝程序。

## 啟動 Tomcat

如果您未在 [完成 Apache Tomcat 設定精靈****] 對話方塊中選擇執行 Tomcat，請在虛擬機器上開啟命令提示字元，然後執行 **net start Tomcat7** 予以啟動。

您現在應該會看到執行中的 Tomcat 如果您執行虛擬機器的瀏覽器並開啟 <http://localhost:8080>。

若要從外部機器看見執行中的 Tomcat，您需要建立端點並開啟連接埠。

## 為虛擬機器建立端點

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下 [虛擬機器]****。
3. 按一下執行 Java 應用程式伺服器之虛擬機器的名稱。
4. 按一下 [端點]****。
5. 按一下 [新增]****。
6. 在 [新增端點****] 對話方塊中，確認已選取 [新增獨立端點****]，然後按 [下一步****]。
7. 在 [新端點詳細資訊****] 對話方塊中：
    1. 指定端點的名稱，如 **HttpIn**。
    2. 指定 [TCP]**** 通訊協定。
    3. 指定公用連接埠 [80]****。
    4. 指定私人連接埠 [8080]****。
    5. 按一下 [**完成**] 按鈕以關閉對話方塊。 系統隨即會建立端點。

## 在防火牆中為虛擬機器開啟連接埠

1. 登入虛擬機器。
2. 按一下 Windows [開始]****。
3. 按一下 [控制台]****。
4. 依序按一下 [系統及安全性]****、[Windows 防火牆]**** 及 [進階設定]****。
5. 按一下 [**輸入規則**]，然後按一下 [**新增規則**]。
 ![新增輸入規則][newibrule]
6. 針對 [**規則類型**]，請選取 [**連接埠**]，然後按一下 [**下一步**]。
 ![新增輸入規則連接埠][newruleport]
7. 在 [**通訊協定和連接埠**] 畫面上，選取 [**TCP**]，指定 **8080** 為 [**特定本機連接埠**]，然後按一下 [**下一步**]。
 ![新增輸入規則][newruleprotocol]
8. 在 [**動作**] 對話方塊中選取 [**允許連接**]，然後按 [**下一步**]。
 ![新增輸入規則動作][newruleaction]
9. 在 [**設定檔**] 畫面上，確定 [**網域**]、[**私人**] 和 [**公用**] 已選取，然後按一下 [**下一步**]。
 ![新增輸入規則設定檔][newruleprofile]
10. 在 [**名稱**] 畫面上，指定規則的名稱，例如 **HttpIn** (不過規則名稱不一定要與端點名稱相符)，然後按一下 [**完成**]。  
 ![新增輸入規則名稱][newrulename]

此時，Tomcat 網站應該會從外部瀏覽器檢視使用下列形式的 URL **http://*your\_DNS\_name*.cloudapp.net**, ，其中 *** your\_DNS\_name *** 是您在建立虛擬機器時指定的 DNS 名稱。

## 應用程式生命週期考量

* 您可以自行建立 Web 應用程式封存檔 (WAR)，然後再將其新增至 **webapps** 資料夾。 例如，建立基本 Java Service Page (JSP) 動態 Web 專案並以 WAR 檔案形式將其匯入，將 WAR 複製到虛擬機器上的 Apache Tomcat **webapps** 資料夾，然後在瀏覽器中予以執行。
* 依預設，當您安裝 Tomcat 服務時，系統會將其設定為手動啟動。 您可以使用 [服務] 嵌入式管理單元將其切換為自動啟動。 依序按一下 Windows [開始]****、[系統管理工具]**** 及 [服務]**** 以啟動 [服務] 嵌入式管理單元。 按兩下 **Apache Tomcat** 服務和設定 **啟動類型** 至 **自動**。

    ![將服務設定為自動啟動][service_automatic_startup]

    使 Tomcat 自動啟動的優點在於，當虛擬機器重新開機時 (例如安裝需要重新開機的軟體更新後) 其將會啟動。

## 後續步驟

了解其他服務 (例如 Azure 儲存體、 服務匯流排和 SQL 資料庫)，您可能想要納入 Java 應用程式藉由檢視提供的資訊 [Java 開發人員中心](http://azure.microsoft.com/develop/java/)。


[virtual_machine_tomcat]: ./media/virtual-machines-java-run-tomcat-application-server/WA_VirtualMachineRunningApacheTomcat.png 
[service_automatic_startup]: ./media/virtual-machines-java-run-tomcat-application-server/WA_TomcatServiceAutomaticStart.png 
[newibrule]: ./media/virtual-machines-java-run-tomcat-application-server/NewInboundRule.png 
[newruleport]: ./media/virtual-machines-java-run-tomcat-application-server/NewRulePort.png 
[newruleprotocol]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProtocol.png 
[newruleaction]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleAction.png 
[newrulename]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleName.png 
[newruleprofile]: ./media/virtual-machines-java-run-tomcat-application-server/NewRuleProfile.png 

