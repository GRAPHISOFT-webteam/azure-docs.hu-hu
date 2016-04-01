<properties
    pageTitle="Azure Active Directory 混合式身分識別設計考量 - 判斷目錄同步處理需求|Microsoft Azure"
    description="識別哪些是企業在內部部署和雲端之間同步處理所有使用者所需的需求。"
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="11/11/2015"
    ms.author="billmath"/>

# 判斷目錄同步處理需求
同步處理的重點是根據使用者的內部部署身分識別，為他們提供雲端中的身分識別。 不論使用者是否將使用同步處理的帳戶來進行驗證或同盟驗證，他們仍然需要在雲端中具備身分識別。  這個身分識別必須定期維護和更新。  更新可以有許多形式，範圍可從標題變更到密碼變更。  

一開始，先評估組織的內部部署身分識別解決方案和使用者需求。 當您定義如何在雲端中建立與維護使用者身分識別的技術需求時，這項評估非常重要。  對於大多數的組織，Active Directory 是內部部署，而且是從中同步處理使用者的內部部署目錄，不過，在某些情況下，這並非事實。  

請確定回答下列問題：


- 您擁有一或多個 AD 樹系，或者完全沒有嗎？
 - 您要進行同步處理的 Azure AD 目錄有多少個？
 
    1. 您會使用篩選嗎？
    2. 您是否規劃了多部 Azure AD Connect 伺服器？
  
- 您目前沒有內部部署的同步處理工具嗎？
  - 如果是，您的使用者是否具有虛擬目錄/身分識別整合？
- 您擁有任何其他您想要同步處理的內部部署目錄 (例如，LDAP 目錄、HR 資料庫等) 嗎？
  - 您將會進行任何 GALSync 嗎？
  - 在您的組織中，UPN 的目前狀態為何？ 
  - 您擁有使用者要對其進行驗證的其他目錄嗎？
  - 貴公司是否使用 Microsoft Exchange？
    - 他們是否規劃擁有混合式交換部署？ 
   
既然您已經了解貴公司的同步處理需求，您現在需要評估使用這些目錄服務的應用程式。 當您定義如何將這些應用程式整合到雲端的技術需求時，這項評估非常重要。 請確定回答下列問題：

- 這些應用程式將移至雲端並使用目錄嗎？
- 有任何需要同步處理到雲端的特殊屬性，讓這些應用程式可成功使用它們嗎？
- 這些應用程式將需要重新撰寫，以充分利用雲端驗證嗎？
- 當使用者使用雲端身分識別來存取這些應用程式時，它們將持續存留在內部部署中嗎？

您也需要判斷目錄同步作業的安全性需求和限制。 當您取得所需的需求清單以便在雲端中建立和維護使用者的身分識別時，這項評估非常重要。 請確定回答下列問題：

- 同步處理伺服器將位於何處？
- 它將會加入網域嗎？
- 伺服器將位於防火牆後面 (例如 DMZ) 限制的網路上嗎？
  - 您能夠開啟所需的防火牆連接埠來支援同步處理嗎？
- 您有任何適用於同步處理伺服器的災害復原計畫嗎？
- 針對您想要同步處理的所有樹系，您是否擁有具備正確權限的帳戶？
 - 如果您的公司並不知道此問題的答案，檢閱文件中的 「 密碼同步化的權限 」 一節 [安裝 Azure Active Directory 同步處理服務](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) 並判斷是否已經具有這些權限的帳戶，或者如果您需要建立一個。
- 如果您擁有多重樹系的同步處理，則同步處理伺服器能夠送達每個樹系嗎？
 
>[AZURE.NOTE]
請確定會記下每個答案，並了解答案背後的原理。 [判斷事件回應需求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) 將會透過可用的選項。 回答這些問題之後，您就能選取最適合業務需求的選項。

## 後續步驟
[判斷多重要素驗證需求](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## 另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-directory-overview.md)

