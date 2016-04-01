<properties
    pageTitle="在 Azure 應用程式服務中建立 PHP-MySQL Web 應用程式並使用 Git 部署"
    description="示範如何建立 PHP Web 應用程式，將資料儲存於 MySQL 中並對 Azure 使用 Git 部署的教學課程。""
    services="app-service\web"
    documentationCenter="php"
    authors="tfitzmac"
    manager="wpickett"
    editor="mollybos"
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="11/19/2015"
    ms.author="tomfitz"/>

#在 Azure 應用程式服務中建立 PHP-MySQL Web 應用程式並使用 Git 部署

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

本教學課程說明如何建立 Php-mysql web 應用程式以及如何將它部署到 [應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714) 使用 Git。 您將使用 [PHP][install-php], 、 MySQL 命令列工具 (屬於 [MySQL][install-mysql])，和 [Git][install-git] 安裝在電腦上。 在本教學課程的指示可運用在任何作業系統，包括 Windows、 Mac 和 Linux 上。 看完本指南後，您將擁有可在 Azure 上執行的 PHP/MySQL Web 應用程式。

您將了解：

* 如何建立 web 應用程式和 MySQL 資料庫，使用 [Azure 入口網站](https://portal.azure.com)。 因為在啟用 PHP [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 根據預設，執行任何特殊需要就能執行 PHP 程式碼。
* 如何使用 Git 來發行與重新發行應用程式到 Azure。

依照本教學課程進行，您將使用 PHP 建置一個簡易的註冊 Web 應用程式。 此應用程式將裝載於 Web Apps 中。 完成之應用程式的螢幕擷取畫面如下：

![Azure PHP web site][running-app]

##設定開發環境

本教學課程假設您有 [PHP][install-php], 、 MySQL 命令列工具 (屬於 [MySQL][install-mysql])，和 [Git][install-git] 安裝在電腦上。


##<a id="create-web-site-and-set-up-git"></a>建立 Web 應用程式並設定 Git 發佈

請遵循以下步驟來建立 Web 應用程式與 MySQL 資料庫：

1. 登入 [Azure 入口網站][management-portal]。
2. 按一下 [ **新增** 圖示。

3. 按一下 [ **See All** 旁 **Marketplace**。 

4. 按一下 [ **Web + 行動**, ，然後 **Web 應用程式 + MySQL**。 然後按一下 [ **建立**。

4. 輸入資源群組的有效名稱。

    ![設定資源群組名稱][resource-group]

5. 輸入新 Web 應用程式的值。

    ![建立 Web 應用程式][new-web-app]

6. 輸入新資料庫的值，包括同意法律條款。

    ![建立新的 MySQL 資料庫][new-mysql-db]

7. 建立 Web 應用程式之後，您將會看到新的 Web 應用程式刀鋒視窗。

7. 在 **設定** 按一下 **連續部署**, ，然後按一下 [ _設定所需設定_。

    ![設定 Git 發佈][setup-publishing]

8. 選取 **本機 Git 儲存機制** 來源。

    ![設定 Git 儲存機制][setup-repository]


9. 若要啟用 Git 發佈，您必須提供使用者名稱和密碼。 請寫下您建立的使用者名稱與密碼 (如果您之前設定過 Git 儲存機制，系統將會略過此步驟。)

    ![Create publishing credentials][credentials]


##取得遠端 MySQL 連線資訊

若要連接至在 Web Apps 中執行的 MySQL 資料庫，您需要連線資訊。 若要取得 MySQL 連線資訊，請依照以下步驟執行：

1. 從資源群組中，按一下資料庫：

    ![選取資料庫][select-database]

2. 從資料庫 **設定**, ，請選取 **屬性**。

    ![選取屬性][select-properties]

2. 記下 `Database`、`Host`、`User Id` 及 `Password` 的值。

    ![注意屬性][note-properties]

##在本機建置及測試您的應用程式

現在您已經建立了 Web 應用程式，您可以在本機開發自己的應用程式，並在測試後進行部署。

註冊應用程式是一項簡單的 PHP 應用程式，您只需提供名稱與電子郵件地址就能註冊活動。 先前的註冊者相關資訊會顯示在資料表中。 註冊資訊會存放在 MySQL 資料庫。 該應用程式包含一個檔案 (複製/貼上以下提供的程式碼)：

* **index.php**︰ 顯示註冊和註冊資訊的資料表的表單。

若要在本機建置與執行應用程式，請遵循下列步驟。 請注意，這些步驟假設您具有 PHP 和 MySQL 命令列工具 （MySQL 的一部分） 在您的本機電腦上設定，且您已啟用 [MySQL 的 PDO 延伸功能][pdo-mysql]。

1. 使用您先前擷取的 `Data Source`、`User Id`、`Password` 和 `Database` 值，連線到遠端 MySQL 伺服器：

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. MySQL 命令提示字元會顯示：

        mysql>

3. 貼上下列 `CREATE TABLE` 命令，以在您的資料庫中建立 `registration_tbl` 資料表：

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. 在本機應用程式資料夾的根目錄中建立 **index.php** 檔案。

5. 開啟 **index.php** 檔案在文字編輯器或 IDE 中並加入下列程式碼，然後完成必要的變更，以標示 `//TODO:` 註解。


        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
            if(!empty($_POST)) {
            try {
                $name = $_POST['name'];
                $email = $_POST['email'];
                $date = date("Y-m-d");
                // Insert data
                $sql_insert = "INSERT INTO registration_tbl (name, email, date)
                           VALUES (?,?,?)";
                $stmt = $conn->prepare($sql_insert);
                $stmt->bindValue(1, $name);
                $stmt->bindValue(2, $email);
                $stmt->bindValue(3, $date);
                $stmt->execute();
            }
            catch(Exception $e) {
                die(var_dump($e));
            }
            echo "<h3>Your're registered!</h3>";
            }
            // Retrieve data
            $sql_select = "SELECT * FROM registration_tbl";
            $stmt = $conn->query($sql_select);
            $registrants = $stmt->fetchAll();
            if(count($registrants) > 0) {
                echo "<h2>People who are registered:</h2>";
                echo "<table>";
                echo "<tr><th>Name</th>";
                echo "<th>Email</th>";
                echo "<th>Date</th></tr>";
                foreach($registrants as $registrant) {
                    echo "<tr><td>".$registrant['name']."</td>";
                    echo "<td>".$registrant['email']."</td>";
                    echo "<td>".$registrant['date']."</td></tr>";
                }
                echo "</table>";
            } else {
                echo "<h3>No one is currently registered.</h3>";
            }
        ?>
        </body>
        </html>

4.  在終端機中，移至您的應用程式資料夾並輸入以下命令：

        php -S localhost:8000

您可以現在瀏覽至 **http://localhost:8000/ /** 測試應用程式。


##發佈您的應用程式

當您在本機完成應用程式測試之後，可以使用 Git 將其發佈至 Web Apps。 您將初始化本機 Git 儲存機制並發行該應用程式。

> [AZURE.NOTE]
> 這些步驟與上述「建立 Web 應用程式並設定 Git 發行」小節結尾處的 Azure 入口網站中所示的步驟相同。

1. （選擇性） 如果您忘記或是錯置了 Git 遠端儲存機制 URL，瀏覽至 Azure 入口網站上的 web 應用程式內容。

1. 開啟 GitBash (如果 Git 位於您的 `PATH`，則為終端機)，將目錄變更為應用程式的根目錄，並執行下列命令：

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    系統會提示您輸入先前建立的密碼。

    ![透過 Git 初始發送至 Azure][git-initial-push]

2. 瀏覽至 **http://[site name].azurewebsites.net/index.php** 若要開始使用應用程式 （這項資訊會存放在您的帳戶儀表板） ︰

    ![Azure PHP web site][running-app]

發佈應用程式之後，您可以開始對其進行變更，並使用 Git 來發佈它們。

##將變更發佈至應用程式

若要將變更發佈至應用程式，請依照以下步驟進行：

1. 在本機對應用程式進行變更。
2. 開啟 GitBash (如果 Git 位於您的 `PATH`，則為終端機)，將目錄變更為應用程式的根目錄，並執行下列命令：

        git add .
        git commit -m "comment describing changes"
        git push azure master

    系統會提示您輸入先前建立的密碼。

    ![透過 Git 將網站變更發送至 Azure][git-change-push]

3. 瀏覽至 **http://[site name].azurewebsites.net/index.php** 以查看您的應用程式以及您所做的變更 ︰

    ![Azure PHP web site][running-app]

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## 後續步驟

如需詳細資訊，請參閱 [PHP 開發人員中心](/develop/php/)。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx
 


