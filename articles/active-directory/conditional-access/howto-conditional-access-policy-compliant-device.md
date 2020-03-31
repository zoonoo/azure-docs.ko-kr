---
title: 조건부 액세스 - 호환 장치 필요 - Azure Active Directory
description: 호환 장치를 요구하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc6bd486c1e8338eaf875c7026764c80d49e2f05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295196"
---
# <a name="conditional-access-require-compliant-devices"></a>조건부 액세스: 호환 장치 필요

Microsoft Intune을 배포한 조직은 장치에서 반환된 정보를 사용하여 다음과 같은 규정 준수 요구 사항을 충족하는 장치를 식별할 수 있습니다.

* 잠금을 해제하기 위해 PIN필요
* 장치 암호화 필요
* 최소 또는 최대 운영 체제 버전 필요
* 장치를 요구하는 것은 탈옥또는 뿌리가 되지 않습니다.

이 정책 준수 정보는 조건부 Access에서 리소스에 대한 액세스 권한을 부여하거나 차단하는 결정을 내릴 수 있는 Azure AD로 전달됩니다. 장치 준수 정책에 대한 자세한 내용은 [Intune을 사용하여 조직의 리소스에 대한 액세스를 허용하는 장치에 대한 규칙 설정](/intune/protect/device-compliance-get-started) 문서에서 찾을 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 리소스에 액세스하는 장치가 조직의 Intune 준수 정책을 준수하는 것으로 표시하도록 하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **모든 사용자를**선택합니다.
   1. **제외에서** **사용자 및 그룹을** 선택하고 조직의 비상 액세스 또는 브레이크 글래스 계정을 선택합니다. 
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업** > **에서 포함**, 모든 **클라우드 앱을**선택합니다.
   1. 정책에서 특정 응용 프로그램을 제외해야 하는 경우 **제외된 클라우드 앱 선택** 아래의 **제외** 탭에서 해당 응용 프로그램을 선택하고 **선택 을**선택할 수 있습니다.
   1. **완료를 선택합니다.**
1. **조건 클라이언트** > **앱(미리 보기)에서** **구성을** **예로**설정하고 **완료를**선택합니다.
1. **액세스 제어** > **권한 부여에서** **장치를 준수로 표시하려면 필요를**선택합니다.
   1. **선택**을 선택합니다.
1. 설정을 확인하고 **정책 활성화를** **설정 합니다.**
1. 정책을 활성화하려면 **만들기를** 선택합니다.

> [!NOTE]
> 위의 단계를 사용하여 **모든 사용자** 및 **모든 클라우드 앱에** 대해 장치 **필요를 준수로 표시하도록** 선택하더라도 새 장치를 Intune에 등록할 수 있습니다. **장치를 규격 제어로 표시해야** 해도 Intune 등록이 차단되지 않습니다. 

### <a name="known-behavior"></a>알려진 동작

Windows 7에서 iOS, Android, macOS 및 일부 타사 웹 브라우저 Azure AD는 장치가 Azure AD에 등록될 때 프로비전되는 클라이언트 인증서를 사용하여 장치를 식별합니다. 사용자가 브라우저를 통해 처음 에 서명하면 인증서를 선택하라는 메시지가 표시됩니다. 최종 사용자는 브라우저를 계속 사용하려면 먼저 이 인증서를 선택해야 합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[디바이스 준수 정책이 Azure AD에서 사용됨](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
