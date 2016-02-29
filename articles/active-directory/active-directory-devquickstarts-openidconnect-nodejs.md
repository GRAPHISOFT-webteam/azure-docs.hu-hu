<properties
    pageTitle="開始使用 node.js 進行 Azure AD 登入和登出"
    description="如何建立可整合 Azure AD 以進行登入的 node.js Express MVS Web App。"
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="11/19/2015"
    ms.author="brandwe"/>

# 使用 Azure AD 進行 Web 應用程式登入與登出


我們將在此處使用 Passport 來執行下列動作：

- 使用 Azure AD 和 v2.0 App 模型將使用者登入 App。
- 顯示使用者的一些相關資訊。
- 讓使用者登出 App。

**Passport** 是 Node.js 的驗證中介軟體。 您可以暗中將極具彈性且模組化的 Passport 放入任何 Express 或 Resitify Web 應用程式。 一組完整的策略可支援使用使用者名稱和密碼、Facebook、Twitter 及其他等驗證。 我們已為 Microsoft Azure Active Directory 開發一項策略。 我們將安裝此模組，然後加入 Microsoft Azure Active Directory `passport-azure-ad` 外掛程式。

為執行此作業，您必須執行下列動作：

1. 註冊應用程式。
2. 設定您的 App 以使用 Passport-azure-ad 策略。
3. 使用 Passport，向 Azure AD 發出登入和登出要求。
4. 列印出使用者的相關資料。

本教學課程的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS)。  若要跟著做，您可以 [下載為.zip 的應用程式的基本架構](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) 或再製基本架構:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

本教學課程最後也會提供完整的應用程式。

## 1.註冊應用程式
- 登入 Azure 管理入口網站。
- 在左側導覽中，按一下 **Active Directory**。
- 選取您要註冊應用程式的租用戶。
- 按一下 [ **應用程式** ] 索引標籤，然後按一下 [新增抽屜。
- 遵循提示，並建立新 **Web 應用程式和/或 WebAPI**。
    -  **名稱** 應用程式將說明您的應用程式使用者
    -    **登入 URL** 是您的應用程式基底 URL。  基本架構的預設值是 ' http://localhost:3000//驗證/openid/傳回 '。
    -  **應用程式識別碼 URI** 是您的應用程式的唯一識別碼。  慣例會使用 `https://<tenant-domain>/<app-name>`，例如：`https://contoso.onmicrosoft.com/my-first-aad-app`
- 完成註冊後，AAD 會為您的應用程式指派一個唯一用戶端識別碼。  您在後續章節中將會用到這個值，所以請從 [設定] 索引標籤中複製此值。

## 2.在目錄中新增必要條件

從命令列中，將目錄位置變更至根資料夾 (若目錄位置原本不在該處)，然後執行下列命令：

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- 此外，您也需要我們的 `passport-azure-ad`：

- `npm install passport-azure-ad`

如此會安裝 passport-azure-ad 做為依據的程式庫。

## 3.設定您的 App 以使用 passport-node-js 策略。
我們將在此設定 Express 中介軟體，以使用 OpenID Connect 驗證通訊協定。  Express 將用來發出登入和登出要求、管理使用者的工作階段，以及取得使用者相關資訊等其他作業。

-   若要開始，請開啟專案根目錄中的 `config.js` 檔案，並在 `exports.creds` 區段中輸入應用程式的組態值。
    -    `clientID:` 是 **應用程式識別碼** 指派給您的應用程式中註冊入口網站。
    -    `returnURL` 是 **重新導向 Uri** 您輸入在入口網站。
    - `clientSecret` 是您在入口網站中輸入的密碼

- 接下來開啟專案根中的 `app.js` 檔案，並新增下列呼叫以叫用與 `passport-azure-ad` 一併使用的 `OIDCStrategy` 策略


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// add a logger

var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- 之後，請使用我們僅供參考的策略來處理登入要求

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport 會使用適用於它的所有策略 (Twitter、Facebook 等) 且所有策略寫入器都依循的類似模式。 查看此策略，您會看見我們將它當成 function() 來傳遞，其中含有一個 token 和一個 done 做為參數。 一旦策略完成所有工作之後，便會盡責地返回。 當它完成之後，我們會想要儲存使用者並隱藏權杖，因此我們不需再次要求它。


> [AZURE.IMPORTANT] 
上述程式碼會讓所有使用者經歷伺服器的驗證。 這就是所謂的自動註冊。 在生產伺服器中，您想要讓所有人都必須先經歷您所決定的註冊過程。 這通常是您在取用者 App 中看到的模式，可讓您向 Facebook 註冊，但接著會要求您填寫其他資訊。 如果這不是範例應用程式，我們就只能從傳回的權杖物件中擷取電子郵件，然後要求他們填寫其他資訊。 由於這是測試伺服器，因此，我們直接將它們加入至記憶體中的資料庫。

- 接下來，我們會新增方法，依 Passport 所要求，允許我們持續追蹤已登入的使用者。 這包括將使用者資訊序列化和還原序列化：

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

- 接下來，加入可載入 express 引擎的程式碼。 您會在此處看到我們使用 Express 所提供的預設 /views 和 /routes 模式。

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 最後，加入此路由，這會將實際的登入要求遞交給 `passport-azure-ad` 引擎：

```JavaScript

// Our Auth routes (Section 3)

// POST /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## 4.使用 Passport，向 Azure AD 發出登入和登出要求

您的應用程式現在已正確設定，將使用 OpenID Connect 驗證通訊協定與 v2.0 端點通訊。  `passport-azure-ad` 已經處理所有製作驗證訊息、 驗證 Azure ad 的權杖和維護使用者工作階段的瑣碎詳細資料。  所有剩餘的部分就是為使用者提供一種方式來登入、登出，以及收集關於已登入使用者的其他資訊。

- 首先，將預設、登入、帳戶及登出方法加入 `app.js` 檔案：

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-   讓我們詳細檢閱這些方法：
    -   `/` 路由將重新導向至 index.ejs 檢視，其會在要求中傳遞使用者 (若有的話)
    -  `/account` 路由會先 ***確保我們驗證*** (我們的實作如下)，然後將使用者傳遞要求中，好讓我們可以取得使用者的其他資訊。
    - `/login` 路由將從 `passport-azuread` 呼叫 azuread-openidconnect 驗證器，如果失敗，則會再次將使用者重新導向至 /login
    - `/logout` 會直接呼叫 logout.ejs (以及路由)，其會清除 Cookie，然後讓使用者返回至 index.ejs


- 針對 `app.js` 的最後一個部分，加入可在上述 `/account` 中使用的 EnsureAuthenticated 方法。

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

- 最後，在 `app.js` 中實際建立伺服器本身：

```JavaScript

app.listen(3000);

```


## 5.在 Express 中建立檢視與路由以在網站中顯示使用者

我們已完成 `app.js`。 現在只需新增路由和檢視即可，這兩者將會向使用者顯示我們取得的資訊，以及控制我們建立的 `/logout` 和 `/login` 路由。

- 在根目錄下方建立 `/routes/index.js` 路由。

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- 在根目錄下方建立 `/routes/user.js` 路由

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

這些簡單路由只會將要求傳遞到我們的檢視，包括使用者 (如果有的話)。

- 建立 `/views/index.ejs` 的根目錄下的檢視。 這是簡單的網頁會呼叫我們登入和登出的方法，並讓我們抓取帳戶資訊。 請注意，我們可以使用條件式 `if (!user)`，因為在要求中傳遞使用者就證實我們擁有已登入的使用者。

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- 在根目錄下方建立 `/views/account.ejs` 檢視，如此即可檢視 `passport-azuread` 放置於使用者要求的其他資訊。

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- 最後，可藉由新增版面配置，使它看起來很美觀。 在根目錄底下建立 '/views/layout.ejs' 檢視

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/account">Account</a> | 
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

最後，建置並執行您的應用程式！ 

執行 `node app.js` 並瀏覽至 `http://localhost:3000`


使用個人的 Microsoft 帳戶或工作或學校帳戶登入，並注意 /account 清單中使用者身分識別的反映狀態。  您的 Web 應用程式現在使用業界標準的通訊協定保護，可以使用個人與工作/學校帳戶來驗證使用者。

(不含您的設定值) 已完成的範例供您參考 [依現狀的.zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip), ，或您可以從 GitHub 複製它:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


您現在可以進入更進階的主題。  您可以嘗試：

[使用 Azure AD 保護 Web API >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
