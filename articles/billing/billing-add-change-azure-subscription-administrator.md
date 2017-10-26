---
title: "Azure 관리자 구독 역할 추가 또는 변경 | Microsoft Docs"
description: "Azure 공동 관리자, 서비스 관리자 및 계정 관리자를 추가 또는 변경하는 방법에 대해 설명합니다."
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2017
ms.author: genli
ms.openlocfilehash: 178d7fbc17464f7c9a4bc891453d0999e4ba62c3
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2017
---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>구독 또는 서비스를 관리하는 Azure 관리자 역할 추가 또는 변경

구독에서 사용되는 Azure 구독을 관리하거나 Azure 서비스를 관리하는 Azure 관리자를 변경할 수 있습니다. Azure 청구 정보를 보고 구독을 관리하려면 계정 센터에 계정 관리자로 로그인해야 합니다. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Azure Portal에서 구독에 대한 RBAC 소유자 관리자 추가 

Azure Portal에서 누군가를 구독의 관리자로 추가하려면 그 사용자에게 [RBAC](../active-directory/role-based-access-control-configure.md) 소유자 역할을 부여하는 것이 좋습니다. 소유자 역할은 할당한 구독에서 리소스를 관리할 수 있으며 다른 구독에 대한 액세스 권한이 없습니다. [Azure Portal](https://portal.azure.com)을 통해 추가한 소유자는 [Azure 클래식 포털](https://manage.windowsazure.com)에서 리소스를 관리할 수 없습니다.

1. [Azure Portal의 구독 보기](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에 로그인합니다.
1. 관리자가 액세스할 구독을 선택합니다.
1. 메뉴에서 **액세스 제어(IAM)**를 선택합니다.
1. **추가** > **역할** > **소유자**를 선택합니다. 소유자로 추가하려는 사용자의 메일 주소를 입력하고 사용자를 선택한 후 **저장**을 선택합니다.

    ![선택한 소유자 역할을 보여 주는 스크린샷](./media/billing-add-change-azure-subscription-administrator/add-role.png)

## <a name="add-or-change-co-administrator"></a>공동 관리자 추가 또는 변경

소유자만 공동 관리자로 추가될 수 있습니다. 참가자 및 독자 같은 역할이 있는 다른 사용자는 공동 관리자로 추가될 수 없습니다.

1. 아직 하지 않는 경우 위의 지침에 따라 소유자로 사용자를 추가합니다.
2. 방금 추가한 소유자 사용자를 **마우스 오른쪽 단추로 클릭**한 다음 **공동 관리자로 추가**를 선택합니다. **공동 관리자로 추가** 옵션이 표시되지 않는 경우 페이지를 새로 고치거나 다른 인터넷 브라우저를 사용해봅니다. 

     ![공동 관리자를 추가하는 스크린샷](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >[Azure 클래식 포털](https://manage.windowsazure.com/)에서 Azure 서비스를 관리해야 하는 경우 "소유자" 계정을 공동 관리자로 추가해야 합니다.

    공동 관리자 권한을 제거하려면 "공동 관리자" 사용자를 **마우스 오른쪽 단추로 클릭**한 다음 **공동 관리자 제거**를 선택합니다.

    ![공동 관리자를 제거하는 스크린샷](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Azure 구독에 대한 서비스 관리자 변경

계정 관리자만 구독에 대한 서비스 관리자를 변경할 수 있습니다. 기본적으로 등록할 때 서비스 관리자는 계정 관리자와 동일합니다.

1. [서비스 관리자 변경에 대한 제한](#limits)을 확인하여 시나리오가 지원되는지 확인합니다.
1. [계정 센터](https://account.windowsazure.com/subscriptions)에 계정 관리자로 로그인합니다.
1. 구독을 선택합니다.
1. 오른쪽에서 **구독 세부 사항 편집**을 선택합니다.

    ![계정 센터에서 구독 편집 단추를 보여 주는 스크린샷](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. **서비스 관리자** 상자에서 새 서비스 관리자의 메일 주소를 입력합니다.

    ![서비스 관리자 전자 메일을 변경하는 상자를 보여 주는 스크린샷](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>서비스 관리자 변경에 대한 제한 사항

* 각 구독은 Azure AD 디렉터리와 연결됩니다. 구독이 연결된 디렉터리를 찾으려면 [Azure 클래식 포털](https://manage.windowsazure.com/)로 이동하여 **설정** > **구독**을 선택합니다. 구독 ID를 확인하여 디렉터리를 찾습니다.
* 회사 또는 학교 계정으로 로그인하는 경우 해당 조직의 다른 계정을 서비스 관리자로 추가할 수 있습니다. 예를 들어 abby@contoso.com은 bob@contoso.com을 서비스 관리자로 추가할 수 있지만 john@notcontoso.com이 contoso.com 디렉터리에 있지 않으면 john@notcontoso.com은 추가할 수 없습니다. 회사 또는 학교 계정을 사용하여 로그인한 사용자는 Microsoft 계정 사용자를 서비스 관리자로 계속해서 추가할 수 있습니다.

  | 로그인 방법 | SA로 Microsoft 계정 사용자 추가? | SA로 동일한 조직에 회사 또는 학교 계정 추가? | SA로 다른 조직에 회사 또는 학교 계정 추가? |
  | --- | --- | --- | --- |
  |  Microsoft 계정 |예 |아니요 |아니요 |
  |  회사 또는 학교 계정 |예 |예 |아니요 |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Azure 구독에 대한 계정 관리자 변경

구독의 계정 관리자를 변경하려면 [다른 계정에 Azure 구독의 소유권 이전](billing-subscription-transfer.md)을 참조하세요.

<a name="check-the-account-administrator-of-the-subscription"></a>

**계정 관리자가 누구인지 확실하지 않은 경우?** 다음 단계를 수행하세요.

1. [Azure Portal의 구독 보기](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에 로그인합니다.
1. 확인하려는 구독을 선택한 다음 **설정**에서 확인합니다.
1. **속성**을 선택합니다. 구독의 계정 관리자는 **계정 관리자** 상자에 표시됩니다.  

## <a name="types-of-azure-admin-accounts"></a>Azure 관리자 계정 유형

 계정 관리자, 서비스 관리자 및 공동 관리자가 Microsoft Azure에서 세 가지 유형의 관리자 역할입니다. 다음 표에서는 이러한 세 가지 관리 역할의 차이점을 설명합니다.

| 관리 역할 | 제한 | 설명 |
| --- | --- | --- |
| 계정 관리자(AA) |Azure 계정당 1개 |Azure 구독을 등록했거나 구입했고, [계정 센터](https://account.azure.com/Subscriptions) 에 액세스하여 다양한 관리 작업을 수행할 권한이 부여된 사용자입니다. 이러한 관리 작업에는 구독 만들기, 구독 취소, 구독에 대한 대금 청구 변경 및 서비스 관리자 변경이 포함됩니다. |
| 서비스 관리자(SA) |Azure 구독당 1개 |이 역할은 [Azure 포털](https://portal.azure.com)에서 서비스를 관리할 권한이 있습니다. 기본적으로 새 구독의 경우 계정 관리자가 서비스 관리자이기도 합니다. |
| [Azure 클래식 포털](https://manage.windowsazure.com) |구독당 200 |서비스 관리자와 동일한 액세스 권한이 있지만 Azure 디렉터리에 대한 구독의 연결을 변경할 수는 없는 역할입니다. |

Azure Active Directory RBAC(역할 기반 액세스 제어)를 사용하면 사용자를 여러 역할에 추가할 수 있습니다. 자세한 내용은 [Azure Active Directory 역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md)를 참조하세요.


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>리소스 액세스 제어 및 Active Directory에 대해 자세히 알아보기

* Microsoft Azure에서 리소스 액세스를 제어하는 방법에 대해 자세히 알아보려면 [Azure의 리소스 액세스 이해](../active-directory/active-directory-understanding-resource-access.md)를 참조하세요.
* Azure Active Directory에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](../active-directory/active-directory-how-subscriptions-associated-directory.md) 및 [Azure Active Directory에서 관리자 역할 할당](../active-directory/active-directory-assign-admin-roles.md)을 참조하세요.

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요.

다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
