---
title: Microsoft 인증자 앱으로 암호 없는 로그인 - Azure Active Directory
description: Microsoft 인증자 앱을 사용하여 Azure AD에 암호 없는 로그인 을 사용하도록 설정합니다(미리 보기)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c684d6f1fbd8128ae020b6fd29da928b286aa18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126693"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Microsoft 인증자 앱에서 암호 없는 로그인 사용(미리 보기)

암호를 사용하지 않고 Microsoft Authenticator 앱을 사용하여 모든 Azure AD 계정에 로그인할 수 있습니다. [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 기술과 마찬가지로 Microsoft Authenticator는 키 기반 인증을 사용하여 디바이스에 연결되고 생체 인식 또는 PIN을 사용하는 사용자 자격 증명을 사용하도록 설정합니다. 이 인증 방법은 모바일을 포함한 모든 장치 플랫폼과 Microsoft 인증 라이브러리와 통합되는 모든 앱 또는 웹 사이트에서 사용할 수 있습니다. 

![사용자가 로그인을 승인하도록 요청하는 브라우저 로그인의 예](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

사용자 이름을 입력한 후 암호에 대한 프롬프트를 보는 대신 Microsoft 인증자 앱에서 전화 로그인을 사용하도록 설정한 사용자에게 앱의 번호를 탭하라는 메시지가 표시됩니다. 앱에서 사용자는 번호가 일치해야 하고 [승인]을 선택한 다음, PIN 또는 생체 인식을 제공해야 인증이 완료됩니다.

> [!NOTE]
> 이 기능은 2017년 3월부터 Microsoft 인증자 앱에 사용되어 왔기 때문에 디렉터리에 대해 정책을 사용하도록 설정하면 사용자가 이 흐름을 즉시 접하고 정책에 의해 활성화되지 않은 경우 오류 메시지가 표시될 수 있습니다. 이러한 변화를 인지하고 사용자가 대비하도록 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 확인 방법으로 푸시 알림을 허용하는 Azure 다단계 인증 
- iOS 8.0 이상 또는 Android 6.0 이상을 실행하는 디바이스에 설치된 최신 버전의 Microsoft Authenticator

> [!NOTE]
> Azure AD PowerShell을 사용하여 이전 Microsoft 인증자 앱 암호 없는 로그인 미리 보기를 사용하도록 설정한 경우 전체 디렉터리에서 사용하도록 설정되었습니다. 이 새 메서드를 사용 하 여 사용 하는 경우 PowerShell 정책을 대체 합니다. 새 인증 방법을 통해 테넌트의 모든 사용자를 사용하도록 설정하는 것이 좋습니다. 

## <a name="enable-passwordless-authentication-methods"></a>암호 없는 인증 방법 사용

### <a name="enable-the-combined-registration-experience"></a>결합된 등록 환경 사용

암호 없는 인증 방법에 대 한 등록 기능은 결합 된 등록 미리 보기에 의존 합니다. 결합된 등록 미리 보기를 사용하려면 다음 문서의 단계를 [따르십시오.](howto-registration-mfa-sspr-combined.md)

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>암호없는 전화 로그인 인증 방법 사용

1. [Azure 포털에](https://portal.azure.com) 로그인
1. *Azure Active Directory*를 검색하고 선택합니다. **보안** > **인증 방법** > **인증 방법 정책 선택(미리 보기)**
1. **암호가 없는 전화 로그인에서**다음 옵션을 선택합니다.
   1. **사용 -** 예 또는 아니요
   1. **대상** - 모든 사용자 또는 사용자 선택
1. 새 정책을 설정하기 위해 **저장**

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Microsoft 인증자 앱의 사용자 등록 및 관리

1. 다음 을 찾아보십시오.[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. 아직 로그인하지 않은 경우
1. **메서드 추가를**클릭하고 인증자 앱을 선택하고 **추가를** 클릭하여 **인증자 앱을**추가합니다.
1. 지침에 따라 장치에 Microsoft 인증자 앱을 설치하고 구성합니다.
1. **완료를** 클릭하여 인증자 MFA 앱 설정 흐름을 완료합니다. 
1. **Microsoft 인증자에서**계정 드롭다운 메뉴에서 **전화 로그인 활성화를** 선택합니다.
1. 암호없는 전화 로그인에 대한 등록을 완료하려면 응용 프로그램의 지침을 따르십시오. 

조직은 Microsoft 인증자 앱에서 설정하고 전화 로그인을 활성화하기 위한 추가 지원을 위해 [암호가 아닌 휴대폰으로](../user-help/microsoft-authenticator-app-phone-signin-faq.md) 로그인하는 문서를 사용자를 가리킬 수 있습니다. 이러한 설정을 적용하려면 로그아웃하고 테넌트에 다시 로그아웃해야 할 수 있습니다. 

## <a name="sign-in-with-passwordless-credential"></a>암호 없는 자격 증명으로 로그인

공개 미리 보기의 경우 사용자가 이 새 자격 증명을 만들거나 사용할 수 있도록 하는 방법은 없습니다. 사용자는 관리자가 테넌트를 사용하도록 설정하고 사용자가 전화 **and** 로그인을 사용하도록 Microsoft 인증자 앱을 업데이트한 경우에만 암호 없는 로그인을 발생합니다.

웹에서 사용자 이름을 입력하고 **다음을**선택하면 사용자에게 번호가 표시되고 Microsoft 인증자 앱에서 암호를 사용하는 대신 인증할 적절한 번호를 선택하라는 메시지가 표시됩니다. 

![Microsoft 인증자 앱을 사용하는 브라우저 로그인의 예](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>알려진 문제

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>사용자는 정책에 의해 활성화되지 않지만 여전히 Microsoft 인증기에서 암호가없는 전화 로그인 방법이 있습니다.

사용자가 현재 Microsoft 인증자 앱 또는 이전 장치에서 암호가 없는 전화 로그인 자격 증명을 만들었을 수 있습니다. 관리자가 암호 없는 전화 로그인에 대 한 인증 방법 정책을 사용 하면 등록 된 자격 증명을 가진 모든 사용자가 새 로그인 프롬프트를 경험 하기 시작 합니다., 그들은 정책을 사용 하도록 사용 하 여 여부에 관계 없이. 사용자가 정책별 자격 증명을 사용할 수 없는 경우 인증 흐름을 완료한 후 오류가 표시됩니다. 

관리자는 사용자가 암호없는 전화 로그인을 사용하도록 선택하거나 사용자가 메서드를 제거해야합니다. 사용자가 더 이상 등록된 장치가 없는 경우 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 이동하여 제거할 수 있습니다. 여전히 MFA에 대한 인증자를 사용하는 경우 Microsoft 인증자 내에서 **전화 로그인 비활성화를** 선택할 수 있습니다.  

### <a name="ad-fs-integration"></a>AD FS 통합

사용자가 Microsoft Authenticator 암호 없는 자격 증명을 사용하도록 설정하면 해당 사용자의 인증은 항상 승인을 위해 알림을 보내는 것으로 기본 설정됩니다. 이 논리는 하이브리드 테넌트의 사용자가 [암호를 대신 사용]을 클릭하는 추가 단계를 수행하지 않고 로그인 확인을 위해 ADFS로 이동하는 것을 방지합니다. 또한 이 프로세스는 온-프레미스 조건부 액세스 정책 및 통과 인증 흐름을 무시합니다. 

사용자가 응답되지 않은 암호없는 전화 로그인 확인이 보류 중이고 다시 로그인을 시도하는 경우, 사용자는 대신 암호를 입력하기 위해 ADFS로 이동 될 수 있습니다.  

### <a name="azure-mfa-server"></a>Azure MFA 서버

조직의 온-프레미스 Azure MFA 서버를 통해 MFA를 사용하도록 설정된 최종 사용자는 암호가 없는 단일 전화 로그인 자격 증명을 만들고 사용할 수 있습니다. 사용자가 자격 증명으로 Microsoft Authenticator의 여러 설치(5개 이상)를 업그레이드하려고 시도하면 이 변경으로 인해 오류가 발생할 수 있습니다.  

### <a name="device-registration"></a>디바이스 등록

이 새로운 강력한 자격 증명을 만들기 위한 전제 조건 중 하나는 Microsoft 인증자 앱이 설치된 장치도 Azure AD 테넌트 내에 개별 사용자에게 등록해야 한다는 것입니다. 현재 장치 등록 제한으로 인해 장치는 단일 테넌트에만 등록할 수 있습니다. 이 제한은 Microsoft Authenticator 앱에서 휴대폰 로그인에 대해 하나의 회사 또는 학교 계정만 설정할 수 있음을 의미합니다.

### <a name="intune-mobile-application-management"></a>Intune 모바일 애플리케이션 관리 

MAM(모바일 응용 프로그램 관리)이 필요한 정책의 적용을 받는 최종 사용자는 Microsoft 인증자 앱에서 암호없는 자격 증명을 등록할 수 없습니다. 

> [!NOTE]
> 장치 등록은 장치 관리 또는 "MDM"과 다름없습니다. Azure AD 디렉터리에서 장치 ID와 사용자 ID만 함께 연결합니다.  

## <a name="next-steps"></a>다음 단계

[암호 없음이란?](concept-authentication-passwordless.md)

[디바이스 등록에 대한 자세한 정보](../devices/overview.md#getting-devices-in-azure-ad)

[Azure Multi-Factor Authentication에 대한 자세한 정보](../authentication/howto-mfa-getstarted.md)
