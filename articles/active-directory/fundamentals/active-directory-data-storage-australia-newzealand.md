---
title: 호주 고객을 위한 ID 데이터 저장소 - Azure AD
description: Azure Active Directory에서 호주 고객을 위해 ID 관련 데이터를 저장하는 위치에 대해 알아봅니다.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460537"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory의 오스트레일리아 및 뉴질랜드 고객을 위한 ID 데이터 저장소

ID 데이터는 Office 365 및 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공한 주소를 기반으로 지리적 위치에 Azure AD에 의해 저장됩니다. ID 고객 데이터가 저장되는 위치에 대한 자세한 내용은 Microsoft 신뢰 센터의 [데이터 위치](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 사용할 수 있습니다.

> [!NOTE]
> Azure AD와 통합되는 서비스 및 응용 프로그램은 ID 고객 데이터에 액세스할 수 있습니다. 사용하는 각 서비스 및 응용 프로그램을 평가하여 특정 서비스 및 응용 프로그램에서 ID 고객 데이터가 처리되는 방식과 해당 서비스 저장소 요구 사항을 충족하는지 여부를 결정합니다. Microsoft 서비스의 데이터 상주에 대한 자세한 내용은 Microsoft 보안 센터의 데이터가 어디에 있나요? 섹션을 참조하세요.

오스트레일리아 및 뉴질랜드에서 주소를 제공하고 Azure AD 무료 버전을 사용하는 고객의 경우 Azure AD는 호주 데이터 센터 내에서 PII 데이터를 미사용 으로 유지합니다. 

다른 모든 Azure AD 프리미엄 서비스는 글로벌 데이터 센터에 고객 데이터를 저장합니다. 서비스의 데이터 센터를 찾으려면 [Azure Active Directory - 데이터가 어디에 있습니까?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>마이크로소프트 Azure 다단계 인증(MFA)

Azure AD의 MFA 서비스는 미사용 글로벌 데이터 센터에 ID 고객 데이터를 저장합니다. 클라우드 기반 Azure MFA 및 Azure MFA Server에서 수집하고 저장하는 사용자 정보에 대한 자세한 내용은 [Azure 다단계 인증 사용자 데이터 컬렉션을](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)참조하십시오. 고객이 MFA를 사용하는 경우 해당 데이터는 미사용 으로 호주 데이터 센터 외부에 저장됩니다. 

## <a name="next-steps"></a>다음 단계
위에서 설명한 특징 및 기능에 대한 자세한 내용은 다음과 같은 문서를 참조하세요.
- [Multi-Factor Authentication이란?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
