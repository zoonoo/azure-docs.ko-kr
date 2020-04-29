---
title: Windows 가상 데스크톱에 대 한 Azure multi-factor authentication 설정-Azure
description: Windows 가상 데스크톱의 보안 강화를 위해 Azure multi-factor authentication을 설정 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998469"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 설정

Windows 용 windows 클라이언트 가상 데스크톱은 로컬 컴퓨터와 Windows 가상 데스크톱을 통합 하는 데 유용한 옵션입니다. 그러나 windows 가상 데스크톱 계정을 Windows 클라이언트에 구성 하는 경우 사용자와 사용자를 안전 하 게 유지 하기 위해 수행 해야 하는 특정 측정값이 있습니다.

처음 로그인 하면 클라이언트에서 사용자 이름, 암호 및 Azure MFA를 묻는 메시지를 표시 합니다. 그런 다음, 다음에 로그인 할 때 클라이언트는 AD (Azure Active Directory) 엔터프라이즈 응용 프로그램에서 토큰을 기억할 것입니다. 사용자 **암호**를 선택 하면 사용자가 자격 증명을 다시 입력할 필요 없이 클라이언트를 다시 시작한 후 로그인 할 수 있습니다.

자격 증명을 기억 하는 것은 편리 하지만 엔터프라이즈 시나리오 또는 개인 장치에 대 한 배포는 보안 수준이 떨어질 수도 있습니다. 사용자를 보호 하려면 클라이언트가 MFA (Azure Multi-Factor Authentication) 자격 증명을 계속 요청 하는지 확인 해야 합니다. 이 문서에서는 Windows 가상 데스크톱에 대 한 조건부 액세스 정책을 구성 하 여이 설정을 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>전제 조건

시작 하는 데 필요한 항목은 다음과 같습니다.

- 다음 라이선스 중 하나를 모든 사용자에 게 할당 합니다.
  - Microsoft 365 E3 또는 E5
  - Azure Active Directory Premium P1 또는 P2
  - Enterprise Mobility + Security E3 또는 E5
- 사용자가 그룹 멤버로 할당 된 Azure Active Directory 그룹입니다.
- 모든 사용자에 대해 Azure MFA를 사용 하도록 설정 합니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [사용자에 대해 2 단계 인증을 요구 하는 방법](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)을 참조 하세요.

>[!NOTE]
>다음 설정은 [Windows 가상 데스크톱 웹 클라이언트](https://rdweb.wvd.microsoft.com/webclient/index.html)에도 적용 됩니다.

## <a name="opt-in-to-the-conditional-access-policy"></a>조건부 액세스 정책 옵트인

1. **Azure Active Directory**를 엽니다.

2. **모든 응용 프로그램** 탭으로 이동 합니다. "응용 프로그램 종류" 드롭다운 메뉴에서 **엔터프라이즈 응용 프로그램**을 선택한 다음 **Windows 가상 데스크톱 클라이언트**를 검색 합니다.

    ![모든 응용 프로그램 탭의 스크린샷 사용자가 검색 창에 "windows 가상 데스크톱 클라이언트"를 입력 하면 앱이 검색 결과에 표시 됩니다.](media/all-applications-search.png)

3. **조건부 액세스**를 선택 합니다.

    ![사용자가 조건부 액세스 탭 위로 마우스 커서를 이동 하는 것을 보여 주는 스크린샷](media/conditional-access-location.png)

4. **+ 새 정책**을 선택 합니다.

   ![조건부 액세스 페이지의 스크린샷 사용자가 마우스 커서를 새 정책 단추 위로 이동 하 고 있습니다.](media/new-policy-button.png)

5. **규칙**의 **이름을** 입력 하 고 필수 구성 요소에서 만든 **그룹** 의 이름을 **선택** 합니다.

6. **선택**을 선택한 다음 **완료**를 선택 합니다.

7. 그런 다음 **클라우드 앱 또는 작업**을 엽니다.

8. **선택** 패널에서 **Windows 가상 데스크톱** 엔터프라이즈 앱을 선택 합니다.

    ![클라우드 앱 또는 작업 페이지의 스크린샷 사용자가 옆의 확인 표시를 선택 하 여 Windows 가상 데스크톱 앱을 선택 했습니다. 선택한 앱은 빨간색으로 강조 표시 됩니다.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >다음 그림에 표시 된 것 처럼 화면 왼쪽에 Windows 가상 데스크톱 클라이언트 앱을 선택 하는 것도 표시 됩니다. 정책을 사용 하려면 Windows 가상 데스크톱 및 Windows 가상 데스크톱 클라이언트 엔터프라이즈 앱이 모두 필요 합니다.
    >
    > ![클라우드 앱 또는 작업 페이지의 스크린샷 Windows 가상 데스크톱 및 Windows 가상 데스크톱 클라이언트 앱은 빨간색으로 강조 표시 됩니다.](media/cloud-apps-enterprise-selected.png)

9. **선택** 선택

10. 다음으로 **권한 부여** 를 엽니다. 

11. **Multi-factor Authentication 필요**를 선택 하 고 선택 **된 컨트롤 중 하나 필요**를 선택 합니다.
   
    ![권한 부여 페이지의 스크린샷 "Multi-factor authentication 필요"를 선택 합니다.](media/grant-page.png)

    >[!NOTE]
    >조직에 MDM 등록 장치가 있는 경우 MFA 프롬프트를 표시 하지 않으려면 **장치를 규격으로 표시 하도록 요구**를 선택할 수도 있습니다.

12. **세션**을 선택 합니다.

13. **로그인 빈도** 를 **활성**으로 설정 하 고 값을 **1 시간**으로 변경 합니다.

    ![세션 페이지의 스크린샷 세션 메뉴에는 로그인 빈도 드롭다운 메뉴가 "1" 및 "Hours"로 변경 된 것으로 표시 됩니다.](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Windows 가상 데스크톱 환경의 활성 세션은 정책을 변경 하면 계속 작동 합니다. 그러나 연결을 끊거나 로그 오프 하는 경우 60 분 후에 자격 증명을 다시 제공 해야 합니다. 설정을 변경 하는 경우 조직의 보안 정책에 맞게 제한 시간을 원하는 만큼 확장할 수 있습니다.
    >
    >기본 설정은 90 일의 이동 기간입니다. 즉, 클라이언트는 90 일 이상 컴퓨터에서 비활성화 된 후에 리소스에 액세스 하려고 할 때 사용자에 게 다시 로그인 하도록 요청 합니다.

14. 정책을 사용 하도록 설정 합니다.

15. **만들기** 를 선택 하 여 정책을 확인 합니다.

모든 작업을 완료했습니다! 언제 든 지 자유롭게 정책을 테스트 하 여 허용 목록이 의도 한 대로 작동 하는지 확인 합니다.
