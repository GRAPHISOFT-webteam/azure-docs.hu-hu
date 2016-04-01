<properties
    pageTitle="針對以 Linux 為基礎的 HDInsight 開發 Java MapReduce 程式 | Microsoft Azure"
    description="了解如何開發 Java MapReduce 程式，並將這些程式部署到以 Linux 為基礎的 HDInsight。"
    services="hdinsight"
    editor="cgronlun"
    manager="paulettm"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>

# 在 HDInsight 上開發 Hadoop 的 Java MapReduce 程式

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-maven-mapreduce-selector.md)]

本文件會逐步指示您使用 Apache Maven 建立 MapReduce 應用程式，然後在以 Linux 為基礎的 HDInsight 中 Hadoop 叢集上部署並執行該應用程式。 如需 HDInsight 叢集上使用 Windows 為基礎的 Hadoop，請參閱 [HDInsight (Windows) 上的 Hadoop 開發 Java MapReduce 程式](hdinsight-develop-deploy-java-mapreduce.md)

##<a name="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 或更新版本 （或同等權限，例如 OpenJDK）

- [Apache Maven](http://maven.apache.org/)

- **Azure 訂用帳戶**

- **Azure CLI**︰ 如需詳細資訊，請參閱 [安裝和設定 Azure CLI](../xplat-cli-install.md)

##設定環境變數

當您安裝 Java 和 JDK 時可能會設定下列環境變數。 不過，您應該檢查它們是否存在，以及它們是否包含您系統的正確值。

* **JAVA_HOME** -應該指向已安裝 Java 執行階段環境 (JRE) 的目錄。 例如，在 OS X、Unix 或 Linux 系統上，它的值應該類似 `/usr/lib/jvm/java-7-oracle`。 在 Windows 中，它的值應該類似 `c:\Program Files (x86)\Java\jre1.7`

* **路徑** -應該包含下列路徑 ︰

    * **JAVA_HOME** （或對等的路徑）

    * **JAVA_HOME\bin** （或對等的路徑）

    * 已安裝 Maven 的目錄

##建立新的 Maven 專案

1. 從終端機工作階段，或開發環境的命令列中，將目錄變更為您想要儲存此專案的位置。

3. 使用 __mvn__ 命令和 Maven 一起安裝來產生專案的樣板。

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    這會在目前目錄中，建立新的目錄，與所指定的名稱 __artifactID__ 參數 (**wordcountjava** 在此範例中。)此目錄將包含下列項目：

    * __pom.xml__ - [專案物件模型 (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) ，其中包含用來建置專案的資訊和組態詳細資料。

    * __src__ -包含的目錄 __main/java/org/apache/hadoop/examples__ 目錄中，您將在此撰寫應用程式。

3. 刪除 __src/test/java/org/apache/hadoop/examples/apptest.java__ 檔案，因為它不會在此範例中使用。

##新增相依性

1. 編輯 __pom.xml__ 檔案，並加入下列內 `<dependencies>` 區段 ︰

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    這向 Maven 表示專案需要將程式庫 (列於內 & lt; artifactId\ >) 具有特定版本 (列於內 （& s) lt; 版本 >)。 編譯時，將會從預設 Maven 儲存機制下載此版本。 您可以使用 [Maven 儲存機制搜尋](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) 檢視詳細資訊。

    `<scope>provided</scope>` 會告訴 Maven 這些相依性不應該和應用程式一起封裝，因為 HDInsight 叢集會在執行階段提供這些相依性。

2. 將下列內容加入 __pom.xml__ 檔案。 這必須在檔案中的 `<project>...</project>` 標籤內；例如，在 `</dependencies>` 和 `</project>` 之間。

        <build>
          <plugins>
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
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    第一個外掛程式會設定 [Maven Shade 外掛程式](http://maven.apache.org/plugins/maven-shade-plugin/), ，用來建置 uberjar （有時稱為 fatjar），其中包含應用程式所需的相依性。 這麼做也可以防止 jar 封裝中具有重複的授權，以免在某些系統中造成問題。

    第二個外掛程式會設定 Maven 編譯器，以用來將此應用程式所需的 Java 版本設為 HDInsight 叢集所使用的版本。

3. 儲存 __pom.xml__ 檔案。

##建立 MapReduce 應用程式

1. 移至 __wordcountjava/src/main/java/org/apache/hadoop/examples__ 目錄並重新命名 __App.java__  檔案重新命名為 __WordCount.java__。

2. 開啟 __WordCount.java__ 檔案在文字編輯器中，並取代為下列內容 ︰

        package org.apache.hadoop.examples;

        import java.io.IOException;
        import java.util.StringTokenizer;
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.fs.Path;
        import org.apache.hadoop.io.IntWritable;
        import org.apache.hadoop.io.Text;
        import org.apache.hadoop.mapreduce.Job;
        import org.apache.hadoop.mapreduce.Mapper;
        import org.apache.hadoop.mapreduce.Reducer;
        import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
        import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class WordCount {

          public static class TokenizerMapper
               extends Mapper<Object, Text, Text, IntWritable>{

            private final static IntWritable one = new IntWritable(1);
            private Text word = new Text();

            public void map(Object key, Text value, Context context
                            ) throws IOException, InterruptedException {
              StringTokenizer itr = new StringTokenizer(value.toString());
              while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
              }
            }
          }

          public static class IntSumReducer
               extends Reducer<Text,IntWritable,Text,IntWritable> {
            private IntWritable result = new IntWritable();

            public void reduce(Text key, Iterable<IntWritable> values,
                               Context context
                               ) throws IOException, InterruptedException {
              int sum = 0;
              for (IntWritable val : values) {
                sum += val.get();
              }
              result.set(sum);
              context.write(key, result);
            }
          }

          public static void main(String[] args) throws Exception {
            Configuration conf = new Configuration();
            String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
            if (otherArgs.length != 2) {
              System.err.println("Usage: wordcount <in> <out>");
              System.exit(2);
            }
            Job job = new Job(conf, "word count");
            job.setJarByClass(WordCount.class);
            job.setMapperClass(TokenizerMapper.class);
            job.setCombinerClass(IntSumReducer.class);
            job.setReducerClass(IntSumReducer.class);
            job.setOutputKeyClass(Text.class);
            job.setOutputValueClass(IntWritable.class);
            FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
            FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
            System.exit(job.waitForCompletion(true) ? 0 : 1);
          }
        }

    請注意，封裝名稱是 **org.apache.hadoop.examples** 類別名稱是 **WordCount**。 提交 MapReduce 工作時會用到這些名稱。

3. 儲存檔案。

##建置應用程式

1. 若要變更 __wordcountjava__ 目錄中，如果您尚不存在。

2. 使用下列命令來建置含有應用程式的 JAR 檔案：

        mvn clean package

    這會清除任何先前的組建成品、下載任何尚未安裝的相依項目，然後建置並封裝應用程式。

3. 一旦命令完成時， __wordcountjava/target__ 目錄將包含名為 __wordcountjava-1.0-SNAPSHOT.jar__。

    > [AZURE.NOTE]  __Wordcountjava-1.0-SNAPSHOT.jar__ 檔案是 uberjar，其中不只含有 WordCount 工作，還有工作在執行階段所需的相依性。


##<a id="upload"></a>上傳 jar

使用以下命令將 jar 檔案上傳至 HDInsight 前端節點。

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

這樣就會將檔案從本機系統複製到前端節點。

> [AZURE.NOTE] 如果您使用密碼保護 SSH 帳戶時，系統會提示您輸入密碼。 如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑。 例如，`scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`。

##<a name="run"></a>執行 MapReduce 工作

1. 如下列文章所述，使用 SSH 連接至 HDInsight：

    - [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 在 SSH 工作階段中，使用以下命令執行 MapReduce 應用程式：

        hadoop jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/wordcountout

    這會使用 WordCount MapReduce 應用程式來計算 davinci.txt 檔案中的字數，並將結果儲存至 __wasb: / 範例/資料/wordcountout__。 輸入檔和輸出都會儲存至叢集的預設儲存體中。

3. 工作完成後，請使用下列命令來檢視結果：

        hadoop fs -cat wasb:///example/data/wordcountout/*

    您應該會收到一份單字和計數的清單，其中含有的值類似如下：

        zeal    1
        zelus   1
        zenith  2

##<a id="nextsteps"></a>接續步驟

在本文件中，您已學到如何開發 Java MapReduce 工作。 請參閱下列文件，了解其他的 HDInsight 使用方式。

- [搭配 HDInsight 使用 Hive][hdinsight-use-hive]
- [搭配 HDInsight 使用 Pig][hdinsight-use-pig]
- [將 HDInsight 與 MapReduce 搭配使用](hdinsight-use-mapreduce.md)

如需詳細資訊，請參閱 [Java 開發人員中心](http://azure.microsoft.com/develop/java/)。

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md



[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx



