<properties 
    pageTitle="在 Mac 和 Linux 使用 SSH |Microsoft Azure" 
    description="在 Linux 和 Mac 上產生和使用 SSH 金鑰，搭配 Azure 上資源管理員和傳統部署模型。" 
    services="virtual-machines" 
    documentationCenter="" 
    authors="squillace" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2015" 
    ms.author="rasquill"/>

#如何在Azure 的 Linux 和 Mac 使用 SSH

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

本主題描述如何使用 **ssh-keygen** 和 **openssl** 上建立及使用的 Linux 和 Mac **ssh rsa** 格式和 **.pem** 格式來保護通訊安全和 Azure Vm Linux 為基礎的檔案。 Linux 型 Azure 虛擬機器使用的資源管理員部署模型建議您建立新的部署和採用 *ssh rsa* 輸入公開金鑰檔案或字串 (取決於部署用戶端)。  [預覽入口網站](https://portal.azure.com) 目前只接受 **ssh rsa** 格式化字串，無論是基於傳統或資源管理員的部署。

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]
若要建立 Linux Vm 使用安全通訊的 Windows 電腦上的這些類型的檔案以供使用，請參閱 [搭配使用 SSH 金鑰在 Windows 上的](virtual-machines-windows-use-ssh-key.md)。 

## 您需要哪些檔案？

基本 ssh 安裝包含 Azure **ssh rsa** 為 2048 位元的公用和私用金鑰組 (根據預設， **ssh-keygen** 儲存這些檔案以 **~/.ssh/id_rsa** 和 **~/.ssh/id-rsa.pub** 除非變更預設值)，以及 `.pem` 檔案會從產生 **id_rsa** 搭配傳統部署模型的傳統入口網站使用私密金鑰檔案。 

以下是部署案例，以及您在每個案例中使用的檔案類型：

1. **ssh rsa** 金鑰所需的部署使用 [預覽入口網站](https://portal.azure.com), ，無論部署模型。
2. 建立使用 Vm 所需的.pem 檔案 [傳統入口網站](https://manage.windowsazure.com)。 .pem 檔案中使用的傳統部署也支援 [Azure CLI](xplat-cli-install.md)。 

## 建立金鑰與 SSH 搭配使用

Azure 需要 **ssh rsa** 格式化為 2048 位元的金鑰檔或對等的.pem 檔案，取決於您的案例而定。 如果您已經有這類檔案，在建立 Azure VM 時傳遞公開金鑰檔。 

如果您需要建立檔案：

1. 請確定您的實作 **ssh-keygen** 和 **openssl** 是最新狀態。 這會因平台而異。 

    - 適用於 Mac，務必瀏覽 [Apple 產品安全性網站](https://support.apple.com/HT201222) ，如有必要，請選擇適當的更新。
    - 若是 Ubuntu、Debian、Mint 等 Debian 型的 Linux 散發套件：

            sudo apt-get update ssh-keygen
            sudo apt-get update openssl

    - 若是 CentOS、Oracle Linux 等 RPM 型的 Linux 散發套件：

            sudo yum update ssh-keygen
            sudo yum update openssl

    - 若是 SLES 和 OpenSUSE

            sudo zypper update ssh-keygen
            sudo zypper update openssl

2. 使用 **ssh-keygen** 若要建立 2048年位元 RSA 公用和私用金鑰檔案，除非您有特定的位置或特定名稱的檔案中，接受預設位置和名稱 `~/.ssh/id_rsa`。 基本命令是：

        ssh-keygen -t rsa -b 2048 

    一般而言，您 **ssh-keygen** 實作會將註解，通常使用者名稱和主機電腦的名稱。 您可以使用 `-C` 選項指定特定的註解。

3. 從您 `~/.ssh/id_rsa` 檔案建立.pem 檔案，以便您使用傳統的入口網站。 使用 **openssl** ，如下所示:

        openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

    如果您想要從不同的私密金鑰檔案建立.pem 檔案，請修改 `-key` 引數。 

> [AZURE.NOTE] 如果您打算管理服務使用傳統部署模型部署，您可能也要建立 **.cer** 格式檔案上傳至入口網站-雖然這不包括 **ssh** 或連線至 Linux VM，這是本文的主題。 若要建立 Linux 或 Mac 上的檔案，請輸入:
<br />
> openssl.exe x509-outform der-在 myCert.pem-myCert.cer 出

將.pem 檔案轉換成 DER 編碼的 x509 憑證檔案。

## 使用您有的 SSH 金鑰

您可以在所有新工作使用 ssh rsa (`.pub`) 金鑰，尤其是資源管理員部署模型和預覽入口網站；如果您需要使用傳統入口網站，您可能需要從您的金鑰建立 `.pem` 檔案。

## 建立 VM 與您的公開金鑰檔案

建立所需的檔案之後，有許多方法可以建立能使用公開-私密金鑰交換進行安全連線的 VM。 在幾乎所有的情況下，尤其是使用資源管理員部署，請在系統提示您提供 ssh 金鑰檔案路徑或檔案內容字串時，傳遞.pub 檔案。 

### 範例：建立 VM with the id_rsa.pub 檔案

最常見的用法是以命令方式建立 VM 時 - 或上傳範本建立 VM 時。 下列程式碼範例示範在 Azure 中建立新的、安全 Linux VM，做法是將公開檔案名稱 (在此案例中是預設 `~/.ssh/id_rsa` 檔案) 傳遞給 `azure vm create` 命令。 (其他引數已事先建立。)

    azure vm create \
    --nic-name testnic \
    --public-ip-name testpip \
    --vnet-name testvnet \
    --vnet-subnet-name testsubnet \
    --storage-account-name computeteststore 
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --username ops \
    -ssh-publickey-file ~/.ssh/id_rsa \
    testrg testvm westeurope linux

下一個範例示範如何使用 **ssh rsa** 與資源管理員範本和 Azure CLI 建立 Ubuntu VM 的使用者名稱和內容保護格式 `~/.ssh/id_rsa.pub` 做為字串。 (本範例縮短公開金鑰字串以利閱讀。) 

    azure group deployment create \
    --resource-group test-sshtemplate \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
    --name mysshdeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    testnewStorageAccountName: testsshvmtemplate3
    adminUserName: ops
    sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQA+/L+rHIjz+nXTzxApgnP+iKDZco9 user@macbookpro
    dnsNameForPublicIP: testsshvmtemplate
    location: West Europe
    vmName: sshvm
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "mysshdeployment"
    + Waiting for deployment to complete
    data:    DeploymentName     : mysshdeployment
    data:    ResourceGroupName  : test-sshtemplate
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-10-08T00:12:12.2529678Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type    Value

    data:    newStorageAccountName  String  testtestsshvmtemplate3
    data:    adminUserName          String  ops
    data:    sshKeyData             String  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAkek3P6V3EhmD+xP+iKDZco9 user@macbookpro
    data:    dnsNameForPublicIP     String  testsshvmtemplate
    data:    location               String  West Europe
    data:    vmSize                 String  Standard_A2
    data:    vmName                 String  sshvm
    data:    ubuntuOSVersion        String  14.04.2-LTS
    info:    group deployment create command OK


### 範例：以 .pem 檔案建立 VM

接著您可以使用 .pem 檔案搭配傳統入口網站或是搭配傳統部署模式和 `azure vm create`，如以下範例所示：

    azure vm create \
    -l "West US" -n testpemasm \
    -P -t myCert.pem -e 22 \
    testpemasm \
    b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-en-us-30GB \
    ops
    info:    Executing command vm create
    warn:    --vm-size has not been specified. Defaulting to "Small".
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-en-us-30GB
    + Looking up cloud service
    info:    cloud service testpemasm not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Configuring certificate
    + Creating VM
    info:    vm create command OK


## 連接到您的 VM

 **Ssh** 命令會使用使用者名稱登入的電腦以及要連接到位址-，以及許多其他特殊變化的連接埠的網路位址。 (如需詳細資訊 **ssh**, ，首先您可以使用這 [文章安全殼層](https://en.wikipedia.org/wiki/Secure_Shell)) 

資源管理員部署的典型用法看起來如下 (如果您只是指定子網域和部署位置)：

    ssh user@subdomain.westus.cloudapp.azure.com -p 22

或者，如果您要連接至傳統部署雲端服務，您使用的位址可能看起來像這樣：

    ssh user@subdomain.cloudapp.net -p 22

因為位址形式可以變更 - 您永遠可以使用的 IP 位址，也或許使用您指派的自訂網域名稱 - 您將必須探索 Azure VM 的位址。 

### 探索搭配傳統部署之 Azure VM SSH 的位址

您可以使用 `azure vm show` 命令搭配 VM 名稱，來探索用於 VM 與傳統部署模型的位址：

    azure vm show testpemasm
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "testpemasm.cloudapp.net"
    data:    Location "West US"
    data:    VMName "testpemasm"
    data:    IPAddress "100.116.160.154"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Small"
    data:    Image "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-en-us-30GB"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "testpemasm-testpemasm-0-201510102050230517"
    data:    OSDisk mediaLink "https://portalvhds4blttsxgjj1rf.blob.core.windows.net/vhd-store/testpemasm-2747c9c432b043ff.vhd"
    data:    OSDisk sourceImageName "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-en-us-30GB"
    data:    OSDisk operatingSystem "Linux"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "40.83.178.221"
    data:    VirtualIPAddresses 0 name "testpemasmContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 localPort 22
    data:    Network Endpoints 0 name "ssh"
    data:    Network Endpoints 0 port 22
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "40.83.178.221"
    data:    Network Endpoints 0 enableDirectServerReturn false
    info:    vm show command OK

### 探索搭配資源管理員部署之 Azure VM SSH 的位址

    azure vm show testrg testvm
    info:    Executing command vm show
    + Looking up the VM "testvm"
    + Looking up the NIC "testnic"
    + Looking up the public ip "testpip"

請檢查網路設定檔 (Network Profile) 區段：

    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-21-8E-AE
    data:          Provisioning State        :Succeeded
    data:          Name                      :testnic
    data:          Location                  :westeurope
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.115.48.189
    data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
    data:
    data:    Diagnostics Instance View:
    info:    vm show command OK

如果您建立 VM 時沒有使用預設的 SSH 連接埠 22，您可以使用 `azure network nsg show` 命令探索具有輸入規則的連接埠，如下列範例所示：

    azure network nsg show testrg testnsg
    info:    Executing command network nsg show
    + Looking up the network security group "testnsg"
    data:    Id                              : /subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testnsg
    data:    Name                            : testnsg
    data:    Type                            : Microsoft.Network/networkSecurityGroups
    data:    Location                        : westeurope
    data:    Provisioning state              : Succeeded
    data:    Security group rules:
    data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
    data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
    data:    testnsgrule                    *                  *            *               22                Tcp       Inbound    Allow   1000
    data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
    data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
    data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
    data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
    data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
    data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
    info:    network nsg show command OK

### 範例：使用 .pem 金鑰和傳統部署之 SSH 工作階段的輸出

如果您使用從您的 `~/.ssh/id_rsa` 檔案建立之 .pem 檔案建立虛擬機器，可以直接 ssh 到該 VM。 請注意，當您這樣做，憑證交握會使用您在 `~/.ssh/id_rsa` 的私密金鑰。 如下例所示：

    ssh ops@testpemasm.cloudapp.net -p 22
    The authenticity of host 'testpemasm.cloudapp.net (40.83.178.221)' can't be established.
    RSA key fingerprint is dc:bb:e4:cc:59:db:b9:49:dc:71:a3:c8:37:36:fd:62.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'testpemasm.cloudapp.net,40.83.178.221' (RSA) to the list of known hosts.
    Saving password to keychain failed
    Identity added: /Users/rasquill/.ssh/id_rsa (/Users/rasquill/.ssh/id_rsa)
    Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

    * Documentation:  https://help.ubuntu.com/

    System information as of Sat Oct 10 20:53:08 UTC 2015

    System load: 0.52              Memory usage: 5%   Processes:       80
    Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

    Graph this data and manage this system at:
        https://landscape.canonical.com/

    Get cloud support with Ubuntu Advantage Cloud Guest:
        http://www.ubuntu.com/business/services/cloud

    0 packages can be updated.
    0 updates are security updates.

    The programs included with the Ubuntu system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.

    Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
    applicable law.

## 如果您遇到連接問題

您可以讀取在建議 [疑難排解 SSH 連線](virtual-machines-troubleshoot-ssh-connections.md) 看看如果它們有助於解決問題。

## 後續步驟
 
既然您已連接到您的 VM，請務必先更新您所選的散發套件，再繼續使用它。

