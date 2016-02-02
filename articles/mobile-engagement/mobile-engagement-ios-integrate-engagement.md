<properties
    pageTitle="Azure Mobile Engagement iOS SDK 整合"
    description="Azure Mobile Engagement iOS SDK 的最新更新與程序"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="MehrdadMzfr"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="08/05/2015"
    ms.author="MehrdadMzfr" />


# 如何在 iOS 上整合 Engagement

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)


此程序描述在您的 iOS 應用程式中，啟動 Engagement 的分析和監視功能時，最簡單的方法。
> [AZURE.IMPORTANT] Engagement SDK 需要 iOS6 以上：應用程式的部署目標必須至少為 iOS 6。

下列步驟便足以啟用計算使用者、工作階段、活動、當機和技術相關的所有統計資料需要的記錄檔之報告。 需要計算事件、 錯誤及工作等其他統計資料的記錄檔報告必須使用 Engagement API 手動完成 (請參閱 [如何使用進階的 Mobile Engagement 標記 API iOS 應用程式中](mobile-engagement-ios-use-engagement-api.md) 因為這些統計資料與相依的應用程式。

## 將 Engagement SDK 嵌入您的 iOS 專案

下載 iOS SDK 從 [這裡](http://aka.ms/qk2rnj)。
將 Engagement SDK 加入至您的 iOS 專案: 在 Xcode 中，以滑鼠右鍵按一下專案，然後選取 **[新增檔案至...]** 選擇 `EngagementSDK` 資料夾。

Engagement 需要額外的架構才能運作：在專案總管中，開啟專案窗格並選取正確的目標。 然後，開啟 [建置階段]**** 索引標籤，在 [連結二進位檔與程式庫]**** 功能表中加入下列架構：

> -   `AdSupport.framework` -將連結設為 `選擇性`
> -   `SystemConfiguration.framework`
> -   `CoreTelephony.framework`
> -   `CFNetwork.framework`
> -   `CoreLocation.framework`
> -   `libxml2.dylib`
> [AZURE.NOTE] AdSupport 架構可以移除。Engagement 需要此架構來收集 IDFA。不過，可以停用 IDFA 收集 \<ios-sdk-engagement-idfa\> 以符合新 Apple 原則關於此識別碼。

## 初始化 Engagement SDK

您需要修改應用程式委派：

-   在實作檔案頂端匯入 Engagement 代理程式：

        [...]
        #import "EngagementAgent.h"

-   在 '**applicationDidFinishLaunching:**' 或 '**application:didFinishLaunchingWithOptions:**' 方法內初始化 Engagement：

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
          [...]
        }


## 基本報告

### 建議使用的方法: 多載您 `UIViewController` 類別

為了啟用 Engagement 計算使用者、 工作階段、 活動、 當機和技術的統計資料所需的所有記錄檔報告，您可以讓所有您 `UIViewController` 子類別繼承自 `EngagementViewController` 類別 (相同的規則 `UITableViewController` -\ > `EngagementTableViewController`)。

**沒有 Engagement：**

    #import <UIKit/UIKit.h>
    
    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }
    
    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**有 Engagement：**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"
    
    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }
    
    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### 替代方法: 呼叫 `startActivity()` 手動

如果您無法或不想多載您 `UIViewController` 類別，您可以改為啟動活動藉由呼叫 `EngagementAgent`的直接方法。
> [AZURE.IMPORTANT] IOS SDK 會自動呼叫 `endActivity()` 方法在應用程式關閉時。 因此，它是 *高* 建議您呼叫 `startActivity` 方法，每當使用者的活動變更時，以及 *永不* 呼叫 `endActivity` 方法，因為呼叫此方法會強制結束目前的工作階段。

## 位置報告

Apple 服務條款不允許應用程式只為了統計資料的目的而使用位置追蹤。 因此，建議您只有當應用程式也會因為另一個原因而使用位置追蹤時，才啟用位置報告。

從 iOS 8 開始，您必須在應用程式的 Info.plist 檔案中提供您的應用程式設定的機碼 [NSLocationWhenInUseUsageDescription] 或 [NSLocationAlwaysUsageDescription] 的字串所使用的位置服務的描述。 如果您想要在背景以 Engagement 報告位置，請加入 NSLocationAlwaysUsageDescription 索引鍵。 在其他情況下，請加入 NSLocationWhenInUseUsageDescription 索引鍵。

### 延遲區域位置報告

延遲區域位置報告允許報告國家、地區以及與裝置相關聯的位置。 這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)。 每個工作階段最多報告一次裝置區域。 絕不會使用 GPS，因此這類位置報告對於電池的影響很小 (但不是沒有)。

報告的區域用來計算關於使用者、工作階段、事件與錯誤的地理統計資料。 它們也可用來做為觸達活動的準則。 針對裝置報告的最後已知的區域可以藉由 [裝置 API] 來擷取。

若要啟用延遲區域位置報告，請在初始化 Engagement 代理程式之後加入下面這一行：

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### 即時位置報告

即時位置報告允許報告與裝置相關聯的緯度和經度。 根據預設，這類位置報告只會使用網路位置 (根據基地台識別碼或 WIFI)，且只會在應用程式於前景中執行 (也就是在工作階段) 時，報告才會為作用中。

即時位置「不會」**用來計算統計資料。 其唯一目的是要允許即時使用
地理柵欄 \<Reach-Audience-geofencing\> 觸達活動的準則。

若要啟用即時位置報告，請在初始化 Engagement 代理程式之後加入下面這一行：

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### GPS 的報告

根據預設，即時位置報告只會使用網路位置。 若要啟用 GPS 位置 (精準度大為提高)，請加入：

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### 背景報告

根據預設，即時位置報告只在應用程式在前景中執行 (也就是在工作階段) 時才會為作用中。 若要也在背景中啟用報告，請加入：

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] 當應用程式在背景中執行，即使啟用 GPS，也只會報告網路位置。

您的應用程式進入背景時，此函式的實作會呼叫 [startMonitoringSignificantLocationChanges]。 請注意，如果新的位置事件抵達，它會自動重新啟動您的應用程式到背景中。

## 進階報告

此外，如果您想要報告應用程式特定事件、 錯誤和作業，您必須透過方法使用 Engagement API `EngagementAgent` 類別。 這個類別的物件可以藉由呼叫擷取 `[共用的 EngagementAgent]` 靜態方法。

Engagement API 允許使用所有 Engagement 的進階功能，詳情請見＜如何
在 iOS 上的使用 engagement API (以及技術文件 `EngagementAgent` 類別)。

## 停用 IDFA 集合

根據預設，Engagement 會使用 [IDFA] 來唯一識別使用者。 但是，如果您未在應用程式中的其他地方使用廣告，您可能會遭到應用程式市集審查程序拒絕。 加入前置處理器巨集可以停用 IDFA 集合 `ENGAGEMENT_DISABLE_IDFA` 在 pch 檔案 (或是在 `建置設定` 應用程式)。 這可確保沒有任何參考 `ASIdentifierManager`, ，`advertisingIdentifier` 或 `isAdvertisingTrackingEnabled` 應用程式組建中。

**prefix.pch** 檔案中的整合：

    #define ENGAGEMENT_DISABLE_IDFA
    ...

您可以藉由檢查 Engagement 測試記錄檔，確認 IDFA 集合已在應用程式中正確停用。請參閱 Integration Test\<ios-sdk-engagement-test-idfa\> 文件的進一步資訊。

## 停用記錄報告

### 使用方法呼叫

如果您想要 Engagement 停止傳送記錄檔，可以呼叫：

    [[EngagementAgent shared] setEnabled:NO];

這個呼叫是持續性: 它會使用 `使用 NSUserDefaults` 來存放的資訊。

您可以啟用記錄報告一次呼叫相同函式 `是`。

### 設定組合中的整合

不呼叫此函式，您也可以整合此設定直接在現有 `Settings.bundle` 檔案。 字串 `engagement_agent_enabled` 必須當做喜好設定識別碼，而且必須與切換開關相關聯 (`PSToggleSwitchSpecifier`)。

下列範例的 `Settings.bundle` 示範如何實作它:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>


[device api]: http://go.microsoft.com/?linkid=9876094 
[nslocationwheninuseusagedescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26 
[nslocationalwaysusagedescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18 
[startmonitoringsignificantlocationchanges]: http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges 
[idfa]: https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier 

