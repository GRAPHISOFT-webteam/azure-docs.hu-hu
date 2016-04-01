

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **資料** 標籤，然後按一下 **註冊** 資料表。 

    ![](./media/mobile-services-update-registrations-script/mobile-portal-data-tables-registrations.png)

2. 在 **註冊**, ，按一下 [ **指令碼** 索引標籤並選取 **插入**。
   
    ![](./media/mobile-services-update-registrations-script/mobile-insert-script-registrations.png)

    這會顯示函式中發生插入時所叫用 **註冊** 資料表。

3. 下列程式碼，來取代 insert 函數，然後按一下 [ **儲存**:

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registrations');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertChannelIfNotFound });
            function insertChannelIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

   這會註冊新的 insert 指令碼，此指令碼會將註冊資訊儲存在新資料表中。



