---
title: B2B 공동 작업 라이선스 지침-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 공동 작업에는 유료 Azure AD 라이선스가 필요하지 않지만 B2B 게스트 사용자를 위한 유료 기능도 얻을 수 있습니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fa01a6bf522061e54e9622cb9201f81c699a8ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60412440"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 공동 작업 라이선스 지침

Azure AD(Active Directory) B2B(기업 간) 공동 작업 시에는 유료 Azure AD 서비스를 사용하도록 외부 사용자(“게스트 사용자”)를 초대할 수 있습니다. 사용자에게 할당하는 유료 Azure AD 라이선스마다 외부 사용자 허용에 따라 게스트 사용자를 5명까지 초대할 수 있습니다.

B2B 게스트 사용자 라이선싱은 1:5 비율을 기준으로 자동 계산되어 보고됩니다. 현재는 B2B 게스트 사용자 라이선스를 게스트 사용자에게 직접 할당할 수 없습니다.

또한 게스트 사용자는 추가 라이선싱 요구 사항 없이 무료 Azure AD 기능을 사용할 수 있습니다. 유료 Azure AD 라이선스가 없어도 게스트 사용자는 무료 Azure AD 기능에 액세스할 수 있습니다. 

## <a name="examples-calculating-guest-user-licenses"></a>예제: 게스트 사용자 라이선스 계산
유료 Azure AD 서비스에 액세스해야 하는 게스트 사용자의 수를 확인한 후에는 필요한 1:5 비율로 게스트 사용자에게 제공하기에 충분한 Azure AD 유료 라이선스가 있는지 확인합니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.

- Azure AD 앱이나 서비스에 게스트 사용자 100명을 초대하려고 하며 모든 게스트 사용자에게 액세스 관리 및 프로비전 기능을 할당하려고 합니다. 또한 게스트 사용자 중 50명에게는 MFA 및 조건부 액세스를 사용해야 하도록 설정하려고 합니다. 이 조합을 적용하려면 Azure AD Basic 라이선스와 Azure AD Premium P1 라이선스가 각각 10개씩 필요합니다. 게스트 사용자에게 Identity Protection 기능을 사용하려는 경우에는 게스트 사용자에게 적용되는 것과 같은 1:5 비율에 해당하는 Azure AD Premium P2 라이선스가 필요합니다.
- 게스트 사용자 60명을 초대하고 모든 게스트 사용자가 MFA를 사용해야 하도록 설정하려는 경우 Azure AD Premium P1 라이선스가 12개 이상 필요합니다. Azure AD Premium P1 라이선스 보유 직원 수가 10명이라면 1:5 비율에 따라 게스트 사용자를 50명까지 초대할 수 있습니다. 이 경우 게스트 사용자 10명을 더 초대하려면 Premium P1 라이선스 2개를 추가로 구매해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다음 리소스를 확인합니다.

* [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure AD B2B 공동 작업이란?](what-is-b2b.md)
* [Azure Active Directory B2B 공동 작업 자주 묻는 질문(FAQ)](faq.md)
