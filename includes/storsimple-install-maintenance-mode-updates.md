<!--author=SharS last changed: 9/17/15-->

#### 透過 Windows PowerShell for StorSimple 安裝維護模式更新

1. 如果您尚未這麼做，請存取裝置序列主控台並選取選項 1 **完整存取權登入**。 

2. 輸入密碼。 預設密碼是 **Password1**。

3. 在命令提示字元中，輸入：

     `Get-HcsUpdateAvailability` 
    
4. 系統將通知您是否有可用的更新，以及更新是否為破壞性或非破壞性更新。 若要套用干擾性更新，您需要使裝置進入維護模式。 請參閱 [步驟 2 ︰ 進入維護模式](storsimple-update-device.md#step2) 如需相關指示。

5. 當裝置處於維護模式時，在命令提示字元中輸入：`Start-HcsUpdate`

6. 系統將提示您進行確認。 當您確認更新之後，它們將安裝於您目前正在存取的控制站上。 安裝更新之後，將重新啟動控制站。 

7. 監視更新的狀態。 輸入：

    `Get-HcsUpdateStatus`
    
    如果 `RunInProgress` 是 `True`，表示更新仍在進行中。 如果 `RunInProgress` 是 `False`，表示已完成更新。  

7. 當目前的控制站上已安裝更新並重新啟動後，請連接到另一個控制站，然後執行步驟 1 到 6。

8. 更新這兩個控制站之後，結束維護模式。 請參閱 [步驟 4 ︰ 結束維護模式](storsimple-update-device.md#step4) 如需相關指示。


