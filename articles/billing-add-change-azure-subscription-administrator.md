---
title: Azure 관리자 역할을 추가하거나 변경하는 방법 | Microsoft Docs
description: Azure 공동 관리자, 서비스 관리자 및 계정 관리자를 추가 또는 변경하는 방법에 대해 설명합니다.
services: ''
documentationcenter: ''
author: genlin
manager: msmbaldwin
editor: ''
tags: billing

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: genli

---
# Azure 관리자 역할을 추가 또는 변경하는 방법
Microsoft Azure에는 세 종류의 관리자 역할이 있습니다.

| 관리 역할 | 제한 | 설명 |
| --- | --- | --- |
| 계정 관리자(AA) |Azure 계정당 1개 |Azure 구독을 등록했거나 구입했고, [계정 센터](https://account.windowsazure.com/Home/Index)에 액세스하여 다양한 관리 작업을 수행할 권한이 부여된 사용자입니다. 이러한 관리 작업에는 구독 만들기, 구독 취소, 구독에 대한 대금 청구 변경 및 서비스 관리자 변경이 포함됩니다. |
| 서비스 관리자(SA) |Azure 구독당 1개 |이 역할은 [Azure 포털](https://portal.azure.com)에서 서비스를 관리할 권한이 있습니다. 기본적으로 새 구독의 경우 계정 관리자가 서비스 관리자이기도 합니다. |
| [Azure 클래식 포털](https://manage.windowsazure.com)에서 공동 관리자(CA) |구독당 200 |서비스 관리자와 동일한 액세스 권한이 있지만 Azure 디렉터리에 대한 구독의 연결을 변경할 수는 없는 역할입니다. |

> [!NOTE]
> Azure Active Directory RBAC(역할 기반 액세스 제어)를 사용하면 사용자를 여러 역할에 추가할 수 있습니다. 자세한 내용은 [Azure Active Directory 역할 기반 액세스 제어](active-directory/role-based-access-control-configure.md)를 참조하세요.
> 
> [!NOTE]
> 이 문서에서 언제든지 도움말이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
> 
> 

## 구독에 대한 관리자를 추가하는 방법
**Azure 포털**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 허브 메뉴에서 **구독** > *관리자가 액세스할 구독*을 선택합니다.
   
    ![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)
3. 구독 블레이드에서 **설정**> **사용자**를 선택합니다.
   
    ![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. 사용자 블레이드에서 **추가**>**역할 선택** > **소유자**를 선택합니다.
   
    ![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)
   
    **참고**
   
   * 소유자 역할에는 공동 관리자로 동일한 액세스 권한이 있습니다. 이 역할은 [Azure 계정 센터](https://account.windowsazure.com/subscriptions)에 대해 액세스 권한을 보유하지 않습니다.
   * [Azure 포털](https://portal.azure.com)을 통해 추가한 소유자는 [Azure 클래식 포털](https://manage.windowsazure.com)에서 서비스를 관리할 수 없습니다.
5. 소유자로 추가하려는 사용자의 메일 주소를 입력하고 사용자를 클릭한 후 **선택**을 클릭합니다.
   
    ![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Azure 클래식 포털**

1. [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.
2. 탐색 창에서 **설정**> **관리자**> **추가**를 선택합니다. </br>
   
    ![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. 공동 관리자로 추가하려는 사람의 메일 주소를 입력한 다음 공동 관리자가 액세스하기를 원하는 구독을 선택합니다.</br>
   
    ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

다음 메일 주소를 공동 관리자로 추가할 수 있습니다.

* **Microsoft 계정**(이전의 Windows Live ID) </br> Microsoft 계정을 사용하여 Outlook(Hotmail), Skype(MSN), OneDrive, Windows Phone 및 Xbox LIVE와 같은 모든 소비자 지향 Microsoft 제품 및 클라우드 서비스에 로그인할 수 있습니다.
* **조직 계정**</br> 조직 계정은 Azure Active Directory에 만들어지는 계정입니다. 조직 계정 주소는 user@&lt;your domain&gt;.onmicrosoft.com 형식과 유사합니다.

### 제한 사항
* 구독은 각각 Azure AD 디렉터리(기본 디렉터리라고도 함)와 연결됩니다. 구독이 연결된 기본 디렉터리를 찾으려면 [Azure 클래식 포털](https://manage.windowsazure.com/)로 이동하여 **설정** > **구독**을 선택합니다. 구독 ID를 확인하여 기본 디렉터리를 찾습니다.
* Microsoft 계정으로 로그인하는 경우 다른 Microsoft 계정 또는 기본 디렉터리 내의 사용자를 공동 관리자로 추가할 수 있습니다.
* 조직 계정으로 로그인하는 경우 해당 조직의 다른 조직 계정을 공동 관리자로 추가할 수 있습니다. 예를 들어 abby@contoso.com은 bob@contoso.com을 서비스 관리자 또는 공동 관리자로 추가할 수 있지만 john@noncontoso.com이 기본 디렉터리의 사용자가 아닌 한 john@notcontoso.com은 추가할 수 없습니다. 조직 계정을 사용하여 로그인한 사용자는 Microsoft 계정 사용자를 서비스 관리자 또는 공동 관리자로 계속해서 추가할 수 있습니다.
* 이제 조직 계정으로 Azure에 로그인할 수 있으므로 서비스 관리자 및 공동 관리자 계정 요구 사항이 다음과 같이 변경되었습니다.
  
  | 로그인 방법 | 기본 디렉터리 내에서 Microsoft 계정 또는 사용자를 CA 또는 SA로 추가하나요? | 동일한 조직의 조직 계정을 CA 또는 SA로 추가하나요? | 다른 조직의 조직 계정을 CA 또는 SA로 추가하나요? |
  | --- | --- | --- | --- |
  |  Microsoft 계정 |예 |아니요 |아니요 |
  |  조직 계정 |예 |예 |아니요 |

## 구독에 대한 서비스 관리자를 변경하는 방법
계정 관리자만 구독에 대한 서비스 관리자를 변경할 수 있습니다.

1. [Azure 계정 센터](https://account.windowsazure.com/subscriptions)에 계정 관리자를 사용하여 로그온합니다.
2. 변경하려는 구독을 선택합니다.
3. 오른쪽에서 **구독 세부 사항 편집**을 클릭합니다. </br>
   
    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. **서비스 관리자** 상자에서 새 서비스 관리자의 메일 주소를 입력합니다. </br>
   
    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## 계정 관리자를 변경하는 방법
Azure 계정의 소유권을 다른 계정에 양도하려면 [Azure 구독의 소유권 양도](billing-subscription-transfer.md)를 참조하세요.

## 다음 단계
* Microsoft Azure에서 리소스 액세스를 제어하는 방법에 대해 자세히 알아보려면 [Azure의 리소스 액세스 이해](active-directory/active-directory-understanding-resource-access.md)를 참조하세요.
* Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](active-directory/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.
* Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](active-directory/active-directory-assign-admin-roles.md)을 참조하세요.

> [!NOTE]
> 계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
> 
> 

<!---HONumber=AcomDC_0824_2016-->