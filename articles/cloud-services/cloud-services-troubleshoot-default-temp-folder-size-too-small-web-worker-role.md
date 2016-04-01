<properties 
   pageTitle="預設 TEMP 資料夾對角色而言太小 | Microsoft Azure"
   description="雲端服務角色的 TEMP 資料夾空間量有限。 本文提供關於如何避免用盡空間的一些建議。"
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="msmets"
   editor=""
   tags="top-support-issue"/>
<tags 
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/14/2015"
   ms.author="daleche" />

# 雲端服務 Web/背景工作角色的預設 TEMP 資料夾太小

雲端服務背景工作角色或 Web 角色的預設暫存目錄大小上限為 100 MB，可能會在某個時間點達到。 本文說明如何避免用盡暫存目錄的空間。

>[AZURE.NOTE] 這只適用於 Azure SDK 1.0 到 SDK 1.4 使用 Web 和背景工作角色。

## 連絡 Azure 客戶支援

如果您需要更多協助，在本文中的任何時間點，您可以與 Azure 專家 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)。

或者，您也可以提出 Azure 支援事件。 移至 [Azure 支援網站](http://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。 使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。


## 為何會用盡空間？
您的應用程式中執行的程式碼可以使用標準 Windows 環境變數 TEMP 和 TMP。 TEMP 和 TMP 都會指向大小上限為 100 MB 的單一目錄。 任何儲存在此目錄中的資料都不會跨託管服務的生命週期而保存；如果託管服務中的角色執行個體被回收，就會清除目錄。

## 修正此問題的建議
實作下列其中一個替代方案：

- 設定本機儲存體資源，以及存取權限直接而不是使用 **TEMP** 或 **TMP**。 若要從您的應用程式內執行程式碼中存取本機儲存體資源，呼叫 [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) 方法。 如需設定本機儲存體資源的詳細資訊，請參閱 [設定本機儲存體資源](cloud-services-configure-local-storage-resources.md)。

- 設定本機儲存資源，並將 TEMP 和 TMP 目錄指向本機儲存資源的路徑。 應該執行這項修改內 [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) 方法。

下列程式碼範例說明如何在 OnStart 方法內修改 TEMP 和 TMP 的目標目錄：


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore" 
            //                  cleanOnRoleRecycle="false" 
            //                  sizeInMB="1024" />
            // </LocalResources>
            
            string customTempLocalResourcePath = 
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);
            
            // The rest of your startup code goes here…
            
            return base.OnStart();
        }
    }
}
```

## 後續步驟

檢視更多 [疑難排解文章](..\?tag=top-support-issue&service=cloud-services) 雲端服務。



