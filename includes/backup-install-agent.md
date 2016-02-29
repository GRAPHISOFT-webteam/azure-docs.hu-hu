## 下載、安裝和註冊 Azure 備份代理程式

建立 Azure 備份保存庫之後，您的每部 Windows 電腦 (Windows Server、Windows 用戶端、System Center Data Protection Manager 伺服器，或 Azure 備份伺服器電腦) 上應該安裝代理程式，以便讓您將資料和應用程式備份至 Azure。

1. 登入 [管理入口網站](https://manage.windowsazure.com/)

2. 按一下 [ **復原服務**, ，然後選取您想要向伺服器註冊備份保存庫。 備份保存庫的 [快速啟動] 頁面隨即出現。

    ![快速入門](./media/backup-install-agent/quickstart.png)

3. 在 [快速入門] 頁面中，按一下 [ **適用於 Windows Server 或 System Center Data Protection Manager 或 Windows 用戶端** 底下 **下載代理程式**。 按一下 [ **儲存** 將它複製到本機電腦。

    ![儲存代理程式](./media/backup-install-agent/agent.png)

4. 代理程式安裝之後，按兩下 MARSAgentInstaller.exe 來啟動 Azure 備份代理程式的安裝。 選擇安裝資料夾和代理程式所需要的暫存資料夾。 指定的快取位置必須至少包含備份資料大小 5%的可用空間。

5.  如果您使用 proxy 伺服器在連線至網際網路， **Proxy 設定** 畫面上，輸入 proxy 伺服器詳細資料。 若您使用已驗證的 Proxy，請在此畫面中輸入使用者名稱和密碼的詳細資料。

6.  Azure 備份代理程式會安裝 .NET Framework 4.5 和 Windows PowerShell (若尚未安裝) 來完成安裝。

7.  代理程式安裝之後，按一下 [ **繼續註冊** ] 按鈕以繼續與工作流程。

    ![註冊](./media/backup-install-agent/register.png)

8. 在保存庫認證畫面中，瀏覽並選取先前已下載的保存庫認證檔。

    ![保存庫認證](./media/backup-install-agent/vc.png)

    保存庫認證檔僅於 48 小時內有效 (從入口網站下載後起算)。 如果您在此畫面中遇到任何錯誤 (例如「提供的保存庫認證檔已過期」)，請登入 Azure 入口網站，並再次下載保存庫認證檔。

    請確定保存庫認證檔位於可透過設定應用程式存取的位置。 如果您遇到存取權相關的錯誤，請將保存庫認證檔複製至此電腦中的暫存位置並重試作業。

    如果您遇到無效的保存庫認證錯誤 (例如「提供的保存庫認證無效」)，檔案可能損毀或沒有復原服務所關聯的最新認證。 從入口網站下載新的保存庫認證檔後重試作業。 如果使用者在此錯誤通常出現 **下載保存庫認證** 中連續快速地在 Azure 入口網站的選項。 在此情況下，僅第二個保存庫認證檔為有效。

9. 在 **加密設定** ] 畫面中，您可以產生複雜密碼或提供複雜密碼 (最少 16 個字元)。 請記得將複雜密碼存放在安全的位置。

    ![加密](./media/backup-install-agent/encryption.png)

    > [AZURE.WARNING] 如果遺失或忘記; 複雜密碼Microsoft 無法協助您復原備份資料。 加密複雜密碼為使用者所有，Microsoft 將不會看到使用者所使用的複雜密碼。 請將檔案儲存在安全的位置，在復原作業期間需要該檔案。

10. 一旦您按一下 **完成** ] 按鈕，電腦即成功註冊至保存庫，您現在已準備好開始備份至 Microsoft Azure。

11. 使用 Microsoft Azure 備份單機版時，您就可以修改註冊工作流程進行期間指定上的 [設定 **變更內容** Azure 備份 mmc 嵌入式管理單元中的選項。

    ![變更屬性](./media/backup-install-agent/change.png)

    或者，使用資料保護管理員時，您可以修改註冊工作流程進行期間指定，即可設定 **設定** 選項選取 **線上** 下 **管理** ] 索引標籤。

    ![設定 Azure 備份](./media/backup-install-agent/configure.png)

