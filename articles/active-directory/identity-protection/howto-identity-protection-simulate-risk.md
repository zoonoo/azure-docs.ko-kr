---
title: Azure AD ID 보호에서 위험 감지 시뮬레이션
description: Id 보호에서 위험 감지를 시뮬레이션 하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbcc2351b7ead3714cfd21aa30b031cec75074a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84463759"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Id 보호에서 위험 감지 시뮬레이션

관리자는 다음 항목을 수행 하기 위해 환경에서 위험을 시뮬레이션할 수 있습니다.

- 위험 검색 및 취약성을 시뮬레이션 하 여 Id 보호 환경에서 데이터를 채웁니다.
- 위험 기반 조건부 액세스 정책을 설정 하 고 이러한 정책의 영향을 테스트 합니다.

이 문서에서는 다음 위험 검색 유형을 시뮬레이트하는 단계를 제공 합니다.

- 익명 IP 주소 (easy)
- 익숙하지 않은 로그인 속성 (보통)
- 불규칙 한 이동 (어려운)

다른 위험 검색은 안전한 방식으로 시뮬레이트할 수 없습니다.

각 위험 검색에 대 한 자세한 내용은 [위험 이란?](concept-identity-protection-risks.md)문서에서 찾을 수 있습니다.

## <a name="anonymous-ip-address"></a>익명 IP 주소

다음 절차를 완료하려면 다음을 사용해야 합니다.

- [Tor 브라우저](https://www.torproject.org/projects/torbrowser.html.en) - 익명 IP 주소를 시뮬레이션할 수 있습니다. 조직이 Tor 브라우저를 사용하여 제한하는 경우 가상 머신을 사용해야 합니다.
- Azure Multi-Factor Authentication에 아직 등록 되지 않은 테스트 계정.

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
- Azure Multi-Factor Authentication 사용 하도록 설정 되었습니다.

**익숙하지 않은 위치에서 로그인을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. 테스트 계정으로 로그인 하는 경우 MFA 챌린지를 전달 하지 않고 MFA (multi-factor authentication) 챌린지를 실패 합니다.
2. 새 VPN을 사용 하 여으로 이동 하 [https://myapps.microsoft.com](https://myapps.microsoft.com) 고 테스트 계정의 자격 증명을 입력 합니다.

로그인이 10 ~ 15분 이내에 ID 보호 대시보드에 나타납니다.

## <a name="atypical-travel"></a>비정상적 이동

알고리즘이 기계 학습을 사용 하 여 친숙 한 장치에서 비정상적인 이동과 같은 가양성을 확인 하거나 디렉터리의 다른 사용자가 사용 하는 Vpn의 로그인을 차단 하므로 비정상적인 이동 상태를 시뮬레이션 하는 것은 어렵습니다. 또한 알고리즘은 위험 검색 생성을 시작 하기 전에 사용자에 대 한 14 일 및 10 개의 로그인 로그인 기록이 필요 합니다. 복잡 한 기계 학습 모델 및 위의 규칙 때문에 다음 단계를 수행 해도 위험 검색이 발생 하지 않을 수 있습니다. 이러한 검색을 시뮬레이션 하기 위해 여러 Azure AD 계정에 대해 이러한 단계를 복제 하는 것이 좋습니다.

**비정상적인 이동 위험 검색을 시뮬레이트하려면 다음 단계를 수행 합니다**.

1. 표준 브라우저를 사용 하 여로 이동 [https://myapps.microsoft.com](https://myapps.microsoft.com) 합니다.  
2. 비정상적인 이동 위험 검색을 생성 하려는 계정의 자격 증명을 입력 합니다.
3. 사용자 에이전트를 변경합니다. F12 (개발자 도구)에서 Microsoft Edge의 사용자 에이전트를 변경할 수 있습니다.
4. 사용자의 IP 주소를 변경합니다. VPN, 다른 추가 기능을 사용 하 여 IP 주소를 변경 하거나 다른 데이터 센터의 Azure에서 새 가상 컴퓨터를 만들 수 있습니다.
5. [https://myapps.microsoft.com](https://myapps.microsoft.com)이전 로그인 후 몇 분 이내에 동일한 자격 증명을 사용 하 여에 로그인 합니다.

로그인이 2-4시간 이내에 ID 보호 대시보드에 나타납니다.

## <a name="testing-risk-policies"></a>위험 정책 테스트

이 섹션에서는 사용자를 테스트 하 고, [방법: 위험 정책 구성 및 설정](howto-identity-protection-configure-risk-policies.md)문서에서 만든 로그인 위험 정책에 대 한 단계를 제공 합니다.

### <a name="user-risk-policy"></a>사용자 위험 정책

사용자 위험 보안 정책을 테스트하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **Azure Active Directory**  >  **보안**  >  **개요**로 이동 합니다.
1. **사용자 위험 정책 구성**을 선택 합니다.
   1. **할당** 아래
      1. **사용자** -출시를 제한 하는 경우 **모든 사용자** 를 선택 하거나 **개인 및 그룹을 선택** 합니다.
         1. 필요에 따라 정책에서 사용자를 제외 하도록 선택할 수 있습니다.
      1. **조건**  -  **사용자 위험** Microsoft는이 옵션을 **높음으로**설정 하는 것이 좋습니다.
   1. **컨트롤** 에서
      1. **액세스** - **액세스를 허용** 하 고 **암호 변경을 요구**하는 것이 Microsoft의 권장 사항입니다.
   1. **정책 적용**  -  **끄기**
   1. **저장** -이 작업을 수행 하면 **개요** 페이지로 돌아갑니다.
1. 예를 들어, 위험 검색 중 하나를 시뮬레이션 하는 등의 방법으로 테스트 계정의 사용자 위험을 상승 시킵니다.
1. 몇 분 정도 기다린 후 사용자에 대 한 위험이 상승 되었는지 확인 합니다. 그렇지 않으면 사용자에 대 한 더 많은 위험 검색을 시뮬레이트합니다.
1. 위험 정책으로 돌아가서 정책 **적용** 을 **켜기** 로 설정 하 고 정책 변경 내용을 **저장** 합니다.
1. 이제 높은 위험 수준으로 사용자를 사용 하 여 로그인 하 여 사용자 위험 기반 조건부 액세스를 테스트할 수 있습니다.

### <a name="sign-in-risk-security-policy"></a>로그인 위험 보안 정책

로그인 위험 정책을 테스트하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **Azure Active Directory**  >  **보안**  >  **개요**로 이동 합니다.
1. **로그인 위험 정책 구성**을 선택 합니다.
   1. **할당** 아래
      1. **사용자** -출시를 제한 하는 경우 **모든 사용자** 를 선택 하거나 **개인 및 그룹을 선택** 합니다.
         1. 필요에 따라 정책에서 사용자를 제외 하도록 선택할 수 있습니다.
      1. **조건**  -  **로그인 위험** Microsoft는이 옵션을 **보통 이상**으로 설정 하는 것이 좋습니다.
   1. **컨트롤** 에서
      1. **액세스** - **액세스를 허용** 하 고 **multi-factor authentication을 요구**하는 것이 Microsoft의 권장 사항입니다.
   1. **정책 적용**  -  **설정**
   1. **저장** -이 작업을 수행 하면 **개요** 페이지로 돌아갑니다.
1. 이제 위험한 세션을 사용 하 여 로그인 (예: 사용자 브라우저 사용) 하 여 로그인 위험 기반 조건부 액세스를 테스트할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [위험이란?](concept-identity-protection-risks.md)

- [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory ID 보호](overview-identity-protection.md)
