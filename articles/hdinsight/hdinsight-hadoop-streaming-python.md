<properties
   pageTitle="使用 HDInsight 開發 Python MapReduce 工作 | Microsoft Azure"
   description="了解如何在以 Linux 為基礎的 HDInsight 叢集上建立和執行 Python MapReduce 工作。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>

#開發適用於 HDInsight 的 Python 串流程式

Hadoop 為 MapReduce 提供一個串流 API，可讓您以 Java 以外的語言撰寫 map 和 reduce 函數。 在本文中，您將學習如何使用 Python 來執行 MapReduce 作業。

> [AZURE.NOTE] 雖然您可以使用這份文件中的 Python 程式碼，以 Windows 為基礎的 HDInsight 叢集，這份文件中的步驟專以 Linux 為基礎的叢集。

這篇文章根據資訊和範例由 Michael Noll 在發佈 [Python 撰寫 Hadoop MapReduce 程式](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/)。

##先決條件

若要完成本文中的步驟，您需要下列項目：

* HDInsight 叢集上的 Linux 型 Hadoop

* 文字編輯器

* 若為 Windows 用戶端，則需要 PuTTY 和 PSCP。 這些公用程式，可從 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY 下載頁面</a>.

##字數統計

在此範例中，您將使用對應器和歸納器實作基本字數計數。 對應器會將句子拆解成個別文字，歸納器則會彙總文字和計數來產生輸出。

下列流程圖說明在對應和歸納階段期間所執行的程序。

![Map Reduce 的插圖](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##為何使用 Python？

Python 是一般用途的高階程式設計語言，可讓您以少於許多其他語言的程式碼行數快速表達概念。 資料科學家最近很喜歡用它做為原型語言，因為它的解譯本質、動態型別和簡潔語法讓它非常適合用於快速開發應用程式。

Python 會安裝在所有 HDInsight 叢集上。

##串流 MapReduce

Hadoop 可讓您指定包含工作所使用的對應和歸納邏輯的檔案。 對應和歸納邏輯的特殊需求如下：

* **輸入**: 對應和縮減元件必須從 STDIN 讀取輸入的資料。

* **輸出**: 對應和縮減元件必須將輸出資料寫入至 STDOUT。

* **資料格式**: 取用和產生的資料必須是索引鍵/值組，以 tab 字元分隔。

Python 可以輕鬆應付這些需求，使用 **sys** 模組從 STDIN 和使用讀取 **列印** 來列印到 STDOUT。 剩下的工作便只需要在機碼和值之間以 Tab (`\t`) 字元格式化資料。

##建立對應器和歸納器

對應器和歸納器是文字檔案，在此情況下 **mapper.py** 和 **reducer.py** (以便更清楚的功用)。 您可以使用自行選擇的編輯器建立這些檔案。

###Mapper.py

建立新的檔名為 **mapper.py** ，並使用下列程式碼做為內容:

    #!/usr/bin/env python

    # Use the sys module
    import sys

    # 'file' in this case is STDIN
    def read_input(file):
        # Split each line into words
        for line in file:
            yield line.split()

    def main(separator='\t'):
        # Read the data using read_input
        data = read_input(sys.stdin)
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

請花一點時間仔細閱讀程式碼，以便了解它的功用。

###Reducer.py

建立新的檔名為 **reducer.py** ，並使用下列程式碼做為內容:

    #!/usr/bin/env python

    # import modules
    from itertools import groupby
    from operator import itemgetter
    import sys

    # 'file' in this case is STDIN
    def read_mapper_output(file, separator='\t'):
        # Go through each line
        for line in file:
            # Strip out the separator character
            yield line.rstrip().split(separator, 1)

    def main(separator='\t'):
        # Read the data using read_mapper_output
        data = read_mapper_output(sys.stdin, separator=separator)
        # Group words and counts into 'group'
        #   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
        for current_word, group in groupby(data, itemgetter(0)):
            try:
                # For each word, pull the count(s) for the word
                #   from 'group' and create a total count
                total_count = sum(int(count) for current_word, count in group)
                # Write to stdout
                print "%s%s%d" % (current_word, separator, total_count)
            except ValueError:
                # Count was not a number, so do nothing
                pass

    if __name__ == "__main__":
        main()

##上傳檔案

同時 **mapper.py** 和 **reducer.py** 必須先在叢集的前端節點上才能加以執行。 最簡單的方式上傳方式是使用 **scp** (**pscp** 如果您使用 Windows 用戶端)。

從相同的目錄中的用戶端 **mapper.py** 和 **reducer.py**, ，使用下列命令。 取代 **username** 取代為 SSH 使用者，以及 **clustername** 與您的叢集名稱。

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

這樣就會將檔案從本機系統複製到前端節點。

> [AZURE.NOTE] 如果您使用密碼保護 SSH 帳戶時，系統會提示您輸入密碼。 如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑，例如 `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`。

##執行 MapReduce

1. 使用 SSH 連線到叢集：

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] 如果您使用密碼保護 SSH 帳戶時，系統會提示您輸入密碼。 如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑，例如 `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`。

2. 使用下列命令啟動 MapReduce 工作。

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasb:///example/data/gutenberg/davinci.txt -output wasb:///example/wordcountout

    此命令有下列幾個部分：

    * **hadoop-streaming.jar**: 執行串流 MapReduce 作業時使用。 它能連結 Hadoop 和您提供的外部 MapReduce 程式碼。

    * **-檔案**: 告訴 Hadoop 此 MapReduce 工作，需要指定的檔案，而且應複製到所有背景工作節點。

    * **-對應程式**: 告訴 Hadoop 来做為對應器的檔案。

    * **-reducer**: 告訴 Hadoop 来做為歸納器的檔案。

    * **-輸入**: 從特定計算的輸入的檔案的文字。

    * **-輸出**: 輸出寫入的目錄。

        > [AZURE.NOTE] 工作將會建立此目錄。

您應該會看到一大堆 **資訊** 工作會啟動，陳述式，最後看到 **對應** 和 **減少** 作業以百分比顯示。

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

工作完成後，您會收到工作的狀態資訊。

##檢視輸出

工作完成後，使用以下命令檢視輸出：

    hadoop fs -text /example/wordcountout/part-00000

這應該會顯示文字及文字出現次數的清單。 以下是輸出資料的範例：

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

##後續步驟

現在您已學會如何搭配 HDInsight 使用串流 MapRedcue 工作，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* [搭配 HDInsight 使用 Hive](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)

