---
title: Windows 가상 데스크톱에 대 한 Azure Multi-Factor Authentication 설정-Azure
description: Windows 가상 데스크톱의 보안 강화를 위해 Azure Multi-Factor Authentication를 설정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16abe8d155a0d7d7f65c69e6305da62bd8813ea4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361152"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대해 Azure Multi-Factor Authentication을 사용하도록 설정

Windows 용 windows 클라이언트 가상 데스크톱은 로컬 컴퓨터와 Windows 가상 데스크톱을 통합 하는 데 유용한 옵션입니다. 그러나 windows 가상 데스크톱 계정을 Windows 클라이언트에 구성 하는 경우 사용자와 사용자를 안전 하 게 유지 하기 위해 수행 해야 하는 특정 측정값이 있습니다.

처음 로그인 하면 클라이언트에서 사용자 이름, 암호 및 Azure MFA를 묻는 메시지를 표시 합니다. 그런 다음, 다음에 로그인 할 때 클라이언트는 AD (Azure Active Directory) 엔터프라이즈 응용 프로그램에서 토큰을 기억할 것입니다. 사용자 **암호**를 선택 하면 사용자가 자격 증명을 다시 입력할 필요 없이 클라이언트를 다시 시작한 후 로그인 할 수 있습니다.

자격 증명을 기억 하는 것은 편리 하지만 엔터프라이즈 시나리오 또는 개인 장치에 대 한 배포는 보안 수준이 떨어질 수도 있습니다. 사용자를 보호 하려면 클라이언트가 MFA (Azure Multi-Factor Authentication) 자격 증명을 계속 요청 하는지 확인 해야 합니다. 이 문서에서는 Windows 가상 데스크톱에 대 한 조건부 액세스 정책을 구성 하 여이 설정을 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하는 데 필요한 항목은 다음과 같습니다.

- Azure Active Directory Premium P1 또는 P2를 포함 하는 라이선스를 사용자에 게 할당 합니다.
- 사용자가 그룹 멤버로 할당 된 Azure Active Directory 그룹입니다.
- 모든 사용자에 대해 Azure MFA를 사용 하도록 설정 합니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [사용자에 대해 2 단계 인증을 요구 하는 방법](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)을 참조 하세요.

> [!NOTE]
> 다음 설정은 [Windows 가상 데스크톱 웹 클라이언트](https://rdweb.wvd.microsoft.com/webclient/index.html)에도 적용 됩니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

이 섹션에서는 Windows 가상 데스크톱에 연결할 때 multi-factor authentication을 요구 하는 조건부 액세스 정책을 만드는 방법을 보여 줍니다.

1. **Azure Portal**에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
2. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
3. **새 정책**을 선택합니다.
4. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
5. **할당** 아래에서 **사용자 및 그룹**을 선택합니다.
   - **포함**아래에서 **사용자 및 그룹 선택**  >  **사용자 및 그룹** 을 선택 하 > 필수 구성 요소 단계에서 만든 그룹을 선택 합니다.
   - **완료** 를 선택합니다.
6. **클라우드 앱 또는 작업**  >  **포함**아래에서 **앱 선택**을 선택 합니다.
   - **Windows 가상 데스크톱** (앱 ID 9cdead84-a844-4324-93f2-b2e6bb768d07)을 선택 하 고를 선택한 다음 **완료**를 **선택**합니다.

     > [!div class="mx-imgBorder"]
     > ![클라우드 앱 또는 작업 페이지의 스크린샷 Windows 가상 데스크톱 및 Windows 가상 데스크톱 클라이언트 앱은 빨간색으로 강조 표시 됩니다.](media/cloud-apps-enterprise.png)

     >[!NOTE]
     >선택 하려는 앱의 앱 ID를 찾으려면 **엔터프라이즈 응용** 프로그램으로 이동 하 고 응용 프로그램 유형 드롭다운 메뉴에서 **Microsoft 응용 프로그램** 을 선택 합니다.

7. **액세스 제어**  >  **권한 부여**에서 **액세스 허용**, **다단계 인증 필요**를 선택 하 고를 **선택**합니다.
8. **액세스 제어**  >  **세션**에서 **로그인 빈도**를 선택 하 고, 값을 **1** 로, 단위를 **시간**으로 설정한 다음, **선택**을 선택 합니다.
9. 설정을 확인하고 **정책 사용**을 **켜기**로 설정합니다.
10. **만들기** 를 선택 하 여 정책을 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 정책에 대 한 자세한 정보](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [사용자 로그인 빈도에 대 한 자세한 정보](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
