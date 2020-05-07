---
title: 다중 테 넌 트 상호 작용의 특징-Azure AD | Microsoft Docs
description: Azure Active Directory 조직의 데이터 독립성 이해
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbe7b0211775e59504443d30fa253cfa14af13b1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582747"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>여러 Azure Active Directory 조직에서 상호 작용 하는 방법 이해

Azure AD (Azure Active Directory)에서 각 Azure AD 조직은 완전히 독립적입니다. 관리 하는 다른 Azure AD 조직과 논리적으로 독립적인 피어입니다. 조직 간 이러한 독립성에는 리소스 독립성, 관리 독립성 및 동기화 독립성이 포함 됩니다. 조직 간에는 부모-자식 관계가 없습니다.

## <a name="resource-independence"></a>리소스 독립성

* 한 조직에서 Azure AD 리소스를 만들거나 삭제 하는 경우 외부 사용자를 부분적으로 제외 하 고 다른 조직의 리소스에는 영향을 주지 않습니다.
* 한 조직에서 도메인 이름 중 하나를 등록 하는 경우 다른 조직에서 사용할 수 없습니다.

## <a name="administrative-independence"></a>관리 독립성

' Contoso ' 조직의 관리자가 아닌 사용자가 ' Test ' 테스트 조직을 만든 경우 다음을 수행 합니다.

* 기본적으로 조직을 만드는 사용자는 해당 조직에서 외부 사용자로 추가 되 고 해당 조직에서 전역 관리자 역할이 할당 됩니다.
* ' Contoso ' 조직의 관리자는 ' 테스트 ' 관리자가 구체적으로 이러한 권한을 부여 하지 않는 한 조직 ' 테스트 '에 대 한 직접적인 관리 권한이 없습니다. 그러나 ' Contoso ' 관리자는 ' 테스트 '를 만든 사용자 계정을 제어 하는 경우 ' 테스트 ' 조직에 대 한 액세스를 제어할 수 있습니다.
* 한 조직에서 사용자에 대 한 Azure AD 역할을 추가 하거나 제거 하는 경우 해당 사용자가 다른 Azure AD 조직에서 할당 된 역할에는 변경 내용이 적용 되지 않습니다.

## <a name="synchronization-independence"></a>동기화 독립성

각 Azure AD 조직을 독립적으로 구성 하 여 다음 중 하나의 단일 인스턴스에서 데이터를 동기화 할 수 있습니다.

* 데이터를 단일 AD 포리스트와 동기화하는 Azure AD Connect 도구
* 데이터를 하나 이상의 온-프레미스 포리스트 및/또는 Azure AD 이외의 데이터 원본과 동기화하는 Forefront Identity Manager용 Azure Active Directory 커넥터

## <a name="add-an-azure-ad-organization"></a>Azure AD 조직 추가

Azure Portal에서 Azure AD 조직을 추가 하려면 Azure AD 전역 관리자 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **새로 만들기**를 선택 합니다.

> [!NOTE]
> 다른 Azure 리소스와 달리 Azure AD 조직은 Azure 구독의 자식 리소스가 아닙니다. Azure 구독이 취소 되거나 만료 된 경우에도 Azure PowerShell, Microsoft Graph API 또는 Microsoft 365 관리 센터를 사용 하 여 Azure AD 조직의 데이터에 액세스할 수 있습니다. [다른 구독을 조직과 연결할](../fundamentals/active-directory-how-subscriptions-associated-directory.md)수도 있습니다.
>

## <a name="next-steps"></a>다음 단계

Azure AD 라이선스 고려 사항 및 모범 사례는 [Azure Active Directory 라이선스 란?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)을 참조 하세요.
