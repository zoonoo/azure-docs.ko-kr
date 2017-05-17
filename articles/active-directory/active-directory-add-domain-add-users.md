---
title: "Azure Active Directory의 사용자 지정 도메인에 사용자 할당 | Microsoft Docs"
description: "사용자 계정으로 Azure Active Directory의 사용자 지정 도메인을 채우는 방법"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ccfa8d72b51c43e04d924d2b096e3cbc407af043
ms.contentlocale: ko-kr
ms.lasthandoff: 12/29/2016


---
# <a name="assign-users-to-a-custom-domain"></a>사용자 지정 도메인에 사용자 할당
Azure Active Directory에 사용자 지정 도메인을 추가한 후에 인증을 시작할 수 있도록 이 도메인에 대한 사용자 계정을 추가해야 합니다.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>회사 네트워크의 디렉터리에서 동기화된 사용자
온-프레미스 Active Directory와 Azure Active Directory 간의 연결을 이미 설정한 경우 동기화로 계정을 채울 수 있습니다. Azure Active Directory를 온-프레미스 Active Directory와 동기화하는 방법에 대한 자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.

## <a name="users-added-and-managed-in-the-cloud"></a>클라우드에 추가되고 관리되는 사용자
기존 사용자 계정에 대한 도메인을 변경하려면

1. 전역 관리자 또는 사용자 관리자인 계정을 사용하여 Azure 클래식 포털을 엽니다.
2. 디렉터리를 엽니다.
3. **사용자** 탭을 선택합니다.
4. 목록에서 사용자를 선택합니다.
5. 사용자에 대한 도메인을 변경한 다음 **저장**을 선택합니다.

이 작업은 [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 또는 [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)를 사용하여 수행할 수도 있습니다.

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>새 사용자를 만들 때 사용자 지정 도메인 선택
1. 전역 관리자 또는 사용자 관리자인 계정을 사용하여 Azure 클래식 포털을 엽니다.
2. 디렉터리를 엽니다.
3. **사용자** 탭을 선택합니다.
4. 명령 모음에서 **추가**를 선택합니다.
5. 사용자 이름에 추가할 때 도메인 목록에서 사용자 지정 도메인을 선택합니다.
6. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 도메인 이름을 사용하여 사용자의 로그인 환경 간소화](active-directory-add-domain.md)
* [사용자 지정 도메인 이름 관리](active-directory-add-manage-domain-names.md)
* [Azure AD에서 도메인 관리 개념 알아보기](active-directory-add-domain-concepts.md)


