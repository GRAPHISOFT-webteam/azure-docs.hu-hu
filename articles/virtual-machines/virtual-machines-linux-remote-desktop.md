<properties
    pageTitle="從遠端桌面連接至 Linux VM | Microsoft Azure"
    description="了解如何安裝和設定遠端桌面，以連接至 Microsoft Azure Linux VM。"
    services="virtual-machines"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2015"
    ms.author="mingzhan"/>



# 使用遠端桌面連接至 Microsoft Azure Linux VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


## 概觀

RDP (遠端桌面通訊協定) 是用於 Windows 的專屬通訊協定。 我們要如何使用 RDP 從遠端連接到 Linux VM (虛擬機器)？

本指南會為您提供答案！ 它會協助您在 Microsoft Azure Linux VM 上安裝及設定 xrdp，而您能夠從 Windows 電腦使用遠端桌面來連接它。 在本指南中我們會使用執行 Ubuntu 或 OpenSUSE 的 Linux VM 做為範例。

Xrdp 是開放原始碼 RDP 伺服器，可讓您從 Windows 電腦使用遠端桌面連接 Linux 伺服器。 其執行效果比 VNC (虛擬網路運算) 好多了。 VNC 具有 "JPEG"品質和緩慢行為，而 RDP 既快速又清楚。

> [AZURE.NOTE] 您必須已經有執行 Linux 的 Microsoft Azure VM。 若要建立並設定 Linux VM，請參閱 [Azure Linux VM 教學課程](virtual-machines-linux-tutorial.md)。


## 建立遠端桌面的端點

我們將對本文件中的遠端桌面使用預設端點 3389。 所以將 3389 端點設定為 Linux VM 的遠端桌面，如下所示：


![image](./media/virtual-machines-linux-remote-desktop/no1.png)


如果您不知道如何設定您的 VM 的端點，請參閱 [指引](virtual-machines-set-up-endpoints.md)。


## 安裝 Gnome 桌面

連接到您的 Linux VM，透過 putty，並安裝 `時會桌面`。

針對 Ubuntu，使用：

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop

針對 OpenSUSE，使用︰

    #sudo zypper install gnome-session

## 安裝 xrdp

針對 Ubuntu，使用：

    #sudo apt-get install xrdp

針對 OpenSUSE，使用︰
> [AZURE.NOTE] 更新 OpenSUSE 版本與您使用的版本會為下列命令下, 面是範例命令 `OpenSUSE 13.2`。

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc

## 在開機時啟動 xrdp 並設定 xdrp 服務

針對 OpenSUSE，使用︰

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

對於 Ubuntu，安裝之後會啟動 xrdp，並在開機時自動啟用。

## 如果您使用晚於 Ubuntu 12.04LTS 的 Ubuntu 版本則使用 xfce

由於目前 xrdp 可能不支援從 Ubuntu 版本晚於 Ubuntu 12.04LTS 時會桌面，我們將使用 `xfce` 桌面改。

安裝 `xfce`, ，使用:

    #sudo apt-get install xubuntu-desktop

然後啟用 `xfce`, ，使用:

    #echo xfce4-session >~/.xsession

編輯組態檔 `/etc/xrdp/startwm.sh`, ，使用:

    #sudo vi /etc/xrdp/startwm.sh   

加入這一行 `xfce4 工作階段` 行之前 `/etc/X11/Xsession`。

重新啟動 xrdp 服務，請使用：

    #sudo service xrdp restart

## 從 Windows 電腦連接 Linux VM

在 Windows 機器中，啟動遠端桌面用戶端，輸入您的 Linux VM 的 DNS 名稱，或移至 `儀表板` Azure 傳統入口網站中按一下 [VM 的 `連線` 連線 Linux VM，您會看到登入視窗下方:

![image](./media/virtual-machines-linux-remote-desktop/no2.png)

登入，而且 `使用者` (& s) `密碼` 的 Linux VM，並立即享受您的 Microsoft Azure Linux VM 的遠端桌面!


## 下一步

如需使用 xrdp 的詳細資訊，您可以參考 [這裡](http://www.xrdp.org/)。





