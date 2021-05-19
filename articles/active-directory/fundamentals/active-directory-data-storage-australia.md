---
title: 오스트레일리아 및 뉴질랜드 고객을 위한 ID 데이터 스토리지 - Azure AD
description: Azure Active Directory가 오스트레일리아 및 뉴질랜드 고객의 ID 관련 데이터를 저장하는 위치에 대해 알아보세요.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bb095e93a3728835e26cbe283f79569c91b7487
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479069"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>오스트레일리아 및 뉴질랜드 고객을 위한 Azure Active Directory의 ID 데이터 스토리지

ID 데이터는 Microsoft 365 및 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공하는 주소에 따라 지리적 위치에서 Azure AD에 의해 저장됩니다. ID 고객 데이터를 저장하는 위치에 대한 정보는 Microsoft 보안 센터의 [데이터를 어디에 배치하나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 사용할 수 있습니다.

> [!NOTE]
> Azure AD와 통합되는 모든 서비스 및 애플리케이션은 ID 고객 데이터에 대한 액세스 권한이 있습니다. ID 고객 데이터가 특정 서비스 및 애플리케이션에서 처리되는 방식과 회사의 데이터 스토리지 요구 사항을 준수하는지 여부를 판단하는 데 사용하는 각 서비스 및 애플리케이션을 평가합니다. Microsoft 서비스의 데이터 상주에 대한 자세한 내용은 Microsoft 보안 센터의 데이터가 어디에 있나요? 섹션을 참조하세요.

오스트레일리아 또는 뉴질랜드에서 주소를 제공한 고객의 경우, Azure AD는 오스트레일리아 데이터 센터 내에서 이러한 서비스에 대해 ID 데이터를 유지합니다. 
- Azure AD 디렉터리 관리 
- 인증

다른 모든 Azure AD 서비스는 고객 데이터를 글로벌 데이터 센터에 저장합니다. 서비스의 데이터 센터를 찾으려면, [Azure Active Directory – 데이터가 어디에 있나요?](https://aka.ms/AADDataMap)를 참조하세요.

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD MFA(Multi-Factor Authentication)

MFA는 글로벌 데이터 센터에 ID 고객 데이터를 저장합니다. 클라우드 기반 Azure AD MFA 및 Azure MFA 서버에서 수집하고 저장하는 사용자 정보에 대한 자세한 내용은 [Azure Multi-Factor Authentication 사용자 데이터 컬렉션](../authentication/concept-mfa-data-residency.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
위에서 설명한 특징 및 기능에 대한 자세한 내용은 다음과 같은 문서를 참조하세요.
- [Multi-Factor Authentication이란?](../authentication/concept-mfa-howitworks.md)
