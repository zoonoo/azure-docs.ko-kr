---
title: 오스트레일리아 및 뉴질랜드 고객을 위한 id 데이터 저장소-Azure AD
description: Azure Active Directory 오스트레일리아 고객에 대 한 id 관련 데이터를 저장 하는 위치에 대해 알아보세요.
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
ms.openlocfilehash: 850298719d5636e964b0c338d7a2a4cc9bb8aece
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77370299"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory에서 오스트레일리아 및 뉴질랜드 고객에 대 한 id 데이터 저장소

Id 데이터는 Office 365 및 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공 하는 주소에 따라 지리적 위치에 Azure AD에 저장 됩니다. Id 고객 데이터가 저장 되는 위치에 대 한 자세한 내용은 Microsoft 보안 센터의 [어디에 있는 데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 섹션을 사용할 수 있습니다.

> [!NOTE]
> Azure AD와 통합 되는 서비스 및 응용 프로그램은 Id 고객 데이터에 액세스할 수 있습니다. 특정 서비스 및 응용 프로그램에서 Id 고객 데이터를 처리 하는 방법 및 회사의 데이터 저장소 요구 사항을 충족 하는지 여부를 결정 하는 데 사용 하는 각 서비스 및 응용 프로그램을 평가 합니다. Microsoft 서비스의 데이터 상주에 대한 자세한 내용은 Microsoft 보안 센터의 데이터가 어디에 있나요? 섹션을 참조하세요.

오스트레일리아 또는 뉴질랜드에서 주소를 제공한 고객의 경우 Azure AD는 오스트레일리아 데이터 센터 내에서 이러한 서비스에 대 한 id 데이터를 유지 합니다. 
- Azure AD 디렉터리 관리 
- 인증

다른 모든 Azure AD 서비스는 고객 데이터를 글로벌 데이터 센터에 저장 합니다. 서비스의 데이터 센터를 찾으려면 [Azure Active Directory – 데이터가 어디에 있나요?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) 를 참조 하세요.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multi-factor authentication (MFA)

MFA는 글로벌 데이터 센터에 Id 고객 데이터를 저장 합니다. 클라우드 기반 Azure MFA 및 Azure MFA 서버에서 수집 하 고 저장 하는 사용자 정보에 대 한 자세한 내용은 [azure Multi-Factor Authentication 사용자 데이터 컬렉션](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
위에서 설명한 특징 및 기능에 대한 자세한 내용은 다음과 같은 문서를 참조하세요.
- [Multi-Factor Authentication이란?](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
