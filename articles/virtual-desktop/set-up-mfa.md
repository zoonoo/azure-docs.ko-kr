---
title: Windows 가상 데스크톱 - Azure에 대한 Azure 다단계 인증 설정
description: Windows 가상 데스크톱에서 보안 강화를 위해 Azure 다단계 인증을 설정하는 방법
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998469"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 설정

Windows 가상 데스크톱용 Windows 클라이언트는 Windows 가상 데스크톱을 로컬 컴퓨터와 통합하기 위한 훌륭한 옵션입니다. 그러나 Windows Virtual Desktop 계정을 Windows 클라이언트로 구성할 때 자신과 사용자를 안전하게 유지하기 위해 취해야 할 특정 조치가 있습니다.

처음 로그인할 때 클라이언트는 사용자 이름, 암호 및 Azure MFA를 요청합니다. 그런 다음 다음에 로그인할 때 클라이언트는 Azure Active Directory(AD) 엔터프라이즈 응용 프로그램에서 토큰을 기억합니다. **나를 기억하기를**선택하면 자격 증명을 다시 입력할 필요 없이 클라이언트를 다시 시작한 후 사용자가 로그인할 수 있습니다.

자격 증명을 기억하는 것은 편리하지만 엔터프라이즈 시나리오 또는 개인 장치에 대한 배포를 덜 안전하게 만들 수도 있습니다. 사용자를 보호하려면 클라이언트가 MFA(Azure 다단계 인증) 자격 증명을 계속 요청해야 합니다. 이 문서에서는 이 설정을 사용하도록 Windows 가상 데스크톱에 대한 조건부 액세스 정책을 구성하는 방법을 보여 주며 이 기능을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 위해 필요한 것은 다음과 같습니다.

- 모든 사용자에게 다음 라이선스 중 하나를 할당합니다.
  - 마이크로 소프트 365 E3 또는 E5
  - Azure Active 디렉터리 프리미엄 P1 또는 P2
  - 엔터프라이즈 모빌리티 + 보안 E3 또는 E5
- 그룹 구성원으로 할당된 사용자가 있는 Azure Active Directory 그룹입니다.
- 모든 사용자에 대해 Azure MFA를 사용하도록 설정합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [사용자에 대한 2단계 인증을 요구하는 방법을](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)참조하십시오.

>[!NOTE]
>다음 설정은 Windows [가상 데스크톱 웹 클라이언트에도](https://rdweb.wvd.microsoft.com/webclient/index.html)적용됩니다.

## <a name="opt-in-to-the-conditional-access-policy"></a>조건부 액세스 정책 에 옵트인

1. Azure 활성 디렉터리 를 **엽니다.**

2. 모든 응용 프로그램 탭으로 **이동합니다.** "응용 프로그램 유형" 드롭다운 메뉴에서 **엔터프라이즈 응용 프로그램을**선택한 다음 Windows 가상 데스크톱 **클라이언트를**검색합니다.

    ![모든 응용 프로그램 탭의 스크린샷입니다. 사용자가 검색 창에 "windows 가상 데스크톱 클라이언트"를 입력하고 앱이 검색 결과에 표시되었습니다.](media/all-applications-search.png)

3. **조건부 액세스를**선택합니다.

    ![사용자가 조건부 액세스 탭위에 마우스 커서를 마우스로 가리키는 것을 보여 주는 스크린샷입니다.](media/conditional-access-location.png)

4. **+ 새 정책을**선택합니다.

   ![조건부 액세스 페이지의 스크린샷입니다. 사용자가 새 정책 단추 위에 마우스 커서를 마우스로 가리키고 있습니다.](media/new-policy-button.png)

5. **규칙의** **이름을** 입력한 다음 필수 구성 사이트에서 만든 **그룹의** *이름을 **선택합니다.**

6. **을 선택한**다음 완료 를 **선택합니다.**

7. 다음으로 **클라우드 앱 또는 작업을**엽니다.

8. **선택** 패널에서 Windows **가상 데스크톱** 엔터프라이즈 앱을 선택합니다.

    ![클라우드 앱 또는 작업 페이지의 스크린샷입니다. 사용자가 옆에 있는 확인 표시를 선택하여 Windows 가상 데스크톱 앱을 선택했습니다. 선택한 앱이 빨간색으로 강조 표시됩니다.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >다음 이미지와 같이 화면 왼쪽에 선택된 Windows 가상 데스크톱 클라이언트 앱도 표시됩니다. 정책이 작동하려면 Windows 가상 데스크톱 및 Windows 가상 데스크톱 클라이언트 엔터프라이즈 앱이 모두 필요합니다.
    >
    > ![클라우드 앱 또는 작업 페이지의 스크린샷입니다. Windows 가상 데스크톱 및 Windows 가상 데스크톱 클라이언트 앱이 빨간색으로 강조 표시됩니다.](media/cloud-apps-enterprise-selected.png)

9. **선택 선택**

10. 다음, 오픈 **그랜트** 

11. **다단계 인증 필요를**선택한 다음 **선택한 컨트롤 중 하나를**선택합니다.
   
    ![그랜트 페이지의 스크린샷입니다. "다단계 인증 필요"가 선택됩니다.](media/grant-page.png)

    >[!NOTE]
    >조직에 MDM에 등록된 장치가 있고 MFA 프롬프트가 표시되지 않도록 하려면 장치 **필요를 선택하여 준수로 표시할**수도 있습니다.

12. **세션**을 선택합니다.

13. 로그인 **빈도를** **활성으로**설정한 다음 값을 **1시간으로**변경합니다.

    ![세션 페이지의 스크린샷입니다. 세션 메뉴에 로그인 빈도 드롭다운 메뉴가 "1" 및 "시간"으로 변경되었음을 보여 줍니다.](media/sign-in-frequency.png)
   
    >[!NOTE]
    >정책을 변경하면 Windows 가상 데스크톱 환경의 활성 세션이 계속 작동합니다. 그러나 연결을 끊거나 사인오프하는 경우 60분 후에 자격 증명을 다시 제공해야 합니다. 설정을 변경하면 조직의 보안 정책과 일치하는 한 시간 시간 지정 기간을 원하는 만큼 연장할 수 있습니다.
    >
    >기본 설정은 90일의 롤링 기간으로, 클라이언트는 90일 이상 컴퓨터에서 비활성 상태인 후 리소스에 액세스하려고 할 때 사용자에게 다시 로그인하도록 요청합니다.

14. 정책을 사용하도록 설정합니다.

15. 정책을 확인하려면 **만들기를** 선택합니다.

모든 작업을 완료했습니다! 허용 목록이 의도한 대로 작동하는지 확인하기 위해 정책을 테스트하십시오.
