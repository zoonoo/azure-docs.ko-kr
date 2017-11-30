---
title: "Azure AD 디렉터리에 기존 Azure 구독을 추가하는 방법 | Microsoft Docs"
description: "Azure AD 디렉터리에 기존 구독을 추가하는 방법"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 67df6d893c0770b9210fc73e96865d5c6396796c
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Azure Active Directory에 Azure 구독을 추가하는 방법
이 문서에서는 Azure 구독과 Azure Active Directory(Azure AD) 간의 관계와 같은 정보 및 Azure AD 디렉터리에 기존 구독을 추가하는 방법을 다룹니다. Azure 구독은 Azure AD와 트러스트 관계가 있습니다. 즉, 사용자, 서비스 및 장치를 인증하는 디렉터리를 신뢰합니다. 여러 구독에서 동일한 디렉터리를 신뢰할 수 있지만 각 구독은 하나의 디렉터리만 신뢰합니다. 

구독이 디렉터리와 갖는 트러스트 관계는 구독이 Azure의 다른 리소스(웹 사이트, 데이터베이스 등)와 갖는 관계와 다릅니다. 구독이 만료되면 구독과 연결된 다른 리소스에 대한 액세스도 중지됩니다. 하지만 Azure AD 디렉터리는 Azure에 남아 있으며 해당 디렉터리와 다른 구독을 연결하고 새 구독을 사용하여 디렉터리를 관리할 수 있습니다.

모든 사용자는 자신을 인증하는 단일 홈 디렉터리를 가지고 있지만 다른 디렉터리의 게스트가 될 수도 있습니다. Azure AD에서 사용자 계정이 멤버 또는 게스트인 디렉터리만 볼 수 있습니다.

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Azure AD 디렉터리에 기존 구독을 추가하려면
구독이 연결된 현재 디렉터리 및 추가하려는 디렉터리에 존재하는 계정으로 로그인해야 합니다. 

1. 소유권을 이전하려는 구독의 계정 관리자인 계정을 사용하여 [Azure 계정 센터](https://account.azure.com/Subscriptions)에 로그인합니다.
2. 구독 소유자로 선택한 사용자가가 대상 디렉터리에 있는지 확인합니다.
3. **구독 이전**을 클릭합니다.
4. 받는 사람을 지정합니다. 받는 사람은 수락 링크가 포함된 전자 메일을 자동으로 받게 됩니다.
5. 받는 사람은 링크를 클릭하고 지불 정보 입력 등의 지침을 따릅니다. 받는 사람이 성공하면 구독이 이전됩니다. 
6. 구독의 기본 디렉터리는 해당 사용자가 있는 디렉터리로 변경됩니다.


## <a name="next-steps"></a>다음 단계
* Azure 구독의 관리자를 변경하는 방법을 자세히 알아보려면 [다른 계정에 Azure 구독의 소유권 이전](../billing/billing-subscription-transfer.md)을 참조하세요.
* Microsoft Azure에서 리소스 액세스를 제어하는 방법에 대해 자세히 알아보려면 [Azure의 리소스 액세스 이해](active-directory-understanding-resource-access.md)
* Azure AD에서 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
