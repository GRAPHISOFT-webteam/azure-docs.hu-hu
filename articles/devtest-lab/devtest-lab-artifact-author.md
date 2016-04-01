    <properties 
    pageTitle="Create custom artifacts for your DevTest Lab VM | Microsoft Azure"
    description="Learn how to author your own artifacts for use with DevTest Labs"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/01/2015"
    ms.author="tarcher"/>

#為您的研發/測試實驗室 VM 建立自訂構件

**成品** 用來部署和佈建 VM 之後，設定您的應用程式。 構件包含構件定義檔和其他儲存於 Git 儲存機制之資料夾中的指令碼檔案。 構件定義檔是由 JSON 和可用來指定您想要在 VM 上安裝的運算式所組成。 例如，您可以定義構件名稱、要執行的命令，以及命令執行時使其可供使用的參數。 您可以依照名稱來參考構件定義檔中的其他指令碼檔案。

##構件定義檔格式
下列範例顯示組成定義檔基本結構的區段。

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| 元素名稱 | 必要？ | 說明
| ------------ | --------- | -----------
| $schema      | 否        | JSON 結構描述檔案的位置，此檔案可協助測試定義檔的有效性。
| title        | 是       | 實驗室中顯示的構件名稱。
| 說明  | 是       | 實驗室中顯示的構件說明。
| iconUri      | 否        | 實驗室中顯示的圖示 URI。
| targetOsType | 是       | 將安裝構件的 VM 作業系統。 支援的選項為：Windows 和 Linux。
| 參數   | 否        | 在電腦上執行構件安裝命令時所提供的值。 這有助於自訂您的構件。
| runCommand   | 是       | 在 VM 上執行的構件安裝命令。

###構件參數

在定義檔的參數區段中，您會指定使用者可在安裝構件時輸入的值。 您可以在構件安裝命令中參考這些值。

您將利用下列結構來定義參數。

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| 元素名稱 | 必要？ | 說明
| ------------ | --------- | -----------
| 類型         | 是       | 參數值類型。 請參閱下列允許的類型清單：
| 顯示名稱為 [是]       | 為實驗室中的使用者顯示的參數名稱。
| 說明  | 是       | 在實驗室中顯示的參數說明。

允許的類型為：

- string  - 任何有效的 JSON 字串
- int - 任何有效的 JSON 整數
- bool - 任何有效的 JSON 布林值
- array - 任何有效的 JSON 陣列

##構件運算式和函式

您可以使用運算式和函式來建構構件安裝命令。
運算式是以方括號 ([ 與 ]) 括住，並會在安裝構件後加以評估。 運算式可出現在 JSON 字串值中的任何一處，並一律傳回另一個 JSON 值。 如果您必須使用開頭為括號 [ 的常數字串，您必須使用兩個括號 [[。
通常您會使用運算式搭配函式來建構值。 正如同在 JavaScript 中，函式呼叫的格式為 functionName(arg1,arg2,arg3)。

下列清單顯示常見的函式。

- parameters(parameterName) - 傳回在執行構件命令時所提供的參數值。
- concat （arg1，arg2，arg3，...）-結合多個字串值。 此函數可以接受任意數目的引數。

下列範例示範如何使用運算式和函式來建構值。

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

##建立自訂構件

遵循下列步驟來建立自訂的構件：

1. 安裝 JSON 編輯器 - 您必須具備 JSON 編輯器，才能使用構件定義檔。 我們建議使用 [Visual Studio Code](https://code.visualstudio.com/), ，這是適用於 Windows、 Linux 及 OS X。

1. 取得 Azure DevTest 實驗室小組所建立的範例 artifactfile.json-請查看成品我們 [GitHub 儲存機制](https://github.com/Azure/azure-devtestlab) ，我們建立了豐富的程式庫的成品，將協助您建立自己的成品。 下載構件定義檔，並對它進行變更以建立您自己的構件。

1. 利用 IntelliSense - 運用 IntelliSense 來查看可用於建構構件定義檔的有效元素。 您也可以看到適用於元素值的不同選項。 例如，IntelliSense 會顯示您的 Windows 或 Linux 的兩個選擇編輯時 **targetOsType** 項目。

1. 將構件儲存於 Git 儲存機制中
    1. 針對每個構件建立個別的目錄，目錄名稱必須與構件名稱相同。
    1. 將構件定義檔 (artifactfile.json) 儲存於您建立的目錄中。
    1. 儲存從構件安裝命令參考的指令碼。

    構件資料夾的可能外觀範例如下：

    ![構件 Git 儲存機制範例](./media/devtest-lab-artifact-author/git-repo.png)

1. 新增的成品儲存機制的實驗室-請參閱的文章， [Git 成品儲存機制加入至 DevTest 實驗室](devtest-lab-add-artifact-repo.md)。

## 後續步驟

了解如何 [Git 成品儲存機制加入至 DevTest 實驗室](devtest-lab-add-artifact-repo.md)。


