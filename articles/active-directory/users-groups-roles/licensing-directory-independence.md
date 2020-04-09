---
title: 여러 테넌트 상호 작용의 특성 - Azure AD | 마이크로 소프트 문서
description: Azure Active Directory 테넌을 완전히 독립적인 조직으로 이해
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878122"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>여러 Azure Active Directory 조직이 상호 작용하는 방식 이해

Azure Active Directory(Azure AD)에서 각 테넌트는 완전히 독립적인 조직입니다. 조직 간의 이러한 독립성에는 리소스 독립성, 관리 독립성 및 동기화 독립성등이 포함됩니다. 조직 간에는 부모-자식 관계가 없습니다.

## <a name="resource-independence"></a>리소스 독립성

* 한 조직에서 Azure AD 리소스를 만들거나 삭제하는 경우 외부 사용자를 제외한 다른 조직의 리소스에는 영향을 주지 않습니다.
* 도메인 이름 중 하나를 한 조직에 등록하는 경우 다른 조직에서 사용할 수 없습니다.

## <a name="administrative-independence"></a>관리 독립성

조직 'Contoso'의 비관리 사용자가 테스트 조직 '테스트'를 만드는 경우 다음을 수행합니다.

* 기본적으로 조직을 만드는 사용자는 해당 새 조직의 외부 사용자로 추가되고 해당 조직에서 전역 관리자 역할을 할당합니다.
* 조직 'Contoso'의 관리자는 '테스트'의 관리자가 특별히 이러한 권한을 부여하지 않는 한 조직 '테스트'에 대한 직접적인 관리 권한이 없습니다. 그러나 'Contoso'의 관리자는 '테스트'를 만든 사용자 계정을 제어하는 경우 조직 '테스트'에 대한 액세스를 제어할 수 있습니다.
* 한 조직에서 사용자에 대한 Azure AD 역할을 추가하거나 제거하는 경우 변경 사항은 다른 Azure AD 조직에서 사용자에게 할당된 역할에 영향을 주지 않습니다.

## <a name="synchronization-independence"></a>동기화 독립성

각 Azure AD 조직을 독립적으로 구성하여 다음 중 하나의 인스턴스에서 데이터를 동기화할 수 있습니다.

* 데이터를 단일 AD 포리스트와 동기화하는 Azure AD Connect 도구
* 데이터를 하나 이상의 온-프레미스 포리스트 및/또는 Azure AD 이외의 데이터 원본과 동기화하는 Forefront Identity Manager용 Azure Active Directory 커넥터

## <a name="add-an-azure-ad-organization"></a>Azure AD 조직 추가

Azure 포털에 Azure AD 조직을 추가하려면 Azure AD 글로벌 관리자인 계정으로 [Azure 포털에](https://portal.azure.com) 로그인하고 **새**를 선택합니다.

> [!NOTE]
> 다른 Azure 리소스와 달리 Azure AD 조직은 Azure 구독의 하위 리소스가 아닙니다. Azure 구독이 취소되거나 만료된 경우에도 Azure PowerShell, Microsoft 그래프 API 또는 Microsoft 365 관리 센터를 사용하여 Azure AD 조직의 데이터에 계속 액세스할 수 있습니다. [다른 구독을 조직에 연결할](../fundamentals/active-directory-how-subscriptions-associated-directory.md)수도 있습니다.
>

## <a name="next-steps"></a>다음 단계

Azure AD 라이선스 고려 사항 및 모범 사례는 [Azure Active Directory 라이선스란 무엇입니까?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
