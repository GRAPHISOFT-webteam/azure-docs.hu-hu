<properties 
    pageTitle="設定雲端服務的 SSL |Microsoft Azure" 
    description="了解如何為 Web 角色指定 HTTPS 端點，以及如何上傳 SSL 憑證來保護應用程式的安全。這些範例使用 Azure 入口網站。" 
    services="cloud-services" 
    documentationCenter=".net" 
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





# 在 Azure 設定應用程式的 SSL

> [AZURE.SELECTOR]
- [Azure classic portal](cloud-services-configure-ssl-certificate.md)
- [Azure portal](cloud-services-configure-ssl-certificate-portal.md)


安全通訊端層 (SSL) 加密是最常用來保護在網際網路上傳送之資料的方法。 此常見工作會討論如何為 Web 角色指定 HTTPS 端點，以及如何上傳 SSL 憑證來保護應用程式的安全。
> [AZURE.NOTE] 這項工作中的程序適用於 Azure 雲端服務。網站，請參閱 [設定 Azure 網站的 SSL 憑證](../web-sites-configure-ssl-certificate.md)。

此工作將使用生產部署；本主題最後將提供關於如何使用預備部署的資訊。

讀取 [這](cloud-services-how-to-create-deploy-portal.md) 第一，如果尚未建立雲端服務。

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

## 步驟 1：取得 SSL 憑證

若要設定應用程式的 SSL，首先您需要取得已由憑證授權單位 (CA) (專門核發憑證的受信任第三方) 簽署的 SSL 憑證。 如果您還沒有此類憑證，則必須向銷售 SSL 憑證的公司取得。

憑證必須符合 Azure 中對於 SSL 憑證的下列要求：

-   憑證必須包含私密金鑰。
-   憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換檔 (.pfx)。
-   憑證的主體名稱必須符合用來存取雲端服務的網域。 您無法向憑證授權單位 (CA) 取得 cloudapp.net 網域的 SSL 憑證。 您必須取得要在存取您的服務時使用的自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須符合用來存取應用程式的自訂網域名稱。 例如，如果您的自訂網域名稱為 **contoso.com**，則您需向 CA 要求 ***.contoso.com** 或 **www.contoso.com** 的憑證。
-   憑證至少必須以 2048 位元加密。

您可以基於測試目的， [建立](cloud-services-certs-create.md) ，並使用自我簽署的憑證。 自我簽署憑證不是由 CA 驗證，因此可以使用 cloudapp.net 網域做為網站 URL。 例如，以下工作即使用自我簽署的憑證的憑證所使用的一般名稱 (CN) 是 **sslexample.cloudapp.net**。

接下來，您必須在服務定義檔與服務組態檔中加入憑證的相關資訊。

<a name="modify"> </a>
## 步驟 2：修改服務定義檔和組態檔

您的應用程式必須已設定為使用憑證，而且您必須新增 HTTPS 端點。 因此，您需要更新服務定義檔與服務組態檔。

1.  在開發環境中，開啟服務定義檔
    (CSDEF)，加入 **憑證** 區段內 **WebRole**
    並包含憑證的下列相關資訊
    ：

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                             storeLocation="LocalMachine" 
                             storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    **Certificates** 區段定義憑證的名稱、位置，以及其所在的存放區名稱。 我們已選擇將憑證儲存在 CA (憑證授權單位) 存放區中，但是您也可以選擇其他選項。 請參閱 [如何使憑證與服務 []][] 如需詳細資訊。

2.  在服務定義檔中，加入 **InputEndpoint** 項目
    內 **端點** 區段以啟用 HTTPS:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  在服務定義檔中，加入 **繫結** 內的項目
    **網站** 一節。 如此會新增 HTTPS 繫結，
    以將端點對應至您的站台：

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    已完成服務定義檔的所有必要變更，
    但是您仍然需要將憑證資訊新增至
    服務組態檔中。

4.  在服務組態檔 (CSCFG) ServiceConfiguration.Cloud.cscfg，加入 **憑證**
    區段內 **角色** 區段中，取代範例指紋
    值：

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>


(上述範例針對指紋演算法的部分使用 **sha1**。 請指定適當的值作為憑證的指紋演算法。)

現在，服務定義檔和服務組態檔已經
更新，請封裝您的部署以上傳至 Azure。 If
您使用 **cspack**, ，確保您不使用
**/generateConfigurationFile** 旗標，因為如此會覆寫
您剛插入的憑證資訊。

## 步驟 3：上傳憑證

連線到入口網站和...

1. 使用下列方法選取您的雲端服務：
    - 在入口網站中，選取您的 [**雲端服務**]。 (位於**全部瀏覽/最近區域**。)

        ![發佈您的雲端服務](media/cloud-services-configure-ssl-certificate-portal/browse.png)

        **OR**

    - 在 [**全部瀏覽**] 下，選取 [**篩選依據**] 下的 [**雲端服務**]，並選取您想要的雲端服務執行個體。

3. 開啟雲端服務的**設定**。

    ![開啟設定](media/cloud-services-configure-ssl-certificate-portal/all-settings.png)

4. 按一下 [**憑證**]。

    ![按一下憑證圖示](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

4. 提供 [**檔案**]、[**密碼**]，然後按一下 [**上傳**]。

## 步驟 4：使用 HTTPS 來連線至角色執行個體

您的部署已在 Azure 啟動並執行，現在您可以
使用 HTTPS 來與其連線。

1.  按一下 [**網站 URL**] 開啟網頁瀏覽器。

    ![按一下網站 URL](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2.  在網頁瀏覽器中，將連結修改為使用 **https** 而非 **http**，然後造訪網頁。
    >[AZURE.NOTE] 如果使用自我簽署憑證，則當您在瀏覽器中瀏覽至與該自我簽署憑證相關聯的 HTTPS 端點時，您將會看見憑證錯誤。 使用信任的憑證授權單位所簽署的憑證，則不會有此問題；因此，您可以忽略該錯誤。 (另一個選項為新增自我簽署憑證至使用者的受信任憑證授權單位憑證存放區。)

    ![網站預覽](media/cloud-services-configure-ssl-certificate-portal/show-site.png)
    >[AZURE.TIP] 若要對預備部署而不是對生產部署使用 SSL，首先您需要判定要在預備部署中使用的 URL。 部署您的雲端服務之後，預備環境的 URL 由 **部署 ID** GUID，格式如下: `https://deployment-id.cloudapp.net/`  

    >建立與 GUID 型 URL 相同的一般名稱 (CN) (例如，**328187776e774ceda8fc57609d404462.cloudapp.net**) 的憑證、使用入口網站將該憑證新增至預備雲端服務、將憑證資訊新增至 CSDEF 與 CSCFG 檔案、重新封裝應用程式，然後將預備部署更新為使用新的套件與 CSCFG 檔。

## 後續步驟

* [雲端服務的一般組態](cloud-services-how-to-configure-portal.md)。
* 了解如何 [部署雲端服務](cloud-services-how-to-create-deploy-portal.md)。
* 設定 [自訂網域名稱](cloud-services-custom-domain-name-portal.md)。
* [管理您的雲端服務](cloud-services-how-to-manage-portal.md)。





