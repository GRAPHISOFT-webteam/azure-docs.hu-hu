<properties
    pageTitle="使用 Maven 建置 HBase 應用程式，並部署到以 Windows 為基礎的 HDInsight | Microsoft Azure"
    description="了解如何使用 Apache Maven 建置以 Java 為基礎的 Apache HBase 應用程式，然後部署至以 Windows 為基礎的 Azure HDInsight 叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>

#使用 Maven 建置搭配使用 HBase 和 HDInsight (Hadoop) 的 Java 應用程式 (英文)

了解如何建立和建置 [Apache HBase](http://hbase.apache.org/) 使用 Apache Maven 以 java 應用程式。 然後在 Azure HDInsight (Hadoop) 中使用此應用程式。

[Maven](http://maven.apache.org/) 是軟體專案管理和理解工具，可讓您建置軟體、 文件及 Java 專案的報表。 在本文中，您將了解如何用它來建立基本的 Java 應用程式，以便在 Azure HDInsight 叢集上建立、查詢和刪除 HBase 資料表。

##需求

* [Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 或更新版本

* [Maven](http://maven.apache.org/)

* [具有 HBase 的 Azure HDInsight 叢集](hdinsight-hbase-get-started.md#create-hbase-cluster)

##建立專案

1. 從開發環境的命令列中，將目錄變更至您想要建立專案的位置，例如 `cd code\hdinsight`

2. 使用 __mvn__ 命令和 Maven 一起安裝來產生專案的樣板。

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    這在目前目錄中，建立新目錄所指定的名稱與 __artifactID__ 參數 (**hbaseapp** 在此範例中。)此目錄將包含下列項目：

    * __pom.xml__: 專案物件模型 ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) 包含用來建置專案的資訊和組態詳細資料。

    * __src__: 所在的目錄 __main\java\com\microsoft\examples__ 目錄中，您將在此撰寫應用程式。

3. 刪除 __src\test\java\com\microsoft\examples\apptest.java__ 檔案，因為它不會用在此範例中。

##更新專案物件模型

1. 編輯 __pom.xml__ 檔案，並加入下列程式碼 `<dependencies>` 區段:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

    這向 Maven 表示專案需要 __hbase 用戶端__ 版本 __0.98.4-hadoop2__。 編譯時，將會從預設 Maven 儲存機制下載此版本。 您可以使用 [Maven 中央儲存機制搜尋](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) 若要深入了解此相依性。

2. 加入下列程式碼以 __pom.xml__ 檔案。 這必須在檔案中的 `<project>...</project>` 標籤內，例如在 `</dependencies>` 和 `</project>`之間。

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
            <resource>
              <directory>${basedir}/conf</directory>
              <filtering>false</filtering>
              <includes>
                <include>hbase-site.xml</include>
              </includes>
            </resource>
          </resources>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
                        <version>3.3</version>
              <configuration>
                <source>1.6</source>
                <target>1.6</target>
              </configuration>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
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
          </plugins>
        </build>

    這會設定資源 (__conf\hbase-site.xml__,，)，其中包含 HBase 的組態資訊。

    > [AZURE.NOTE] 您也可以設定組態值，透過程式碼。 請參閱中的註解 __CreateTable__ 範例說明如何執行這項操作。

    這也會設定 [Maven 編譯器外掛程式](http://maven.apache.org/plugins/maven-compiler-plugin/) 和 [Maven Shade 外掛程式](http://maven.apache.org/plugins/maven-shade-plugin/)。 Compiler 外掛程式用來編譯拓撲。 Shade 外掛程式用來防止以 Maven 所建置的 JAR 封裝發生授權重複。 使用此項目的理由在於，重複的授權檔會導致 HDInsight 叢集在執行階段發生錯誤。 使用 maven-shade-plugin 搭配 `ApacheLicenseResourceTransformer` 實作可防止此錯誤。

    maven-shade-plugin 也會產生 uber jar (或 fat jar)，其含有應用程式需要的所有相依性。

3. 儲存 __pom.xml__ 檔案。

4. 建立新目錄 __conf__ 中 __hbaseapp__ 目錄。 在 __conf__ 目錄中，建立新的檔名為 __hbase-site.xml__ ，並使用下列做為內容:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
         * Copyright 2010 The Apache Software Foundation
         *
         * Licensed to the Apache Software Foundation (ASF) under one
         * or more contributor license agreements.  See the NOTICE file
         * distributed with this work for additional information
         * regarding copyright ownership.  The ASF licenses this file
         * to you under the Apache License, Version 2.0 (the
         * "License"); you may not use this file except in compliance
         * with the License.  You may obtain a copy of the License at
         *
         *     http://www.apache.org/licenses/LICENSE-2.0
         *
         * Unless required by applicable law or agreed to in writing, software
         * distributed under the License is distributed on an "AS IS" BASIS,
         * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
         * See the License for the specific language governing permissions and
         * limitations under the License.
         */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    此檔案用來載入 HDInsight 叢集的 HBase 組態。

    > [AZURE.NOTE] 這是一個極小的 hbase-site.xml 檔案，並包含 HDInsight 叢集最低限度的設定。 如需 HDInsight 所使用的 hbase-site.xml 組態檔的完整版本，請參閱 [使用 Azure 入口網站在 HDInsight 中管理 Hadoop 叢集](hdinsight-administer-use-management-portal.md#rdp)。 hbase-site.xml 檔案位於 C:\apps\dist\hbase-&lt;version number>-hadoop2\conf 目錄中。 檔案路徑的版本號碼部分會隨著 HBase 在叢集上更新而變更。

3. 儲存 __hbase-site.xml__ 檔案。

##建立應用程式

1. 移至 __hbaseapp\src\main\java\com\microsoft\examples__ 目錄，並將 app.java 檔案至 __CreateTable.java__。

2. 開啟 __CreateTable.java__ 檔案，並將現有的內容取代為下列:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            //   in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    這是 __CreateTable__ 類別，將會建立名為的資料表 __人__ ，並填入一些預先定義的使用者。

3. 儲存 __CreateTable.java__ 檔案。

4. 在 __hbaseapp\src\main\java\com\microsoft\examples__ 目錄中，建立新的檔名為 __SearchByEmail.java__。 使用下列項目做為此檔案的內容：

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

     __SearchByEmail__ 類別也可用來依電子郵件地址查詢資料列。 因為此類別使用規則運算式篩選器，您可以在使用此類別時提供字串或規則運算式。

5. 儲存 __SearchByEmail.java__ 檔案。

6. 在 __hbaseapp\src\main\hava\com\microsoft\examples__ 目錄中，建立新的檔名為 __DeleteTable.java__。 使用下列項目做為此檔案的內容：

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    這個類別來清理此範例中，停用並卸除所建立的資料表是 __CreateTable__ 類別。

7. 儲存 __DeleteTable.java__ 檔案。

##建置和封裝應用程式

1. 開啟命令提示字元，並將目錄變更至 __hbaseapp__ 目錄。

2. 使用下列命令來建置含有應用程式的 JAR：

        mvn clean package

    這會清除任何先前的組建成品、下載任何尚未安裝的相依性，然後建置並封裝應用程式。

3. 命令完成時， __hbaseapp\target__ 目錄將包含名為 __hbaseapp-1.0-SNAPSHOT.jar__。

    > [AZURE.NOTE]  __Hbaseapp-1.0-SNAPSHOT.jar__ 檔案是一個 uber jar (有時稱為 fat jar)，內含執行應用程式所需的所有相依性。

##上傳 JAR 檔案並啟動工作

> [AZURE.NOTE] 中所述，有很多種檔案上傳至 HDInsight 叢集， [hdinsight 的資料上傳](hdinsight-upload-data.md)。 下列步驟會使用 [PowerShell](../install-configure-powershell.md)。

1. 安裝和設定 Azure PowerShell，建立名為的新檔案之後 __hbase-runner.psm1__。 使用下列項目做為此檔案的內容：

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccountName `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccountName `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup `
                | %{ $_.Key1 }
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    此檔案包含兩個模組：

    * __Add-hdinsightfile__ -用來將檔案上傳至 HDInsight

    * __Start-hbaseexample__ -用來執行稍早建立的類別

2. 儲存 __hbase-runner.psm1__ 檔案。

3. 開啟新的 Azure PowerShell 視窗，將目錄變更至 __hbaseapp__ 目錄，然後執行下列命令。

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    將路徑變更的位置 __hbase-runner.psm1__ 稍早建立的檔案。 這會為此 Azure PowerShell 工作階段註冊模組。

2. 使用下列命令來上傳 __hbaseapp-1.0-SNAPSHOT.jar__ 至 HDInsight 叢集。

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    取代 __hdinsightclustername__ 與 HDInsight 叢集的名稱。 命令接著會將上傳 __hbaseapp-1.0-SNAPSHOT.jar__ 至 __範例/jars__ HDInsight 叢集的主要儲存體中的位置。

3. 上傳檔案之後，請使用下列程式碼，建立新資料表 __hbaseapp__:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    取代 __hdinsightclustername__ 與 HDInsight 叢集的名稱。

    此命令會建立名為的新資料表 __人__ HDInsight 叢集中。 此命令不會在主控台視窗中顯示任何輸出。

2. 若要在資料表中搜尋項目，請使用下列命令：

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    取代 __hdinsightclustername__ 與 HDInsight 叢集的名稱。

    此命令會使用 **SearchByEmail** 類別來搜尋的任何資料列位置 __contactinformation__ 資料行系列和 __電子郵件__ 資料行，包含字串 __contoso.com__。 您應該會得到下列結果：

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

    使用 __fabrikam.com__ 的 `-emailRegex` 值會傳回之使用者的 __fabrikam.com__ 電子郵件欄位中。 因為此搜尋以規則運算式為基礎的篩選器來實作，您也可以輸入規則運算式，例如 __^ r__, ，其將傳回的項目電子郵件以字母 'r' 開頭的位置。

##刪除資料表

當您完成範例時，使用下列命令，從 Azure PowerShell 工作階段刪除 __人__ 此範例中使用的資料表:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

取代 __hdinsightclustername__ 與 HDInsight 叢集的名稱。

##疑難排解

###使用 Start-HBaseExample 時沒有結果或傳回非預期的結果

請使用 `-showErr` 參數，以檢視執行工作時所產生的標準錯誤 (STDERR)。

