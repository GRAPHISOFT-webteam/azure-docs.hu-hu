<properties
   pageTitle="使用 Java 在事件中樞內透過 Storm on HDInsight 處理事件 | Azure"
   description="了解如何使用 Maven 建立的 Java Storm 拓撲處理事件中樞資料。"
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>

# 使用 Storm on HDInsight 處理 Azure 事件中樞的事件 (Java)

Azure 事件中樞可讓您從網站、應用程式和裝置處理巨量資料。 事件中樞 Spout 可讓您輕鬆地使用 Apache Storm on HDInsight 來即時分析資料。 您也可以使用事件中樞 Bolt 將資料從 Storm 寫入事件中樞。

在本教學課程中，您將了解如何使用事件中樞 Spout 和 Bolt 在以 Java 為基礎的 Storm 拓撲中讀取和寫入資料。

## 必要條件

* Apache Storm on HDInsight 叢集。 使用下列其中一個使用者入門文章以建立叢集：

    - A [Linux 型叢集](hdinsight-apache-storm-tutorial-get-started-linux.md)︰ 如果您想要使用 SSH 與 Linux、 Unix、 OS X 或 Windows 用戶端從叢集一起運作，請選取此選項

    - A [windows 叢集](hdinsight-apache-storm-tutorial-get-started.md)︰ 選取此選項，如果您想要使用 PowerShell 來處理從 Windows 用戶端叢集

    > [AZURE.NOTE] 兩個叢集類型之間的唯一差異是您是否使用 SSH 來提交至叢集或 web form 拓撲。

* [Azure 事件中樞](../event-hubs/service-bus-event-hubs-csharp-ephcs-getstarted.md)

* [Oracle Java Developer Kit (JDK) 第 7 版](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或同等權限，例如 [OpenJDK](http://openjdk.java.net/)

* [Maven](https://maven.apache.org/download.cgi): Maven 是 Java 專案的專案建置系統

* 文字編輯器或 Java 整合式開發環境 (IDE)

    > [AZURE.NOTE] 您的編輯器或 IDE 可能具有處理 maven，但不會記載在這份文件的特定功能。 如需編輯環境功能的詳細資訊，請參閱所使用產品的文件。

 * SSH 用戶端。 如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列其中一篇文章：

    - [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

* SCP 用戶端。 與所有 Linux、Unix 和 OS X 系統一起提供。 Windows 用戶端，我們建議 PSCP 可從 [PuTTY 下載頁面下載](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

##了解範例

 [Hdinsight java-storm eventhub](https://github.com/Blackmist/hdinsight-java-storm-eventhub) 範例包含兩種拓撲 ︰

__com.microsoft.example.EventHubWriter__ 隨機資料寫入 Azure 事件中樞。 資料由 Spout 產生，而且是隨機裝置識別碼和裝置值。 所以它是模擬會發出字串識別碼和數值的部分硬體。

__com.microsoft.example.EventHubReader__ 讀取事件中樞 （EventHubWriter，所寫入的資料） 資料，並將它儲存到 HDFS (WASB 在此情況下，因為這是撰寫並測試使用 Azure HDInsight) 中，/devicedata 目錄中。

資料會在寫入事件中樞之前格式化為 JSON 文件，當讀取器讀取時會從 JSON 剖析，並且進入 Tuple。 JSON 格式如下所示：

    { "deviceId": "unique identifier", "deviceValue": some value }

使用 JSON 文件將資料儲存至事件中樞的原因是讓我們知道格式為何，而不是依賴事件中樞 Spout 和 Bolt 的內部格式化機制。

###專案組態

 **POM.xml** 檔案包含此 Maven 專案的組態資訊。 有趣的部分是：

####EventHubs Storm Spout 相依性

    <dependency>
      <groupId>com.microsoft.eventhubs</groupId>
      <artifactId>eventhubs-storm-spout</artifactId>
      <version>0.9.3</version>
    </dependency>

這樣會加入 eventhubs-storm-spout 封裝的相依性，包含用以從事件中樞讀取的 Spout 和寫入事件中樞的 Bolt。

> [AZURE.NOTE] 此套件並沒有出現在 [Maven]，並將以手動方式安裝在本機 Maven 儲存機制中稍後的步驟。

####HdfsBolt 和 WASB 元件

HdfsBolt 一般是用來將資料儲存至 Hadoop 分散式檔案系統 HDFS。 不過，HDInsight 叢集會使用 Azure 儲存體 (WASB) 做為預設的資料存放區，所以我們必須載入數個元件，讓 HdfsBolt 了解 WASB 檔案系統。

      <!--HdfsBolt stuff -->
      <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-hdfs</artifactId>
        <exclusions>
          <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
          </exclusion>
          <exclusion>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
          </exclusion>
        </exclusions>
        <version>0.9.3</version>
      </dependency>
      <!--
     This is a temporary workaround to make HdfsBolt work with WASB through hadoop-azure project.
     For now, we have to build hadoop-client, hadoop-hdfs and hadoop-azure from Hadoop trunk
     (which defaults to 3.0.0-SNAPSHOT version). And push those jars and dependencies to local
     mvn repo (take a look at push_lib_mvn.ps1).

     Once Hadoop 2.7 is released, we can just switch to that version.
     Note that hadoop-azure is added to Hadoop on Hadoop 2.7.
     -->
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-client</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-hdfs</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-azure</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-common</artifactId>
       <version>3.0.0-SNAPSHOT</version>
       <exclusions>
         <exclusion>
           <groupId>org.slf4j</groupId>
           <artifactId>slf4j-log4j12</artifactId>
         </exclusion>
       </exclusions>
     </dependency>
     <dependency>
       <groupId>com.microsoft.windowsazure.storage</groupId>
       <artifactId>microsoft-windowsazure-storage-sdk</artifactId>
       <version>0.6.0</version>
     </dependency>

> [AZURE.NOTE] 啟用 WASB 封裝不在 Maven 儲存機制上，且將在稍後的步驟以手動方式安裝。

####maven-compiler-plugin

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

這會告訴 Maven，專案應該以 Java 7 的相容性編譯，這是 HDInsight 叢集所使用的版本。

####maven-shade-plugin

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <!-- Keep us from getting a can't overwrite file error -->
          <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
          </transformers>
          <!-- Keep us from getting a bad signature error -->
          <filters>
            <filter>
                <artifact>*:*</artifact>
                <excludes>
                    <exclude>META-INF/*.SF</exclude>
                    <exclude>META-INF/*.DSA</exclude>
                    <exclude>META-INF/*.RSA</exclude>
                </excludes>
            </filter>
        </filters>
        </configuration>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
          </execution>
        </executions>
      </plugin>

這是用來將解決方案封裝至 uber jar，它包含專案程式碼和必要的相依性。 它也用來：

* 重新命名相依性的授權檔案：若尚未完成，則會在以 Windows 為基礎的 HDInsight 叢集上的執行階段造成錯誤。

* 排除安全性/簽章：若尚未完成，則會在 HDInsight 叢集上的執行階段造成錯誤。

####exec-maven-plugin

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.2.1</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

可讓您使用下列命令在開發環境上本機執行拓撲：

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

例如，`mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`。

####資源區段

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

這會定義專案所需的資源：

- **EventHubs.properties**︰ 包含用來連接到 Azure 事件中樞資訊
- **core-site.xml**︰ 包含 HDInsight 叢集所使用的 Azure 儲存體的相關資訊。

您必須在這兩個項目中填入事件中樞與 HDInsight 叢集的相關資訊。

##設定環境變數

當您在開發工作站上安裝 Java 和 JDK 時可能會設定下列環境變數。 不過，您應該檢查它們是否存在，以及它們是否包含您系統的正確值。

* **JAVA_HOME** -應該指向已安裝 Java 執行階段環境 (JRE) 的目錄。 例如，在 Unix 或 Linux 散發套件上，它的值應該類似 `/usr/lib/jvm/java-7-oracle` 在 Windows 中，它的值應該類似 `c:\Program Files (x86)\Java\jre1.7`

* **路徑** -應該包含下列路徑 ︰

    * **JAVA_HOME** （或對等的路徑）

    * **JAVA_HOME\bin** （或對等的路徑）

    * 已安裝 Maven 的目錄

## 設定事件中樞

事件中樞是此範例的資料來源。 請使用下列步驟建立新的事件中心。

1. 從 [Azure 傳統入口網站](https://manage.windowsazure.com), ，請選取 **新增** > **服務匯流排** > **事件中心** > **自訂建立**。

2. 上 **新增新的事件中心** 畫面上，輸入 **事件中樞名稱**, ，請選取 **區域** 建立中樞，並建立新的命名空間或選取現有的 gpo。 按一下 [ **箭號** 以繼續。

    ![精靈頁面 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

    > [AZURE.NOTE] 您應該選取相同 **位置** 與 Storm on HDInsight 伺服器以降低延遲和成本。

2. 在 **設定事件中樞** 畫面上，輸入 **分割計數** 和 **訊息保留** 值。 在此範例中，資料分割計數使用 10，訊息保留使用 1。 請記下資料分割計數，因為您稍後會用到這個值。

    ![精靈頁面 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. 建立事件中心之後，選取的命名空間中，選取 **事件中樞**, ，然後選取您稍早建立的事件中樞。

4. 選取 **設定**, ，然後使用下列資訊建立兩個新的存取原則。

    <table>
    <tr><th>名稱</th><th>權限</th></tr>
    <tr><td>寫入器</td><td>傳送</td></tr>
    <tr><td>讀取者</td><td>接聽</td></tr>
    </table>

    建立權限之後，請選取 **儲存** 在頁面底部的圖示。 這樣會建立共用存取原則，可用來傳送 (寫入器) 和接聽 (讀取器) 此事件中樞的訊息。

    ![原則](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. 儲存原則之後，請使用 **共用存取金鑰產生器** 要擷取的索引鍵之頁面的底部 **寫入器** 和 **讀取器** 原則。 請儲存這些金鑰，因為稍後會用到。

## 下載和建置專案

1. 從 GitHub 下載的專案 ︰ [hdinsight java-storm eventhub](https://github.com/Blackmist/hdinsight-java-storm-eventhub)。 您可以下載封裝為 zip 封存，或使用 [git](https://git-scm.com/) 複製專案在本機。

2. 使用下列命令以將專案中包含的封裝安裝至本機 Maven 儲存機制。 這樣會啟用事件中樞 Spout 和 Bolt，以及使用 HdfsBolt 寫入 Azure 儲存體 (WASB) 的能力。

        mvn -q install:install-file -Dfile=lib/eventhubs/eventhubs-storm-spout-0.9.3-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9.3 -Dpackaging=jar

        mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-azure-3.0.0-SNAPSHOT.jar

        mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-client-3.0.0-SNAPSHOT.jar

        mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-hdfs-3.0.0-SNAPSHOT.jar

        mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.jar -DpomFile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.pom

        mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom

        mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom

        mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom

    > [AZURE.NOTE] 如果您使用 Powershell，必須輸入您 mau `-D` 以引號括住的參數。 例如，`"-Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom"`。

    這些檔案還有原本從 https://github.com/hdinsight/hdinsight-storm-examples，這樣才能找到有最新版本。

3. 使用下列項目建置和封裝專案：

        mvn package

    這會下載必要的相依性、建置然後封裝專案。 輸出會儲存在 __/目標__ 目錄 __EventHubExample-1.0-SNAPSHOT.jar__。

## 部署拓撲

此專案所建立的 jar 中包含兩種拓撲。 __com.microsoft.example.EventHubWriter__ 和 __com.microsoft.example.EventHubReader__。 應該先啟動 EventHubWriter 拓撲，因為它會將事件寫入事件中樞，然後由 EventHubReader 讀取。

###如果使用以 Linux 為基礎的叢集

1. 使用 SCP 將 jar 封裝複製到您的 HDInsight 叢集。 將 USERNAME 取代為用於您的叢集的 SSH 使用者。 將 CLUSTERNAME 取代為 HDInsight 叢集的名稱：

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    如果您針對 SSH 帳戶使用密碼，系統會提示您輸入密碼。 如果您搭配帳戶使用 SSH 金鑰，可能需要使用 `-i` 參數來指定金鑰檔的路徑。 例如，`scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`。

    > [AZURE.NOTE] 如果您的用戶端是 Windows 工作站，您可能沒有安裝 SCP 命令。 我們建議 PSCP，您可以從下載 [PuTTY 下載頁面下載](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

    此命令會將檔案複製到叢集上 SSH 使用者的主目錄中。

1. 完成上傳檔案之後，使用 SSH 以連接到 HDInsight 叢集。 取代 **USERNAME** 您的 SSH 登入名稱。 取代 **CLUSTERNAME** 與您的 HDInsight 叢集名稱 ︰

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] 如果您使用 SSH 帳戶的密碼，系統會提示您輸入的密碼。 如果您搭配帳戶使用 SSH 金鑰，可能需要使用 `-i` 參數來指定金鑰檔的路徑。 下列範例會從 `~/.ssh/id_rsa` 載入私密金鑰：
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    如果您要使用 PuTTY，請輸入 `CLUSTERNAME-ssh.azurehdinsight.net` 中 __主機名稱 （或 IP 位址）__ 欄位，，然後按一下 __開啟__ 連線。 系統會提示您輸入 SSH 帳戶名稱。

    > [AZURE.NOTE] 如果您使用 SSH 帳戶的密碼，系統會提示您輸入的密碼。 如果您搭配帳戶使用 SSH 金鑰，可能需要使用下列步驟來選取金鑰：
    >
    > 1. 在 **類別**, ，展開 **連接**, ，展開 **SSH**, ，然後選取 **Auth**。
    > 2. 按一下 [ **瀏覽** ，然後選取包含您的私密金鑰的.ppk 檔案。
    > 3. 按一下 [ __開啟__ 連線。

2. 使用下列命令以啟動拓撲：

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    這將會啟動拓撲並為它們提供易記名稱「讀取器」和「寫入器」。

3. 等待一兩分鐘的時間以允許從事件中樞寫入和讀取拓撲，然後使用下列命令來確認 EventHubReader 正在將資料儲存至 HDInsight 儲存體：

        hadoop fs -ls /devicedata

    這應該會傳回類似下列的檔案清單：

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE] 某些檔案可能會顯示大小為 0，因為它們都由 EventHubReader，但是資料不儲存這些尚未。

    您可以使用下列命令檢視這些檔案的內容：

        hadoop fs -text /devicedata/*.txt

    此命令會傳回類似以下的資料：

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    第一個資料行包含裝置識別碼值，第二個資料行是裝置值。

4. 使用下列命令以停止拓撲：

        storm kill reader
        storm kill writer

###如果使用以 Windows 為基礎的叢集

1. 開啟瀏覽器來 https://CLUSTERNAME.azurehdinsight.net。 出現提示時，輸入 HDInsight 叢集的系統管理員認證。 您會進入 Storm 儀表板。

2. 使用 __Jar 檔案__ 下拉式清單中，瀏覽並選取 EventHubExample-1.0-SNAPSHOT.jar 檔案從您的建置環境。

3. 如 __類別名稱__, ，輸入 `com.mirosoft.example.EventHubWriter`。

4. 如 __其他參數__, ，輸入 `writer`。 最後，按一下 [ __提交__ 上傳 jar 和啟動 EventHubWriter 拓樸。

5. 一旦啟動拓撲，使用表單來啟動 EventHubReader：

    * __Jar 檔案__︰ 選取 EventHubExample-1.0-SNAPSHOT.jar 先前上傳
    * __類別名稱__︰ 輸入 `com.microsoft.example.EventHubReader`
    * __其他參數__︰ 輸入 `reader`

    按一下 [提交] 以啟動 EventHubReader 拓撲。

6. 等候幾分鐘，讓產生的事件和儲存，然後以 Azure 儲存體，然後選取拓撲 __查詢主控台__ ] 索引標籤頂端的 __Storm 儀表板__ 頁面。

7. 在 __查詢主控台__, ，請選取 __Hive 編輯器__ 和取代預設的 `select * from hivesampletable` 取代為下列 ︰

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasb:///devicedata/';
        select * from devicedata limit 10;

    按一下 [ __選取__ 來執行查詢。 這會從 EventHubReader 寫入 Azure 儲存體 (WASB) 的資料傳回 10 個資料列。 一旦查詢完成，您應該會看到類似以下的資料：

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. 選取 __Storm 儀表板__ 在頁面頂端，然後選取 __Storm UI__。 從 __Storm UI__, ，選取的連結 __讀取器__ 拓撲，然後使用 __Kill__ ] 按鈕以停止拓撲。 重複的程序 __寫入器__ 拓樸。



### 檢查點

EventHubSpout 會定期將其狀態設定檢查點到 Zookeeper 節點，這會儲存目前從佇列讀取之訊息的位移。 如此可允許元件在下列狀況中，從儲存的位移處開始接收訊息：

* 元件執行個體失敗，且已重新啟動。

* 您可藉由加入或移除節點來增大或壓縮叢集。

* 拓撲遭終止並重新啟動 **同名**。

####在以 Windows 為基礎的 HDInsight 叢集上

您可以匯出及匯入保存的檢查點到 WASB (您的 HDInsight 叢集所使用的 Azure 儲存體)。若要這樣做的指令碼位於 Storm on HDInsight 叢集的 **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**。

>[AZURE.NOTE] 在路徑中的版本號碼可能不同，因為 Storm 叢集上所安裝的版本可能會在未來變更。

此目錄中的指令碼有：

* **stormmeta_import.cmd**︰ 從叢集預設儲存體容器將所有 Storm 中繼資料匯都入到 Zookeeper。

* **stormmeta_export.cmd**︰ 將所有 Storm 中繼資料從 Zookeeper 都匯出到叢集預設儲存體容器。

* **stormmeta_delete.cmd**︰ 從 Zookeeper 刪除所有 Storm 中繼資料。

匯出匯入可讓您在需要刪除叢集，但又想要在讓新的叢集回到線上時從中樞的目前位移繼續處理時，保存檢查點資料。

> [AZURE.NOTE] 由於資料已保存到預設儲存體容器，新的叢集 **必須** 使用與先前叢集相同的儲存體帳戶和容器。

##疑難排解

如果您未看到檔案儲存到 /devicedata 位置 (使用 `hadoop fs -ls /devicedata` 命令或在 [查詢主控台] 使用 Hive 命令)，使用 Storm UI 來尋找拓撲傳回的任何錯誤。

如需使用 Storm UI 的詳細資訊，請參閱下列主題：

* 如果您使用 __linux__ Storm on HDInsight 叢集，請參閱 [部署和管理以 Linux 為基礎的 HDInsight 上 Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

* 如果您使用 __windows__ Storm on HDInsight 叢集，請參閱 [部署和管理 Windows 為基礎的 HDInsight 上的 Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

##後續步驟

* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)


