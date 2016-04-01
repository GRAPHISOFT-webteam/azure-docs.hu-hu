<properties
   pageTitle="在 Azure 流量管理員中管理端點 | Microsoft Azure"
   description="本文將協助您從 Azure 流量管理員加入、移除、啟用和停用端點。"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# 加入、停用、啟用或刪除端點

不論網站模式為何，Azure App Service 中的 Web Apps 功能已為資料中心內的網站提供容錯移轉和循環配置資源流量路由功能。 Azure 流量管理員可讓您在不同的資料中心的網站和雲端服務中指定容錯移轉和循環配置資源流量路由。 提供該項功能的首要步驟是將雲端服務或網站端點新增至流量管理員。

>[AZURE.NOTE] 您無法將外部位置或流量管理員設定檔新增為使用 Azure 傳統入口網站的端點。 您必須使用 REST API [建立定義](http://go.microsoft.com/fwlink/p/?LinkId=400772) 或 Windows PowerShell [Add-azuretrafficmanagerendpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774)。

您也可以停用屬於流量管理員設定檔一部分的個別端點。 端點包括雲端服務和網站。 停用端點會將端點做為設定檔的一部分保留，但是設定檔會以好像未包含端點的方式運作。 此動作對於暫時移除處於維護模式、或被重新部署的端點而言非常有用。 在重新啟動並執行端點之後，您可以將它啟用。

>[AZURE.NOTE] 停用端點會與其在 Azure 中的部署狀態無關。 健全的端點將會持續運作，並且即使在流量管理員中停用該服務時仍然可以接收流量。 此外，在一個設定檔中停用端點並不會影響它在另一個設定檔中的狀態。

## 若要新增雲端服務或網站端點


1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔，然後按一下設定檔名稱右側的箭號。 這會開啟設定檔的設定頁面。
2. 在頁面頂端，按一下 [ **端點** 以檢視已設定的端點。
3. 在頁面底部，按一下 [ **新增** 存取 **新增服務端點** 頁面。 根據預設，這個頁面會列出下的雲端服務 **服務端點**。
4. 若是雲端服務，請在清單中選取雲端服務以將他們做為此設定檔的端點啟用。 清除雲端服務名稱會將它從端點清單中移除。
5. 若是網站，請按一下 [ **服務類型** 下拉式清單，然後選取 **Web 應用程式**。
6. 請在清單中選取網站以將他們做為此設定檔的端點啟用。 清除網站名稱會將它從端點清單中移除。 請注意，您只可以在每個 Azure 資料中心 (也稱為「區域」) 內選取單一網站。 如果您要在託管多個網站的資料中心內選取一個網站，當您選取第一個網站時，相同資料中心內的其他網站會變成無法選取。 並請注意，只有標準網站才會被列出。
7. 選取此設定檔的端點之後，請按一下右下角的核取記號以儲存變更。

>[AZURE.NOTE] 如果您使用 *容錯移轉* 流量路由方法之後您將新增, 或移除端點，請務必調整 [組態] 頁面上容錯移轉優先權清單，以反映您要用於組態的容錯移轉順序。 如需詳細資訊，請參閱 [設定容錯移轉流量路由](traffic-manager-configure-failover-load-balancing.md)。

## 若要停用端點

1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔，然後按一下設定檔名稱右側的箭號。 這會開啟設定檔的設定頁面。
2. 在頁面頂端，按一下 [ **端點** 以檢視您的組態中包含的端點。
3. 按一下您想要停用，然後按一下 [端點 **停用** 頁面的底部。
4. 根據流量管理員網域名稱的 DNS 存留時間 (TTL) 設定，流量將會停止流向端點。 您可以從流量管理員設定檔的 [組態] 頁面變更 TTL。

## 若要啟用端點

1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔，然後按一下設定檔名稱右側的箭號。 這會開啟設定檔的設定頁面。
2. 在頁面頂端，按一下 [ **端點** 以檢視您的組態中包含的端點。
3. 按一下您想要啟用，然後按一下 [端點 **啟用** 頁面的底部。
4. 流量會如設定檔指定再次開始流向服務。

## 若要新增雲端服務或網站端點


1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔，然後按一下設定檔名稱右側的箭號。 這會開啟設定檔的設定頁面。
2. 在頁面頂端，按一下 [ **端點** 以檢視已設定的端點。
3. 在 [端點] 頁面上，按一下您要從設定檔刪除的端點名稱。
4. 在頁面底部，按一下 [ **刪除**。

>[AZURE.NOTE] 您無法刪除外部位置或流量管理員設定檔，為使用 Azure 傳統入口網站的端點。 您必須使用 Windows PowerShell。 如需詳細資訊，請參閱 [Remove-azuretrafficmanagerendpoint](https://msdn.microsoft.com/library/dn690251.aspx)。

## 後續步驟


[設定容錯移轉路由方法](traffic-manager-configure-failover-routing-method.md)

[設定循環配置資源路由方法](traffic-manager-configure-round-robin-routing-method.md)

[設定效能路由方法](traffic-manager-configure-performance-routing-method.md)

[疑難排解流量管理員的已降級狀態](traffic-manager-troubleshooting-degraded.md)

[流量管理員的相關作業 (REST API 參考)](http://go.microsoft.com/fwlink/p/?LinkID=313584)


