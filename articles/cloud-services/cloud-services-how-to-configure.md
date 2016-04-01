<properties 
    pageTitle="如何設定雲端服務 | Microsoft Azure" 
    description="了解如何在 Azure 中設定雲端服務。 了解更新雲端服務組態和設定角色執行個體的遠端存取。" 
    services="cloud-services" 
    documentationCenter="" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/22/2015"
    ms.author="adegeo"/>




# 如何設定雲端服務

> [AZURE.SELECTOR]
- [Azure 傳統入口網站](cloud-services-how-to-configure.md)
- [Azure 入口網站](cloud-services-how-to-configure-portal.md)

您可以在 Azure 傳統入口網站中設定雲端服務的最常用設定。 或者，如果您想要直接更新組態檔，可以下載要更新的服務組態檔、上傳更新過的檔案，然後將雲端服務更新為使用這些組態變更。 使用上述任一種方式，都會將組態更新推送到所有角色執行個體。

Azure 傳統入口網站也可讓您 [Azure 雲端服務中的角色啟用遠端桌面連線](cloud-services-role-enable-remote-desktop.md)

每個角色至少必須有兩個角色執行個體，Azure 才能確保服務在組態更新期間有 99.95% 的可用性。 如此才能讓一個虛擬機器在受到更新時，還有另一個虛擬機器可以處理用戶端要求。 如需詳細資訊，請參閱 [服務等級協定](http://azure.microsoft.com/support/legal/sla/)。

## 變更雲端服務

1. 在 [Azure 傳統入口網站](http://manage.windowsazure.com/), ，按一下 [ **雲端服務**, ，按一下 [雲端服務的名稱，然後按一下 **設定**。

    ![Configuration Page](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    在 **設定** ] 頁面上，您可以設定監視、 更新角色設定，並選擇的客體作業系統和系列角色執行個體。 

2. 在 **監視**, 、 將監視層級詳細資訊或最小]，並設定所需的詳細資訊監視的診斷連接字串。

3. 針對服務角色 (依角色分組)，您可以更新下列設定：
    
    >**設定**  
    >修改的其他組態設定中指定的值 *ConfigurationSettings* 服務組態 (.cscfg) 檔案的項目。
    >
    >**憑證**  
    >變更要在角色之 SSL 加密中使用的憑證指紋。 若要變更憑證，您必須先上傳新的憑證 (在 **憑證** 頁面)。 然後，更新角色設定中所顯示憑證字串中的指紋。

4. 在 **作業系統**, ，您可以變更的作業系統系列或版本，角色執行個體，或選擇 **自動** 若要啟用自動更新目前的作業系統版本。 作業系統設定會套用於 Web 角色和背景工作角色，但不影響虛擬機器。

    部署期間會在所有角色執行個體上安裝最新作業系統版本，而且預設會自動更新作業系統。 
    
    如果您因為程式碼中的相容性需求而需要在不同的作業系統版本上執行雲端服務，則可以選擇作業系統系列和版本。 當您選擇特定作業系統版本時，雲端服務的自動作業系統更新會暫停。 您需要確保作業系統收到更新。
    
    如果您解決所有應用程式有最新的作業系統版本的相容性問題，您可以啟用自動更新作業系統的作業系統版本設定為 [ **自動**。 
    
    ![OS Settings](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. 若要儲存組態設定，並將其推送到角色執行個體，按一下 [ **儲存**。 (按一下 **捨棄** 可取消變更。)**儲存** 和 **捨棄** 變更設定之後，加入命令列。

## 更新雲端服務組態檔

1. 下載含有最新組態的雲端服務組態檔 (.cscfg)。 在 **設定** 定域機組服務頁面上，按一下 **下載**。 然後按一下 [ **儲存**, ，或按一下 **另存新檔** 儲存檔案。

2. 在您更新服務組態檔之後，請上傳和套用組態更新：

    1. 在 **設定** 頁面上，按一下 **上載**。
    
        ![Upload Configuration](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. 在 **組態檔**, ，使用 **瀏覽** 選取更新過的.cscfg 檔案。
    
    3. 如果您的雲端服務包含任何只有一個執行個體的角色，選取 **套用設定，即使一或多個角色包含單一執行個體** 核取方塊以繼續進行角色的組態更新。
    
        除非每個角色都定義至少兩個執行個體，否則 Azure 無法保證雲端服務在服務組態更新期間至少有 99.95% 的可用性。 如需詳細資訊，請參閱 [服務等級協定](http://azure.microsoft.com/support/legal/sla/)。
    
    4. 按一下 [ **確定** （勾選記號）。 


## 後續步驟

* 了解如何 [部署雲端服務](cloud-services-how-to-create-deploy.md)。
* 設定 [自訂網域名稱](cloud-services-custom-domain-name.md)。
* [管理您的雲端服務](cloud-services-how-to-manage.md)。
* [啟用 Azure 雲端服務中角色的遠端桌面連線](cloud-services-role-enable-remote-desktop.md)
* 設定 [ssl 憑證](cloud-services-configure-ssl-certificate.md)。


