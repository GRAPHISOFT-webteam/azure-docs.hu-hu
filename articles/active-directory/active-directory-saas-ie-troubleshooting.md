<properties
   pageTitle="疑難排解 Internet explorer 的存取面板延伸模組 | Microsoft Azure"
   description="如何使用群組原則針對我的 app 入口網站部署 Internet Explorer 附加元件。"
   services="active-directory"
   documentationCenter=""
   authors="liviodlc"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/28/2015"
   ms.author="liviodlc"/>

#疑難排解 Internet explorer 的存取面板延伸模組

這篇文章可協助您疑難排解下列問題：

- 使用 Internet Explorer 時無法透過「我的 app」入口網站存取您的 app。
- 即使您已經安裝軟體，還是看到「安裝軟體」訊息。

如果您是系統管理員，請參閱 ︰ [如何部署 Internet Explorer 使用群組原則的存取面板延伸模組](active-directory-saas-ie-group-policy.md)

##執行診斷工具

您可以下載並執行「存取面板」診斷工具來診斷存取面板延伸模組的安裝問題：

1. [按一下這裡下載診斷工具。](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. 開啟檔案，然後按 **解壓縮全部** ] 按鈕。

    ![按下 [解壓縮全部]](./media/active-directory-saas-ie-troubleshooting/extract1.png)

3. 然後按下 **擷取** ] 按鈕以繼續。

    ![按下 [解壓縮]](./media/active-directory-saas-ie-troubleshooting/extract2.png)

4. 若要執行此工具，以滑鼠右鍵按一下名為的檔案 **AccessPanelExtensionDiagnosticTool**, ，然後選取 **開啟 > Microsoft Windows 指令碼主機**。

    ![[開啟檔案] > [Microsoft Windows Based Script Host]](./media/active-directory-saas-ie-troubleshooting/open_tool.png)

5. 然後您會看到下列診斷視窗，描述您的安裝可能有哪些錯誤。

    ![一個診斷視窗範例](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)

6. 按一下 「**是**」，讓程式修正所發現的問題。

7. 若要儲存這些變更，關閉所有 Internet Explorer 視窗，然後再開啟 Internet Explorer。<br />如果您仍然無法存取您的應用程式，請嘗試下列步驟。

##檢查存取面板延伸模組是否已啟用

確認 Internet Explorer 中已啟用存取面板延伸模組：

1. 在 Internet Explorer 中，按一下 **齒輪圖示** 視窗右上角。 然後選取 **網際網路選項**。<br />(較舊版本的 Internet Explorer 中可以找到此下 **工具 > 網際網路選項**。

    ![移至 [工具] > [網際網路選項]](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)

2. 按一下 [ **程式** 索引標籤，然後按一下 [ **管理附加元件** ] 按鈕。

    ![按一下 [管理附加元件]](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)

3. 在這個對話方塊中，選取 **存取面板延伸模組** 然後按一下 [ **啟用** ] 按鈕。

    ![按一下 [啟用]](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)

4. 若要儲存這些變更，請關閉所有 Internet Explorer 視窗，然後再次開啟 Internet Explorer。

##啟用 InPrivate 瀏覽的延伸模組

如果您正在使用 InPrivate 瀏覽模式：

1. 在 Internet Explorer 中，按一下 **齒輪圖示** 視窗右上角。 然後選取 **網際網路選項**。<br />(較舊版本的 Internet Explorer 中可以找到此下 **工具 > 網際網路選項**。

    ![一個診斷視窗範例](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)

2. 移至 **隱私權** 索引標籤，然後 **取消核取** 標示的核取方塊 **InPrivate 瀏覽開始時停用工具列和延伸模組**</p>

    ![取消核取 [InPrivate 瀏覽啟動時停用工具列和延伸模組]](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)

3. 若要儲存這些變更，請關閉所有 Internet Explorer 視窗，然後再次開啟 Internet Explorer。

##解除安裝存取面板延伸模組

從您的電腦解除安裝存取面板延伸模組：

1. 在鍵盤上按 **Windows 鍵** 若要開啟 [開始] 功能表。 功能表開啟時，您可以輸入任何內容進行搜尋。 輸入 [控制台]，然後再開啟 **控制台** 出現在搜尋結果中。

    ![搜尋「控制台」](./media/active-directory-saas-ie-troubleshooting/search_sm.png)

2. 在控制台中右上角，變更 **檢視** 選項 **大圖示**。 然後尋找並按一下 **程式和功能** ] 按鈕。

    ![變更檢視以顯示大圖示](./media/active-directory-saas-ie-troubleshooting/control_panel.png)

3. 從清單中選取 **存取面板延伸模組**, ，然後按一下 [ **解除安裝** ] 按鈕。

    ![按一下 [解除安裝]](./media/active-directory-saas-ie-troubleshooting/uninstall.png)

4. 您可以嘗試再安裝一次延伸模組，看看是否已經解決問題。

如果您遇到問題，解除安裝延伸模組，您也可以移除它使用 [It](https://go.microsoft.com/?linkid=9779673) 工具。

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]


