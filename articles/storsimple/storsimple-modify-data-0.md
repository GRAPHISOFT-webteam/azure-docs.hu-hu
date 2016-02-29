<properties 
   pageTitle="修改 StorSimple 裝置上的 DATA 0 設定 | Microsoft Azure"
   description="了解如何使用 Windows PowerShell for StorSimple 重新設定 StorSimple 裝置上的 DATA 0 網路介面。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# 修改 StorSimple 裝置上的 DATA 0 網路介面設定

## 概觀

Microsoft Azure StorSimple 裝置有 6 個網路介面，從 DATA 0 至 DATA 5。 DATA 0 介面一律透過 Windows PowerShell 介面或序列主控台設定，且自動以雲端啟用。 DATA 0 介面會在初始部署 StorSimple 裝置期間透過安裝精靈第一次設定。 當裝置處於操作模式時，您可能需要重新進行 DATA 0 設定。 本教學課程提供兩種修改 DATA 0 網路設定的方法，皆需透過 Windows PowerShell for StorSimple 進行。

閱讀本教學課程之後，您將能夠：

- 透過安裝精靈修改 DATA 0 網路設定
- 透過 `Set-HcsNetInterface` cmdlet 修改 DATA 0 網路設定


## 透過安裝精靈修改 DATA 0 網路設定
您可以連接至 StorSimple 裝置的 Windows PowerShell 介面並啟動安裝精靈工作階段以重新設定 DATA 0 網路設定。 執行下列步驟以修改 DATA 0 設定：

#### 透過安裝精靈修改 DATA 0 網路設定

1. 在序列主控台功能表中，選擇選項 1 **完整存取權登入**。 當系統提示您提供 **裝置系統管理員密碼**。 預設密碼為 `Password1`。

2. 在命令提示字元中，輸入：


    `Invoke-HcsSetupWizard`

3. 安裝精靈將會出現以協助您設定裝置的 DATA 0 介面。 提供 IP 位址、閘道器和網路遮罩的新值。

> [AZURE.NOTE] 固定的控制器 Ip 必須透過重新設定 **設定** StorSimple 裝置在 Azure 傳統入口網站的頁面。 如需詳細資訊，請移至 [修改網路介面](storsimple-modify-device-config.md#modify-network-interfaces)。


## 透過 Set-HcsNetInterface cmdlet 修改 DATA 0 網路設定
若要重新設定 DATA 0 網路介面是透過使用的替代方式 `Set-HcsNetInterface` 指令程式。 cmdlet 是從 StorSimple 裝置的 Windows PowerShell 介面執行。 執行下列步驟以修改 DATA 0 設定： 

#### 透過 Set-HcsNetInterface cmdlet 修改 DATA 0 網路設定

1. 在序列主控台功能表中，選擇選項 1 **完整存取權登入**。 出現提示時，提供裝置系統管理員密碼。 預設密碼為 `Password1`。

2. 在命令提示字元中，輸入：

    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
    
    在角括弧內鍵入下列 DATA 0 的值：
                                            
    - IPv4 位址
    
    - IPv4 閘道
    
    - IPv4 子網路遮罩
    
    - 控制器 0 的固定的 IPv4 位址

    - 控制器 1 的固定的 IPv4 位址

## 後續步驟

- 若要設定 DATA 0 以外的網路介面，您可以使用 [Azure 傳統入口網站中的 [設定] 頁面](storsimple-modify-device-config.md)。 
- 如果您遇到任何問題，設定您的網路介面時，請參閱 [部署問題進行疑難排解](storsimple-troubleshoot-deployment.md)。


