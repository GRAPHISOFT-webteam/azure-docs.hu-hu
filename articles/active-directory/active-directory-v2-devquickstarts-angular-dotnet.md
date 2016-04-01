<properties
    pageTitle="開始使用 Azure AD AngularJS | Microsoft Azure"
    description="如何建置可在個人 Microsoft 帳戶及工作或學校帳戶登入使用者的 Angular JS 單一頁面應用程式。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="12/09/2015"
    ms.author="dastrock"/>


# 應用程式模型 v2.0 預覽版本：在 AngularJS 單一頁面應用程式中新增登入 - .NET

在本文中，我們會使用 Azure Active Directory 應用程式模型 v2.0，將以 Microsoft 帳戶的登入新增至 AngularJS 應用程式。  應用程式模型 v2.0 可讓您在您的應用程式中執行單一整合，並且以個人和工作/學校帳戶驗證使用者。

這個範例是簡單的待辦事項清單單一頁面應用程式，在後端 REST API 儲存工作、使用 .NET 4.5 MVC 架構撰寫，並且使用 Azure AD 的 OAuth 持有人權杖進行保護。  AngularJS 應用程式會使用我們的開放原始碼 JavaScript 驗證程式庫 [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) 來處理整個的登入程序，並取得權杖，以呼叫 REST API。  相同的模式可以套用至其他 REST api，例如驗證 [Microsoft Graph](https://graph.microsoft.com) 或 Azure 資源管理員 Api。

## 下載

若要開始，您必須下載並安裝 Visual Studio。  然後您可以複製或 [下載](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) 基本架構應用程式 ︰

```
git clone --branch skeleton https://github.com/AzureADQuickStarst/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

基本架構應用程式包含簡單的 AngularJS 應用程式的重複使用程式碼，但是會遺漏所有身分識別相關的部分。  如果您不想要跟著做，您可以改為再製或 [下載](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) 完成的範例。

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## 註冊應用程式

首先，建立應用程式中的 [應用程式註冊入口網站](https://apps.dev.microsoft.com), ，或請遵循下列 [詳細步驟](active-directory-v2-app-registration.md)。  請確定：

- 新增 **Web** 平台應用程式。
- 輸入正確 **重新導向 URI**。 此範例的預設值是 `https://localhost:44326/`。
- 保留 **允許隱含流程** 已啟用] 核取方塊。 

複製 **應用程式識別碼** ，其指派給您的應用程式，您將在稍後需要。 

## 安裝 adal.js
若要開始，請瀏覽至您下載並安裝 adal.js 的專案。  如果您有 [bower](http://bower.io/) 安裝，您可以只要執行這個命令。  若是任何相依性版本不符，請選擇較高的版本。
```
bower install adal-angular#experimental
```

或者，您可以手動下載 [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) 和 [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js)。  將這兩個檔案新增至 `TodoSPA` 專案的 `app/lib/adal-angular-experimental/dist` 目錄。

現在在 Visual Studio 中開啟專案，並且在主頁面本文的結尾處載入 adal.js：

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## 設定 REST API

在我們進行設定的同時，讓後端 REST API 運作。  在專案根目錄中，開啟 `web.config`，並且取代 `audience` 值。  REST API 會使用此值來驗證在 AJAX 要求時從 Angular 應用程式收到的權杖。

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>
    
...
```

這是我們討論 REST API 運作方式所花費的所有時間。  可自由摸索、 程式碼，但如果您想要了解保護 web Api 與 Azure AD，請參閱 [這篇文章](active-directory-v2-devquickstarts-dotnet-api.md)。 

## 登入使用者
撰寫一些身分識別程式碼。  您可能已經發現 adal.js 包含 AngularJS 提供者，它運用 Angular 路由機制相當良好。  從將 adal 模組新增至應用程式開始：

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

您現在可以使用您的應用程式識別碼初始化 `adalProvider`：

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

太棒了，現在 adal.js 有了保護您的應用程式並且登入使用者所需的所有資訊。  若要對應用程式中的特定路由強制登入，它只需要一行程式碼：

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

現在，當使用者按一下 `TodoList` 連結時，需要的話，adal.js 會自動重新導向至 Azure AD 進行登入。  您也可以藉由在控制器中叫用 adal.js，明確傳送登入和登出要求：

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## 顯示使用者資訊
現在使用者已登入，您可能需要存取您的應用程式中已登入使用者的驗證資料。  Adal.js 會在 `userInfo` 物件中為您公開這項資訊。  若要在檢視中存取此物件，首先將 adal.js 新增至對應控制器的根範圍：

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

然後您可以直接在檢視中定址 `userInfo` 物件： 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

您也可以使用 `userInfo` 物件，以判斷使用者登入或登出。

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## 呼叫 REST API
最後，取得某些權杖並且呼叫 REST API，以建立、讀取、更新和刪除工作。  您知道嗎？  您不必做 *事*。  Adal.js 會自動為您取得、快取和重新整理權杖。  它也會將這些權杖附加至您傳送至 REST API 的傳出 AJAX 要求。  

到底是如何運作的呢？ 它是所有感謝的神奇之處 [AngularJS 攔截器](https://docs.angularjs.org/api/ng/service/$http), ，可讓 adal.js 轉換傳出和傳入的 http 訊息。  此外，adal.js 會假設傳送至相同網域做為視窗的任何要求，應該使用適用於相同應用程式識別碼的權杖做為 AngularJS 應用程式。  這就是為什麼我們在 Angular 應用程式和 NodeJS REST API 中使用相同的應用程式識別碼。  當然，您可以覆寫這個行為，並且視需要告知 adal.js 取得其他 REST API 的權杖 - 但是對於這個簡單的案例，使用預設值即可。

以下是程式碼片段，說明從 Azure AD 傳送具有持有人權杖的要求有多輕鬆：

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

恭喜！  您的 Azure AD 整合式單一頁面應用程式現在已完成。  佩服吧！  它可以驗證使用者、使用 OpenID Connect 安全地呼叫其後端 REST API，以及取得使用者的基本資訊。  根據預設，它支援來自 Azure AD 具有個人 Microsoft 帳戶或工作/學校帳戶的任何使用者。  執行應用程式，並且在瀏覽器中瀏覽至 `https://localhost:44326/`。  使用個人 Microsoft 帳戶或工作/學校帳戶登入。  將工作新增至使用者待辦事項清單，然後登出。  嘗試使用其他類型的帳戶登入。 如果您需要建立工作/學校使用者的 Azure AD 租用戶 [了解如何取得租用這裡](active-directory-howto-tenant.md) （它是免費的）。

若要繼續了解應用程式模型 v2.0 預覽，head 回我們 [v2.0 開發人員指南](active-directory-appmodel-v2-overview.md)。  如需其他資源，請參閱：

- [GitHub 上的 Azure 範例 >>](https://github.com/Azure-Samples)
- [堆疊溢位上的 Azure AD >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- 在 azure AD 文件 [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)

