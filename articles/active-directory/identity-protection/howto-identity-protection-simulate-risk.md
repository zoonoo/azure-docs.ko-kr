---
title: Azure AD ID 보호에서 위험 검색 시뮬레이션
description: ID 보호에서 위험 탐지를 시뮬레이션하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886936"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>ID 보호에서 위험 탐지 시뮬레이션

관리자는 다음 항목을 수행하기 위해 해당 환경에서 위험을 시뮬레이션할 수 있습니다.

- 위험 탐지 및 취약점을 시뮬레이션하여 ID 보호 환경에서 데이터를 채웁니다.
- 위험 기반 조건부 액세스 정책을 설정하고 이러한 정책의 영향을 테스트합니다.

이 문서에서는 다음 위험 검색 유형을 시뮬레이션하기 위한 단계를 제공합니다.

- 익명 IP 주소(간편함)
- 익숙하지 않은 로그인 속성(보통)
- 비정형 여행 (어려운)

다른 위험 탐지는 안전한 방식으로 시뮬레이션할 수 없습니다.

각 위험 탐지에 대한 자세한 내용은 이 문서에서 찾을 수 [있습니다.](concept-identity-protection-risks.md)

## <a name="anonymous-ip-address"></a>익명 IP 주소

다음 절차를 완료하려면 다음을 사용해야 합니다.

- [Tor 브라우저](https://www.torproject.org/projects/torbrowser.html.en) - 익명 IP 주소를 시뮬레이션할 수 있습니다. 조직이 Tor 브라우저를 사용하여 제한하는 경우 가상 머신을 사용해야 합니다.
- Azure 다단계 인증에 아직 등록되지 않은 테스트 계정입니다.

**익명 IP에서 로그인을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. [Tor 브라우저](https://www.torproject.org/projects/torbrowser.html.en)를 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)로 이동합니다.   
2. **익명 IP 주소에서 로그인** 보고서에 표시하려는 계정의 자격 증명을 입력합니다.

로그인이 10 ~ 15분 이내에 ID 보호 대시보드에 나타납니다. 

## <a name="unfamiliar-sign-in-properties"></a>일반적이지 않은 로그인 속성

알 수 없는 위치를 시뮬레이트하려면 테스트 계정이 이전에 로그인하지 않은 위치 및 디바이스에서 로그인해야 합니다.

다음 프로시저는 새로 만든 항목을 사용합니다.

- VPN 연결: 새 위치를 시뮬레이트합니다.
- 가상 머신: 새 디바이스를 시뮬레이트합니다.

다음 절차를 완료하려면 다음 항목을 포함하는 사용자 계정을 사용해야 합니다.

- 최소 30일 로그인 기록.
- Azure 다단계 인증을 사용할 수 있습니다.

**익숙하지 않은 위치에서 로그인을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. 테스트 계정으로 로그인할 때 MFA 챌린지를 통과하지 못해 MFA(다단계 인증) 챌린지에 실패합니다.
2. 새 VPN을 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com) 테스트 계정의 자격 증명을 탐색하고 입력합니다.

로그인이 10 ~ 15분 이내에 ID 보호 대시보드에 나타납니다.

## <a name="atypical-travel"></a>비정상적 이동

알고리즘이 기계 학습을 사용하여 익숙한 장치에서 비정형 이동 또는 디렉터리에서 다른 사용자가 사용하는 VPN의 로그인과 같은 가양성을 제거하기 때문에 비정형 이동 조건을 시뮬레이션하는 것은 어렵습니다. 또한 알고리즘은 위험 탐지 생성을 시작하기 전에 사용자의 로그인 기록14일과 로그인 10회가 필요합니다. 복잡한 기계 학습 모델 과 위의 규칙으로 인해 다음 단계가 위험 탐지로 이어지지 않을 가능성이 있습니다. 이 검색을 시뮬레이션하기 위해 여러 Azure AD 계정에 대해 이러한 단계를 복제할 수 있습니다.

**비정형 여행 위험 검색을 시뮬레이션하려면 다음 단계를 수행합니다.**

1. 표준 브라우저를 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)로 이동합니다.  
2. 비정형 여행 위험 검색을 생성하려는 계정의 자격 증명을 입력합니다.
3. 사용자 에이전트를 변경합니다. 개발자 도구(F12)에서 Microsoft Edge의 사용자 에이전트를 변경할 수 있습니다.
4. 사용자의 IP 주소를 변경합니다. VPN, Tor 추가 기능을 사용하거나 다른 데이터 센터에서 Azure에서 새 가상 컴퓨터를 만들어 IP 주소를 변경할 수 있습니다.
5. 이전 로그인 [https://myapps.microsoft.com](https://myapps.microsoft.com) 후 몇 분 이내에 이전과 동일한 자격 증명을 사용하여 로그인합니다.

로그인이 2-4시간 이내에 ID 보호 대시보드에 나타납니다.

## <a name="testing-risk-policies"></a>위험 정책 테스트

이 섹션에서는 사용자 및 문서에서 만든 로그인 위험 정책, [방법: 구성 및 위험 정책 사용 설정에](howto-identity-protection-configure-risk-policies.md)대한 테스트를 위한 단계를 제공합니다.

### <a name="user-risk-policy"></a>사용자 위험 정책

사용자 위험 보안 정책을 테스트하려면 다음 단계를 수행합니다.

1. [Azure 포털로](https://portal.azure.com)이동합니다.
1. Azure **Active 디렉터리** > **보안** > **개요로**이동합니다.
1. **사용자 위험 정책 구성을**선택합니다.
   1. **과제** 중
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자를** 선택하거나 개인 및 **그룹을 선택합니다.**
         1. 선택적으로 정책에서 사용자를 제외하도록 선택할 수 있습니다.
      1. **조건** - **사용자 위험** 마이크로 소프트의 권장 사항은 **높은으로이**옵션을 설정하는 것입니다 .
   1. **언더 컨트롤**
      1. **액세스** - Microsoft의 권장 사항은 **액세스를 허용하고** **암호를 변경해야 하는**것입니다.
   1. **정책** - **적용 끄기**
   1. **저장** - 이 작업은 **개요** 페이지로 돌아갑니다.
1. 예를 들어 위험 검색 중 하나를 몇 번 시뮬레이션하여 테스트 계정의 사용자 위험을 높입니다.
1. 몇 분 간 기다린 다음 사용자에게 위험이 증가했는지 확인합니다. 그렇지 않은 경우 사용자에 대한 더 많은 위험 검색을 시뮬레이션합니다.
1. 위험 정책으로 **돌아가정책 적용을** **켜기** 및 **저장** 정책 변경으로 설정합니다.
1. 이제 위험 수준이 높은 사용자를 사용하여 로그인하여 사용자 위험 기반 조건부 액세스를 테스트할 수 있습니다.

### <a name="sign-in-risk-security-policy"></a>로그인 위험 보안 정책

로그인 위험 정책을 테스트하려면 다음 단계를 수행합니다.

1. [Azure 포털로](https://portal.azure.com)이동합니다.
1. Azure **Active 디렉터리** > **보안** > **개요로**이동합니다.
1. **로그인 위험 정책 구성을 선택합니다.**
   1. **과제** 중
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자를** 선택하거나 개인 및 **그룹을 선택합니다.**
         1. 선택적으로 정책에서 사용자를 제외하도록 선택할 수 있습니다.
      1. **조건** - **로그인 위험** Microsoft의 권장 사항은 이 옵션을 중간 **이상으로**설정하는 것입니다.
   1. **언더 컨트롤**
      1. **액세스** - Microsoft의 권장 사항은 **액세스를 허용하고** **다단계 인증을 요구하는 것입니다.**
   1. **정책** - **적용**
   1. **저장** - 이 작업은 **개요** 페이지로 돌아갑니다.
1. 이제 위험한 세션을 사용하여 로그인하여 로그인 위험 기반 조건부 액세스를 테스트할 수 있습니다(예: Tor 브라우저 사용). 

## <a name="next-steps"></a>다음 단계

- [위험이란 무엇입니까?](concept-identity-protection-risks.md)

- [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory ID 보호](overview-identity-protection.md)
