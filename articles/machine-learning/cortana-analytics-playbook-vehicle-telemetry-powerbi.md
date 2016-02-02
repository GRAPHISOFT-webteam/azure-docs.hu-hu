<properties 
    pageTitle="車輛遙測分析方案範本 PowerBI 儀表板安裝指示 | Microsoft Azure" 
    description="使用 Cortana Analytics 具備的強大功能，取得關於車輛健全狀態與駕駛習慣的即時預測情資。" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="bradsev" />



# 車輛遙測分析方案範本 PowerBI 儀表板安裝指示

此**功能表**連結至此腳本的章節。

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


「車輛遙測分析」方案示範汽車經銷商、汽車製造商和保險公司如何運用 Cortana Analytics 功能，取得車輛健全狀態與駕駛習慣的即時預測情資，以改善客戶體驗、R&D 和行銷活動等方面。 
本文件包含在您的訂用帳戶中部署方案之後，如何設定 PowerBI 報告和儀表板的逐步指示。


## 必要條件

1.  瀏覽至部署車輛遙測資料分析解決方案 [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)
2.  [安裝 Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3.  [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)。 如果您沒有 Azure 訂用帳戶，請立即取得免費的 Azure 訂用帳戶
4.  Microsoft PowerBI 帳戶


## Cortana Analytics Suite 元件

「車輛遙測分析」方案範本會在您的訂用帳戶中部署下列 Cortana Analytics 服務。

- **事件中樞**可將數百萬計的車輛遙測事件擷取至 Azure。
- **串流分析**可取得關於車輛健全狀態的即時情資，同時將資料保存到長期儲存體，供進行更豐富的批次分析。
- **機器學習服務**可即時進行異常偵測與批次處理以取得預測情資。
- **HDInsight** 用於大規模的資料轉換
- **Data Factory** 可執行批次處理管線的協調、排程、資源管理和監控工作。

**Power BI** 給此方案一個豐富的儀表板來提供即時資料和預測性分析視覺效果。

此方案使用兩種不同的資料來源：**模擬車輛訊號和診斷資料集**和**車輛類別目錄**。

此方案包含車輛遠程資訊服務模擬器。 它會在指定時間點發出對應於車輛狀態與駕駛模式的診斷資訊和訊號。

「車輛類別目錄」是包含 VIN 至車型對應的參考資料集


## PowerBI 儀表板的準備工作

### 部署

完成部署後，您應該會看到下圖，其中以「綠色」標示所有這些元件。

- 按一下綠色節點右上方的箭號，瀏覽至對應的服務，以驗證是否已成功部署所有這些元件。
- 按一下 [車輛遠程資訊服務模擬器] 節點右上方的箭號，下載資料模擬器封裝。 將檔案儲存在本機電腦上並解壓縮。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

現在，您可以使用豐富的視覺效果來設定 PowerBI 儀表板，以取得車輛健全狀況和駕駛習慣的即時預測性情資。 建立所有報告和設定儀表板大約需要 45 分鐘至一小時。


### 設定 Power BI 即時儀表板

**產生模擬資料**

1. 在您本機電腦，請移至車輛車用通訊模擬器封裝解壓縮的資料夾 ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)
2.  執行應用程式 ***CarEventGenerator.exe***。
3.  它會在指定時間點發出對應於車輛狀態與駕駛模式的診斷資訊和訊號。 這會發佈至您在部署時所設定的 Azure 事件中樞執行個體。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)

**啟動即時儀表板應用程式**

解決方案包含一個可在 PowerBI 中產生即時儀表板的應用程式。 此應用程式會接聽由串流分析以連續模式發佈事件的事件中樞執行個體。 針對此應用程式收到的每一個事件，它會使用機器學習服務要求-回應評分端點來處理資料，而產生的資料集會發佈至 PowerBI 推送 API 來呈現視覺效果。

若要下載應用程式：

1.  按一下 [圖表檢視上的 PowerBI 節點上，按一下 **下載即時儀表板應用程式**' [屬性] 窗格上的連結。![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard-new1.png)
2.  擷取並儲存在本機的應用程式 ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.  執行應用程式 **RealtimeDashboardApp.exe**
4.  提供有效的 Power BI 認證、登入，然後按一下 [接受]****

    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)

    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)


### 設定 PowerBI 報告

即時報告和儀表板需要大約 30-45 分鐘的時間才能完成。 
瀏覽至 [http://powerbi.com](http://powerbi.com) 與登入。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Power BI 中將會產生新的資料集。 按一下 **ConnectedCarsRealtime** 資料集。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

使用 **Ctrl + s** 儲存空白報告。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

提供報告名稱「車輛遙測分析即時 - 報告」**。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## 即時報告

此解決方案有 3 份即時報告：

1.  行駛中的車輛
2.  需要維修的車輛
3.  車輛健全狀況統計資料

您可以選擇設定上述 3 份即時報告，或在任何階段後停止並繼續進行設定批次報告的下一節。 我們建議您建立上述 3 份報表，以將解決方案即時路徑的完整深入分析視覺化。

### 1.行駛中的車輛

按兩下 **第 1 頁** 將它重新命名為"車輛作業中"  
    ![Connected Cars - 行駛中的車輛](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

從 [欄位]**** 中選取 **vin** 欄位，然後選擇「卡片」****做為視覺效果類型。

將建立卡片視覺效果，如圖所示。  
    ![Connected Cars - 選取 vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

按一下空白區域以加入新的視覺效果。

從欄位中選取 **City** 和 **vin**。 將視覺效果變更為「地圖」****。 將 **vin** 拖曳到值區域。 拖放到 **縣 (市)** 欄位從 **圖例** 區域。   
    ![Connected Cars - 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

選取 **格式** > 一節 **視覺效果**, ，按一下 **標題** 和變更的文字 **」 運作的縣 (市) 的車輛"**。  
    ![Connected Cars - 行駛中的車輛 (依城市)](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

最後一個視覺效果看起來如圖所示。    
    ![Connected Cars - 最終視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

按一下空白區域以加入新的視覺效果。

選取 **City** 和 **vin**，將視覺效果類型變更為「群組直條圖」****。 請確定 **City** 欄位在 [軸] 區域****，**vin** 在 [值] 區域****

排序圖表的 **「 計數的 vin 」**  
    ![Connected Cars - vin 的計數](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

將圖表 [標題]**** 變更為「行駛中的車輛 (依城市)」****

按一下 [ **格式** 區段，然後選取 **資料色彩**,  ，按一下 [ **「 On 」** 至 **全部顯示**  
    ![Connected Cars - 顯示所有資料色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

按一下色彩圖示變更個別的城市的色彩。  
    ![Connected Cars - 變更色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

按一下空白區域以加入新的視覺效果。

選取 **群組直條圖** 拖曳視覺效果的視覺效果，從 **縣 (市)** 欄位 **軸** 區域中， **模型** 中 **圖例** 區域和 **vin** 中 **值** 區域。  
    ![Connected Cars - 群組直條圖](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Connected Cars -轉譯](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

重新整理此頁面上的所有視覺效果，如圖所示。  
    ![Connected Cars -視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

您已經成功設定「行駛中的車輛」即時報告。 您可以繼續建立下一份即時報告，或在此停止並設定儀表板。

### 2.需要維修的車輛

按一下 [ ![新增](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) 若要加入新的報表，重新命名為 **「 車輛需要維護 」**

![Connected Cars - 需要維修的車輛](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

選取 **vin** 欄位和視覺效果類型變更為 **卡**。  
    ![Connected Cars - Vin 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

我們在資料集中有一個名為 "MaintenanceLabel" 的欄位。 此欄位的值可以是 “0” 或 “1”。 它是由佈建為解決方案一部分並與即時路徑整合的 Azure Machine Learning 模型設定。 "1" 表示車輛需要維修。

我們加入 [頁面層級]**** 篩選以顯示需要維修的車輛資料。

1. 拖放到 **"MaintenanceLabel"** 欄位到 **頁面層級篩選**。  
![Connected Cars - 頁面層級篩選](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

2. 按一下 [ **基本篩選** 功能表出現在底部 MaintenanceLabel 頁面層級篩選。  
![Connected Cars - 基本篩選](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  若要設定其篩選值 **"1"**    
![Connected Cars - 篩選值](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)


按一下空白區域以加入新的視覺效果。

選取 **群組直條圖** 從視覺效果  
![Connected Cars - Vind 卡片視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Connected Cars - 群組直條圖](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

將 **Model** 欄位拖曳到 [軸]**** 區域，將 **Vin** 拖曳到 [值]**** 區域。 然後，依 [vin 的計數]**** 排序視覺效果。 將圖表 [標題]**** 變更為「需要維修的車輛 (依車型)」****

拖放到 **vin** 欄位到 **色彩飽和度** 出現在 **欄位** ![欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) 區段 **視覺化** ] 索引標籤  
![Connected Cars - 色彩飽和度](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

變更 **資料色彩** 從視覺效果中 **格式** 區段  
變更最小值色彩: **F2C812**  
變更最大值色彩: **FF6300**  
![Connected Cars - 色彩變更](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Connected Cars - 新的視覺效果色彩](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「群組直條圖」****，將 **vin** 欄位拖曳到 [值]**** 區域，將 **City** 欄位拖曳到 [軸]**** 區域。 依 [vin 的計數]**** 排序圖表。 變更圖表 **標題** 至 **」 需要維護依城市的車輛"**   
![Connected Cars - 需要維修的車輛 (依城市)](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

按一下空白區域以加入新的視覺效果。

選取 **多重資料列卡** 拖曳視覺效果的視覺效果，從 **模型** 和 **vin** 到 **欄位** 區域。  
![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

重新整理所有視覺效果，最後的報表，如下所示的外觀  
![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

您已經成功設定「需要維修的車輛」即時報告。 您可以繼續建立下一份即時報告，或在此停止並設定儀表板。

### 3.車輛健全狀況統計資料

按一下 [ ![新增](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) 若要加入新的報表，重新命名為 **"車輛健全狀況統計資料 」**

選取 **量測計** 視覺效果，從視覺效果，然後拖曳 **速度** 到欄位
**值、 最小值最大值** 區域。  
![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

將 [值]**** 區域中的 **speed** 預設彙總變更為 [平均值]****

將 [最小值]**** 區域中的 **speed** 預設彙總變更為 [最小值]****

將 [最大值]**** 區域中的 **speed** 預設彙總變更為 [最大值]****

![Connected Cars - 多列卡片](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

將 [量測計標題]**** 重新命名為「平均速度」****

![Connected Cars - 量測計](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

按一下空白區域以加入新的視覺效果。

同樣地加入「平均機油」****、「平均油量」****和「平均引擎溫度」****的**量測計**。

依照上述「平均速度」****量測計中的步驟，變更每個量測計中各欄位的預設彙總。

![Connected Cars - 量測計](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「條線和群組直條圖」****，然後將 [City]**** 欄位拖曳到 [共用軸]****，將 **speed**、**tirepressure 和 engineoil 欄位**拖曳到 [資料行值]**** 區域，將其彙總類型變更為 [平均值]****。

拖放到 **engineTemperature** 欄位到 **列值** 區域中，將彙總類型變更為 **平均**。

![Connected Cars -視覺效果欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

將圖表 [標題]**** 變更為「平均速度、胎壓、機油和引擎溫度」****

![Connected Cars -視覺效果欄位](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「樹狀圖」****視覺效果，將 **Model** 欄位拖曳到 [群組]**** 區域，將 **MaintenanceProbability** 欄位拖曳到 [值]**** 區域。

將圖表 [標題]**** 變更為「需要維修的車型」****

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「100% 堆疊橫條圖」****視覺效果，將 **city** 欄位拖曳到 [軸]**** 區域，將 **MaintenanceProbability**、**RecallProbability** 欄位拖曳到 [值]**** 區域。

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

按一下 [格式]****，選取 [資料色彩]****，將 **MaintenanceProbability** 色彩變更為值 **"F2C80F"**

將圖表的 [標題]**** 變更為「車輛維條和召回的機率 (依城市)」****

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

按一下空白區域以加入新的視覺效果。

從 [視覺效果] 中選取「區域圖」****視覺效果，將 **Model** 欄位拖曳到 [軸]**** 區域，將 **engineOil、tirepressure、speed 和 MaintenanceProbability** 欄位拖曳到 [值]**** 區域。 將其彙總類型變更為 [平均值]****。

![Connected Cars - 變更彙總類型](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

將圖表的 [標題] 變更為「平均機油、胎壓、速度和維修機率 (依車型)」****

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

按一下空白區域以加入新的視覺效果。

1. 從 [視覺效果] 中選取「散佈圖」****視覺效果
2. 將 **Model** 欄位拖曳到 [詳細資料]**** 和 [圖例]**** 區域
3. 將 **fuel** 欄位拖曳到 [X 軸]**** 區域，將彙總變更為 [平均值]
4. 將 **engineTemparature** 欄位拖曳到 [Y 軸]**** 區域，將彙總變更為 [平均值]****
5. 將 **vin** 欄位拖曳到 [大小]**** 區域

![Connected Cars - 加入新的視覺效果](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

將圖表 [標題]**** 變更為「燃料、引擎溫度的平均值 (依車型)」****

![Connected Cars - 變更圖表標題](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

最終的報告顯示如下。

![Connected Cars - 最終報告](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### 將報告中的視覺效果釘選到即時儀表板

按一下 [儀表板] 旁邊的加號圖示，以建立空白儀表板。 您可以將它命名為「車輛遙測分析儀表板」

![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

將上述報告中的視覺效果釘選到儀表板。

![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

已建立上述 3 份報告並將對應的視覺效果釘選到儀表板時，儀表板應如下所示。 如果您尚未建立所有報告，儀表板看起來可能有所不同。

![Connected Cars - 儀表板](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

恭喜！ 您已成功建立即時儀表板。 隨著您繼續執行 CarEventGenerator.exe 和 RealtimeDashboardApp.exe，您應該會在儀表板上看見即時更新。 
應該需要約 10 至 15 分鐘的時間才能完成下列步驟。

## 設定 Power BI 批次處理儀表板

注意: 花大約 2 小時 (從部署成功完成) 的端對端批次處理完成執行，然後處理值得一年，產生之資料的管線。 請稍候再進行後續步驟。

**下載 PowerBI 設計工具檔案**
• 在預先設定的 PowerBI 設計工具的檔案已部署的一部分
• 按一下 [圖表] 檢視中的 [PowerBI] 節點，然後按一下 '下載 PowerBI 設計工具' 檔案連結屬性] 窗格 ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9.5-download-powerbi-designer.png)

• 將儲存在本機

**設定 PowerBI 報告**
• 開啟設計工具的檔案 'VehicleTelemetryAnalytics-桌面 Report.pbix' 使用 PowerBI 桌面。 如果您還沒有，安裝 PowerBI 桌面從 [PowerBI 桌面安裝](http://www.microsoft.com/download/details.aspx?id=45331)。

• 按一下 **編輯查詢**。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

- 按兩下 [來源]****。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- 以佈建為部署一部分的 Azure SQL Server 更新伺服器連接字串。 按一下圖表上的 Azure SQL 節點，然後檢視 [屬性] 窗格的伺服器名稱。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11.5-view-server-name.png)

- 將 [資料庫]**** 保留為 *connectedcar*。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- 按一下 [確定]****。
- 依預設會選取 [Windows 認證]**** 索引標籤，請按一下右邊的 [資料庫]**** 索引標籤，將它變更為 [資料庫認證]****。
- 提供在 Azure SQL Database 部署安裝過程中提供的 [使用者名稱]**** 和 [密碼]****。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- 按一下 [連接]****。
- 針對右窗格中出現的其餘 3 個查詢，重複上述步驟，並更新資料來源連接詳細資料。
- 按一下 [關閉並載入]****。 Power BI Desktop 檔案資料集會連接到 SQL Azure 資料庫資料表。
- **關閉** Power BI Desktop 檔案。

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- 按一下 [儲存]**** 按鈕以儲存變更。

您現在已設定對應至解決方案中的批次處理路徑的所有報告。


## 上傳至 *powerbi.com*

1.  瀏覽至 PowerBI web 入口網站，在 http://powerbi.com 和登入。
2.  按一下 [取得資料]
3.  上傳 Power BI Desktop 檔案。
4.  若要上傳，請按一下 [取得資料] -> [檔案取得]-> [本機檔案]****
5.  瀏覽至 **“VehicleTelemetryAnalytics – Desktop Report.pbix”**
6.  一旦上傳檔案，將會瀏覽回到您的 Power BI 工作區。

將會為您建立資料集、報告和空白儀表板。


將圖表釘選至 **Power BI** 中的現有儀表板 [車輛遙測分析儀表板]****。 按一下上面建立的空白儀表板，然後瀏覽至剛才上傳的報告上的 [報告]**** 區段。

![車輛遙測 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**請注意報表有六個頁面:**  
第 1 頁: 車輛密度  
第 2 頁: 即時車輛健全狀況  
第 3 頁: 積極驅動車輛   
第 4 頁: 回收車輛  
第 5 頁: 燃料有效率地驅動車輛  
第 6 頁: Contoso 標誌

![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)


**從第 3 頁**, ，釘選下列
1.  VIN 的計數  
    ![Connected Cars PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

2.  積極地驅動車輛模型 – 瀑布圖  
    ![車輛遙測 - 釘選圖表 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**從第 5 頁**, ，釘選下列
1.  Vin 的計數    
    ![車輛遙測 - 釘選圖表 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)
2.  油料有效率的車輛模型: 群組的直條圖  
    ![車輛遙測 - 釘選圖表 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**從第 4 頁**，釘選下列項目

1.  Vin 的計數  
    ![車輛遙測 - 釘選圖表 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png)

2.  依城市來回收的車輛模型: Treemap  
    ![車輛遙測 - 釘選圖表 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**從第 6 頁**，釘選下列項目

1.  Contoso 馬達標誌  
    ![車輛遙測 - 釘選圖表 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**組織儀表板**

1.  瀏覽至儀表板
2.  將滑鼠暫留在每個圖表，根據下列完整儀表板影像中提供的名稱來重新命名。 也移動圖表看起來像下面的儀表板。  
    ![車輛遙測 - 組織儀表板 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
    ![車輛遙測 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. 如果您已建立本文件中提到的所有報報，則最終完成的儀表板應該看起來如下。

![車輛遙測 - 組織儀表板 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

恭喜！ 您已成功建立報報和儀表板，可取得車輛健全狀況和駕駛習慣的即時、預測和批次深入分析。





