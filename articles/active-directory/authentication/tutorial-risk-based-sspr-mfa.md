---
title: Azure ID 보호를 사용하는 위험 기반 MFA 및 SSPR
description: 이 자습서에서는 Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 Azure ID 보호를 사용하여 위험한 동작을 줄일 것입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: df3344efadbc915bba0c863979cae8b8fdff99b0
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512128"
---
# <a name="tutorial-use-risk-events-to-trigger-multi-factor-authentication-and-password-changes"></a>자습서: 위험 이벤트를 사용하여 Multi-Factor Authentication 및 암호 변경 트리거

이 자습서에서는 단순한 모니터링 및 보고 도구 이상의 역할을 하는 Azure AD Premium P2 기능인 Azure AD(Azure Active Directory) ID 보호를 사용하도록 설정할 것입니다. 조직의 ID를 보호하려면 위험한 동작에 자동으로 대응하는 위험 기반 정책을 구성하면 됩니다. 이러한 정책은 암호 변경을 요구하거나 Multi-Factor Authentication을 적용하는 등 자동으로 업데이트를 차단하거나 시작할 수 있습니다.

기존 조건부 액세스 정책 외에도 Azure AD ID 보호 정책을 추가 보호 계층으로 사용할 수 있습니다. 사용자가 이러한 정책 중 하나가 필요한 위험한 동작을 전혀 트리거하지 않을 수도 있지만, 관리자는 사용자가 보호된다는 사실을 알고 있습니다.

다음과 같은 일부 항목이 위험 이벤트를 트리거할 수 있습니다.

* 자격 증명이 손실된 사용자
* 익명 IP 주소에서 로그인
* 비정상적 위치로 불가능한 이동
* 감염된 디바이스에서 로그인
* 의심스러운 작업이 있는 IP 주소에서 로그인
* 알 수 없는 위치에서 로그인

Azure AD ID 보호에 대한 자세한 내용은 [Azure AD ID 보호란](../active-directory-identityprotection.md) 문서에서 찾을 수 있습니다.

> [!div class="checklist"]
> * Azure MFA 등록 사용
> * 위험 기반 암호 변경 사용
> * 위험 기반 Multi-Factor Authentication 사용

## <a name="prerequisites"></a>필수 조건

* 적어도 평가판 Azure AD Premium P2 라이선스가 할당된 정상 작동 중인 Azure AD 테넌트에 대한 액세스 권한.
* Azure AD 테넌트의 전역 관리자 권한이 있는 계정.
* SSPR(이전 셀프 서비스 암호 재설정) 및 MFA(Multi-Factor Authentication) 자습서 완료.

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>SSPR 및 MFA에 위험 기반 정책 사용

위험 기반 정책을 사용하도록 설정하는 프로세스는 간단합니다. 아래 단계는 샘플 구성을 안내합니다.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>사용자가 Multi-Factor Authentication에 등록할 수 있도록 설정

Azure AD ID 보호에는 사용자를 Multi-Factor Authentication에 등록하고 현재 등록 상태를 쉽게 식별할 수 있는 기본 정책이 포함되어 있습니다. 이 정책을 사용해도 사용자가 Multi-Factor Authentication을 수행해야 하는 것은 아니지만, 미리 등록하라는 요청을 받게 됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스**를 클릭하고 **Azure AD ID 보호**로 이동합니다.
1. **MFA 등록**을 클릭합니다.
1. 정책 적용을 **켜기**로 설정합니다.
   1. 이 정책을 설정하면 모든 사용자는 Multi-Factor Authentication에서 사용할 준비 방법을 등록해야 합니다.
1. **저장**을 클릭합니다.

   ![사용자가 Azure AD ID 보호를 사용하여 로그인 시 MFA에 등록하도록 요구](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>위험 기반 암호 변경 사용

Microsoft는 연구원, 법 집행 기관, Microsoft의 다양한 보안 팀, 신뢰할 수 있는 기타 소스와 협력하여 사용자 이름 및 암호 쌍을 찾습니다. 이러한 쌍 중의 하나가 환경의 계정과 일치하는 경우 다음 정책을 사용하여 위험 기반 암호 변경을 트리거할 수 있습니다.

1. 사용자 위험 정책을 클릭합니다.
1. **조건** 아래에서 **사용자 위험**을 선택한 다음, **중간 이상**을 선택합니다.
1. "선택"을 클릭하고 "완료"를 클릭합니다.
1. **액세스** 아래에서 **액세스 허용**을 선택한 다음, **암호 변경 필요**를 선택합니다.
1. [선택]을 클릭합니다.
1. 정책 적용을 **켜기**로 설정합니다.
1. 페이지 맨 아래에 있는 **저장**

### <a name="enable-risk-based-multi-factor-authentication"></a>위험 기반 Multi-Factor Authentication 사용

대부분의 사용자는 추적 가능한 일반 동작을 갖고 있으며, 정상 범위를 벗어나면 사용자가 로그인하도록 허용하는 것이 위험할 수 있습니다. 이러한 사용자를 차단할 수도 있고 Multi-Factor Authentication을 수행하여 신분을 증명하게 할 수도 있습니다. 위험한 로그인이 감지되면 MFA를 요구하는 정책을 사용하려면 다음 정책을 사용하도록 설정합니다.

1. 로그인 위험 정책을 클릭합니다.
1. **조건** 아래에서 **사용자 위험**을 선택한 다음, **중간 이상**을 선택합니다.
1. "선택"을 클릭하고 "완료"를 클릭합니다.
1. **액세스** 아래에서 **액세스 허용**을 선택한 다음, **Multi-Factor Authentication 필요**를 선택합니다.
1. [선택]을 클릭합니다.
1. 정책 적용을 **켜기**로 설정합니다.
1. 페이지 맨 아래에 있는 **저장**

## <a name="clean-up-resources"></a>리소스 정리

테스트를 완료했고 더 이상 위험 기반 정책을 사용하지 않으려면 해제하려는 각 정책으로 돌아가서 **정책 적용**을 **끄기**로 설정합니다.
