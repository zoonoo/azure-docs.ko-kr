---
title: Azure Active Directory B2B 모범 사례 및 권장 사항
description: Azure Active Directory에서 B2B(기업 간) 게스트 사용자 액세스에 대한 모범 사례 및 권장 사항을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5721ef606b6ea916f5a00031c58f5e2adeb0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050859"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Active Directory B2B 모범 사례
이 문서에는 Azure Active Directory(Azure AD)의 B2B(비즈니스 간) 공동 작업을 위한 권장 사항 및 모범 사례가 포함되어 있습니다.

   > [!IMPORTANT]
   > **2021년 3월 31일부터**Microsoft는 B2B 공동 작업 시나리오에 대해 관리되지 않는 Azure AD 계정 및 테넌드를 만들어 초대 사용의 상환을 더 이상 지원하지 않습니다. 준비에, 우리는 고객이 [이메일 일회성 암호 인증을](one-time-passcode.md)선택하는 것이 좋습니다. 이 공개 미리 보기 기능에 대한 피드백을 환영하며 더 많은 공동 작업 방법을 만들 수 있게 되어 기쁩니다.

## <a name="b2b-recommendations"></a>B2B 권장 사항
| 권장 | 주석 |
| --- | --- |
| 최적의 로그인 환경을 위해 ID 공급자와 페더레이트 | 가능하면 ID 공급자와 직접 페더레이트하여 초대된 사용자가 MSA(Microsoft 계정) 또는 Azure AD 계정을 만들지 않고도 공유 앱 및 리소스에 로그인할 수 있도록 합니다. [Google 페더레이션 기능을](google-federation.md) 사용하여 B2B 게스트 사용자가 Google 계정으로 로그인할 수 있도록 허용할 수 있습니다. 또는 [직접 페더레이션(미리 보기) 기능을](direct-federation.md) 사용하여 IDP(IDP)가 SAML 2.0 또는 WS-Fed 프로토콜을 지원하는 조직과 직접 페더레이션을 설정할 수 있습니다. |
| 다른 방법으로 인증할 수 없는 B2B 게스트의 경우 이메일 일회용 암호(미리 보기) 기능을 사용합니다. | [전자 메일 일회용 암호(미리 보기)](one-time-passcode.md) 기능은 Azure AD, MSA(Microsoft 계정) 또는 Google 페더레이션과 같은 다른 수단을 통해 인증할 수 없는 경우 B2B 게스트 사용자를 인증합니다. 게스트 사용자는 초대를 사용하거나 공유 리소스에 액세스할 때 메일 주소로 전송되는 임시 코드를 요청할 수 있습니다. 그런 다음, 이 코드를 입력하여 로그인을 계속합니다. |
| 로그인 페이지에 회사 브랜딩 추가 | B2B 게스트 사용자에게 보다 직관적인 로그인 페이지를 사용자 지정할 수 있습니다. [로그인할 회사 브랜딩 및 액세스 패널 페이지를 추가하는](../fundamentals/customize-branding.md)방법을 알아보십시오. |
| B2B 게스트 사용자 사용 경험에 개인 정보 보호 정책을 추가합니다. | 초대된 사용자가 개인 정보 보호 약관에 동의해야 계속하려면 조직의 개인 정보 취급 방침의 URL을 초대 사용 프로세스에 추가할 수 있습니다. [방법: Azure Active Directory에서 조직의 개인 정보 추가](https://aka.ms/adprivacystatement) |
| 대량 초대(미리 보기) 기능을 사용하여 여러 B2B 게스트 사용자를 동시에 초대합니다. | Azure 포털에서 일괄 초대 미리 보기 기능을 사용하여 여러 게스트 사용자를 동시에 조직에 초대합니다. 이 기능을 사용하면 CSV 파일을 업로드하여 B2B 게스트 사용자를 만들고 초대장을 일괄 전송할 수 있습니다. [B2B 사용자를 대량 초대하려면 자습서를](tutorial-bulk-invite.md)참조하십시오. |
| MFA(다단계 인증)에 대한 조건부 액세스 정책 적용 | 파트너 B2B 사용자와 공유하려는 앱에 MFA 정책을 적용하는 것이 좋습니다. 이렇게 하면 파트너 조직에서 MFA를 사용하고 있는지 여부에 관계없이 테넌트의 앱에 MFA가 일관되게 적용됩니다. [B2B 공동 작업 사용자에 대한 조건부 액세스를](conditional-access.md)참조하십시오. |
| 장치 기반 조건부 액세스 정책을 적용하는 경우 제외 목록을 사용하여 B2B 사용자에 대한 액세스를 허용합니다. | 조직에서 장치 기반 조건부 액세스 정책을 사용하도록 설정하면 조직에서 관리하지 않으므로 B2B 게스트 사용자 장치가 차단됩니다. 특정 파트너 사용자가 포함된 제외 목록을 만들어 장치 기반 조건부 액세스 정책에서 제외할 수 있습니다. [B2B 공동 작업 사용자에 대한 조건부 액세스를](conditional-access.md)참조하십시오. |
| B2B 게스트 사용자에게 직접 링크를 제공할 때 테넌트별 URL 사용 | 초대 이메일 대신 게스트에게 앱 또는 포털에 대한 직접 링크를 제공할 수 있습니다. 이 직접 링크는 테넌트에 특정해야 하며, 이는 공유 앱이 있는 테넌트에서 게스트를 인증할 수 있도록 테넌트 ID 또는 확인된 도메인을 포함해야 함을 의미합니다. [게스트 사용자의 사용 환경을](redemption-experience.md)참조하십시오. |
| 앱을 개발할 때 UserType을 사용하여 게스트 사용자 환경을 결정합니다.  | 응용 프로그램을 개발하는 경우 테넌트 사용자 및 게스트 사용자에게 다른 환경을 제공하려면 UserType 속성을 사용합니다. UserType 클레임은 현재 토큰에 포함되지 않습니다. 응용 프로그램은 Microsoft 그래프 API를 사용하여 사용자가 UserType을 얻을 수 있도록 디렉터리를 쿼리해야 합니다. |
| 사용자와 조직간의 관계가 변경되는 *경우에만* UserType 속성을 변경합니다. | PowerShell을 사용하여 사용자에 대한 UserType 속성을 멤버에서 게스트로 변환할 수 있지만 그 반대의 경우도 마찬가지입니다. [B2B 게스트 사용자의 속성을](user-properties.md)참조하십시오.|

## <a name="next-steps"></a>다음 단계

[B2B 공유 관리](delegate-invitations.md)
