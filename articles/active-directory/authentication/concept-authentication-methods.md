---
title: 인증 방법 및 기능 - Azure Active Directory
description: 로그인 이벤트 개선 및 보안을 지원하는 데 사용할 수 있는 Azure Active Directory의 다양한 인증 방법 및 기능에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 642f2705f54fe8f84cfde7ff039c9a723be59595
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770962"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory에서 사용할 수 있는 인증 및 확인 방법은 무엇인가요?

Azure AD(Azure Active Directory)의 계정에 대한 로그인 환경에는 사용자가 스스로 인증할 수 있는 다양한 방법이 있습니다. 사용자 이름 및 암호는 사용자가 자격 증명을 기록으로 제공하는 가장 일반적인 방법입니다. Azure AD의 최신 인증 및 보안 기능을 사용하면 기본 암호를 보완하거나 추가 인증 방법으로 대체할 수 있습니다.

Azure AD의 사용자는 다음 인증 방법 중 하나를 사용하여 인증하도록 선택할 수 있습니다.

* 기존 사용자 이름 및 암호
* Microsoft Authenticator 앱의 암호 없는 로그인
* OATH 하드웨어 토큰 또는 FIDO2 보안 키
* SMS 기반 암호 없는 로그인

Azure AD의 많은 계정은 SSPR(셀프 서비스 암호 재설정) 및 Azure Multi-Factor Authentication을 사용하도록 설정됩니다. 이러한 기능에는 전화 통화 또는 본인 확인 질문과 같은 추가 확인 방법이 포함됩니다. 사용자가 여러 확인 방법을 등록하도록 요구하는 것이 좋습니다. 사용자가 한 가지 방법을 사용할 수 없는 경우 다른 방법으로 인증하도록 선택할 수 있습니다.

다음 표에서는 기본 또는 보조 인증에 사용할 수 있는 방법을 간략하게 설명합니다.

| 방법 | Primary authentication(기본 인증) | 보조 인증 |
| --- | --- | --- |
| [암호](#password) | 예 | |
| [Microsoft Authenticator 앱](#microsoft-authenticator-app) | 예(미리 보기) | MFA 및 SSPR |
| [FIDO2 보안 키(미리 보기)](#fido2-security-keys) | 예 | MFA 전용 |
| [OATH 소프트웨어 토큰](#oath-software-tokens) | 예 | MFA |
| [OATH 하드웨어 토큰(미리 보기)](#oath-hardware-tokens-preview) | 예 | MFA |
| [SMS](#phone-options) | 예(미리 보기) | MFA 및 SSPR |
| [음성 통화](#phone-options) | 예 | MFA 및 SSPR |
| [본인 확인 질문](#security-questions) | 예 | SSPR 전용 |
| [전자 메일 주소](#email-address) | 예 | SSPR 전용 |
| [앱 암호](#app-passwords) | 예 | 특정 경우 MFA만 |

이 문서에서는 Azure AD에서 사용할 수 있는 다양한 인증 및 확인 방법과 특정 한도 또는 제한 사항을 간략하게 설명합니다.

![로그인 화면에서 사용하는 인증 방법](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>암호

Azure AD 암호는 기본 인증 방법 중 하나일 때가 많습니다. 암호 인증 방법을 사용하지 않도록 설정할 수는 없습니다.

사용자가 로그인하는 데 암호를 사용하지 않을 때 [SMS 기반 로그인](howto-authentication-sms-signin.md)과 같은 인증 방법을 사용하는 경우에도 암호는 사용 가능한 인증 방법으로 유지됩니다.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 앱

Authenticator 앱은 Azure AD 회사 또는 학교 계정이나 Microsoft 계정에 추가 보안 수준을 제공하며 [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) 및 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)에서 사용할 수 있습니다. Microsoft Authenticator 앱을 사용하면 사용자가 로그인 중에 암호 없는 방식으로 인증하거나 SSPR(셀프 서비스 암호 재설정) 또는 Azure Multi-Factor Authentication 이벤트 중에 추가 확인 옵션으로 인증할 수 있습니다.

사용자는 승인 또는 거부를 위해 모바일 앱을 통해 알림을 받거나, Authenticator 앱을 사용하여 로그인 인터페이스에 입력할 수 있는 OATH 확인 코드를 생성할 수 있습니다. 알림 및 확인 코드를 모두 사용하는 경우 Authenticator 앱을 등록하는 사용자는 두 방법 중 하나를 사용하여 해당 ID를 확인할 수 있습니다.

사용자 이름 및 암호 조합이 아니라 로그인 프롬프트에서 Authenticator 앱을 사용하려면 [Microsoft Authenticator 앱에서 암호 없는 로그인을 사용하도록 설정(미리 보기)](howto-authentication-passwordless-phone.md)을 참조하세요.

> [!NOTE]
> SSPR를 사용하도록 설정하는 경우 사용자에게 모바일 앱을 등록하는 옵션이 없습니다. 대신 사용자는 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)에서 모바일 앱을 등록하거나 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)에서 결합된 보안 정보 등록의 일부로 등록할 수 있습니다.

### <a name="notification-through-mobile-app"></a>모바일 앱을 통한 알림

Authenticator 앱을 사용하면 스마트폰 또는 태블릿에 알림을 표시하여 계정에 대한 무단 액세스를 방지하고 사기성 트랜잭션을 중지할 수 있습니다. 사용자가 알림을 확인하고 올바르면 **확인**을 선택합니다. 그렇지 않으면 **거부**를 선택할 수 있습니다.

![로그인 프로세스를 완료하기 위한 Authenticator 앱 알림의 웹 브라우저 프롬프트 예시 스크린샷](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 조직에서 중국에 근무하거나 출장을 가는 직원이 있을 경우 Android 디바이스에서 ‘모바일 앱을 통한 알림’ 방법은 해당 국가/지역에서 작동하지 않습니다. 이러한 사용자에 대해서는 다른 인증 방법을 사용하도록 해야 합니다.

### <a name="verification-code-from-mobile-app"></a>모바일 앱의 확인 코드

Authenticator 앱을 소프트웨어 토큰으로 사용하여 OATH 확인 코드를 생성할 수 있습니다. 사용자 이름 및 암호를 입력한 후 Authenticator 앱에서 제공한 코드를 로그인 인터페이스에 입력합니다. 확인 코드는 두 번째 인증 형식을 제공합니다.

사용자는 언제든지 사용할 수 있도록 구성된 Microsoft Authenticator 앱과 같은 인증자 애플리케이션 또는 최대 5개의 OATH 하드웨어 토큰을 조합할 수 있습니다.

> [!WARNING]
> 재설정에 한 가지 방법만 필요하고 셀프 서비스 암호 재설정을 사용하는 경우 최고 수준의 보안을 유지하기 위해 사용자에게 유일한 옵션으로 확인 코드만 제공됩니다.
>
> 두 방법이 필수인 경우 사용자가 사용하도록 설정한 다른 방법과 함께 알림 또는 확인 코드를 사용하여 재설정할 수 있습니다.

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO(Fast IDentity Online) Alliance는 공개 인증 표준의 수준을 올리고 암호 인증 형태의 사용자를 줄이도록 지원합니다. FIDO2는 WebAuthn(웹 인증) 표준을 통합하는 최신 표준입니다.

사용자 이름 및 암호 조합이 아닌 로그인 프롬프트에서 FIDO2 보안 키를 사용하려면 [암호 없는 FIDO2 보안 키 로그인(미리 보기)](howto-authentication-passwordless-security-key.md)을 참조하세요.

사용자는 로그인 인터페이스에서 FIDO2 보안 키를 등록한 다음에 인증의 기본 수단으로 선택할 수 있습니다. 이러한 FIDO2 보안 키는 일반적으로 USB 디바이스이지만 Bluetooth 또는 NFC를 사용할 수도 있습니다. 인증을 처리하는 하드웨어 디바이스를 사용하면 노출되거나 추측될 수 있는 암호가 없으므로 계정의 보안이 강화됩니다.

Azure AD의 FIDO2 보안 키는 현재 미리 보기 상태입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="oath-tokens"></a>OATH 토큰

OATH TOTP(시간 제약이 있는 일회성 암호)는 OTP(일회용 암호) 코드가 생성되는 방법을 지정하는 개방형 표준입니다. OATH TOTP는 코드를 생성하는 소프트웨어 또는 하드웨어를 사용하여 구현할 수 있습니다. Azure AD는 다른 코드 생성 표준인 OATH HOTP를 지원하지 않습니다.

### <a name="oath-software-tokens"></a>OATH 소프트웨어 토큰

소프트웨어 OATH 토큰은 일반적으로 Microsoft Authenticator 앱 및 다른 인증자 앱과 같은 애플리케이션입니다. Azure AD는 앱에 입력되고 각 OTP를 생성하는 데 사용되는 비밀 키 또는 시드를 생성합니다.

Authenticator 앱은 푸시 알림을 수행하도록 설정된 경우 자동으로 코드를 생성하므로 디바이스가 연결되지 않은 경우에도 사용자에게 백업이 있습니다. OATH TOTP를 사용하여 코드를 생성하는 타사 애플리케이션을 사용할 수도 있습니다.

일부 OATH TOTP 하드웨어 토큰은 프로그래밍 가능합니다. 즉, 사전 프로그래밍된 비밀 키 또는 시드가 제공되지 않습니다. 이러한 프로그래밍 가능한 하드웨어 토큰은 소프트웨어 토큰 설정 흐름에서 얻은 비밀 키 또는 시드를 사용하여 설정할 수 있습니다. 고객은 선택한 공급업체에서 이러한 토큰을 구매하고 해당 공급업체의 설정 프로세스에서 비밀 키 또는 시드를 사용할 수 있습니다.

### <a name="oath-hardware-tokens-preview"></a>OATH 하드웨어 토큰(미리 보기)

Azure AD는 30초 또는 60초마다 코드를 새로 고치는 OATH-TOTP SHA-1 토큰의 사용을 지원합니다. 고객은 자신이 선택한 공급업체에서 이러한 토큰을 구매할 수 있습니다.

OATH TOTP 하드웨어 토큰은 일반적으로 토큰에서 사전 프로그래밍된 비밀 키 또는 시드를 제공합니다. 이러한 키는 다음 단계에 설명된 대로 Azure AD에 입력해야 합니다. 비밀 키는 128자로 제한되며 일부 토큰과는 호환되지 않을 수 있습니다. 비밀 키에는 *a-z* 또는 *A-Z*와 *1-7*까지의 숫자만 포함할 수 있으며, *Base32*로 인코딩해야 합니다.

다시 시드될 수 있는 프로그래밍 가능한 OATH TOTP 하드웨어 토큰은 소프트웨어 토큰 설정 흐름에서 Azure AD를 사용하여 설정할 수도 있습니다.

OATH 하드웨어 토큰은 공개 미리 보기의 일부로 지원됩니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

![OATH 토큰을 MFA OATH 토큰 블레이드에 업로드](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

토큰이 확보되면 아래 예제와 같이 UPN, 일련 번호, 비밀 키, 시간 간격, 제조업체 및 모델이 포함된 CSV(쉼표로 구분된 값) 파일 형식으로 업로드해야 합니다.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> CSV 파일에 머리글 행을 포함해야 합니다.

CSV 파일로 올바르게 형식이 지정되면 관리자는 Azure Portal에 로그인하여 **Azure Active Directory > 보안 > MFA > OATH 토큰**으로 이동하고 CSV 파일을 업로드할 수 있습니다.

CSV 파일의 크기에 따라 처리하는 데 몇 분 정도가 소요될 수 있습니다. 현재 상태를 가져오려면 **새로 고침** 단추를 선택합니다. 파일에 오류가 있는 경우 오류가 나열된 CSV 파일을 다운로드하여 해결할 수 있습니다. 다운로드한 CSV 파일의 필드 이름은 업로드된 버전과 다릅니다.

오류가 모두 처리되면 관리자는 토큰에 대해 **활성화**를 선택하고 토큰에 표시된 OTP를 입력하여 각 키를 활성화할 수 있습니다.

사용자는 언제든지 사용할 수 있도록 구성된 Microsoft Authenticator 앱과 같은 인증자 애플리케이션 또는 최대 5개의 OATH 하드웨어 토큰을 조합할 수 있습니다.

## <a name="phone-options"></a>전화 옵션

문자 메시지를 사용하는 직접 인증의 경우 [SMS 기반 인증(미리 보기)에 대해 사용자를 구성하고 사용하도록 설정](howto-authentication-sms-signin.md)할 수 있습니다. SMS 기반 로그인은 일선 작업자에게 적합합니다. SMS 기반 로그인을 사용하면 사용자가 애플리케이션 및 서비스에 액세스하기 위해 사용자 이름과 암호를 몰라도 됩니다. 대신 사용자가 등록된 휴대폰 번호를 입력하고 확인 코드를 포함하는 문자 메시지를 받은 후 로그인 인터페이스에 입력합니다.

사용자는 Azure Multi-Factor Authentication 또는 SSPR(셀프 서비스 암호 재설정) 중에 사용되는 인증의 보조 형식처럼 휴대폰 또는 사무실 전화를 사용하여 자신을 확인할 수도 있습니다.

올바르게 작동하려면 전화번호가 *+CountryCode PhoneNumber* 형식으로 저장되어야 합니다(예: *+1 4251234567*).

> [!NOTE]
> 국가/지역 코드와 전화번호 사이에 공백이 필요합니다.
>
> 암호 재설정은 내선 번호를 지원하지 않습니다. *+1 4251234567X12345* 형식에서도 전화를 걸기 전에 내선 번호가 제거됩니다.

### <a name="mobile-phone-verification"></a>휴대폰 확인

Azure Multi-Factor Authentication 또는 SSPR의 경우 사용자는 로그인 인터페이스에 입력할 확인 코드가 포함된 문자 메시지를 수신하도록 선택하거나, 정의된 PIN 코드를 입력하라는 메시지와 함께 전화를 받을 수 있습니다.

사용자가 자신의 휴대폰 번호를 디렉터리에 표시하지 않는 대신 암호 재설정에는 사용하도록 하려면 관리자가 디렉터리에 해당 휴대폰 번호를 채우지 않아야 합니다. 대신 사용자가 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)에서 조합된 보안 정보 등록을 통해 **인증 전화** 특성을 채워야 합니다. 관리자는 사용자의 프로필에서 이 정보를 볼 수 있지만 다른 곳에 게시되지는 않습니다.

![전화번호가 채워진 인증 방법을 보여 주는 Azure Portal의 스크린샷](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft는 동일한 번호를 통한 일관적인 SMS 또는 음성 기반 Azure Multi-Factor Authentication 즉시 이행을 보장하지 않습니다. 사용자를 위해 SMS 이행성을 향상하기 위한 조정 작업을 수시로 진행하면서 언제든지 짧은 코드를 추가하거나 제거할 수 있습니다. Microsoft는 미국 및 캐나다 이외의 국가/지역에는 짧은 코드를 지원하지 않습니다.

#### <a name="text-message-verification"></a>문자 메시지 확인

SSPR 또는 Azure Multi-Factor Authentication 중에 문자 메시지를 확인하면 확인 코드를 포함한 SMS가 휴대폰 번호로 전송됩니다. 로그인 프로세스를 완료하려면 제공된 확인 코드를 로그인 인터페이스에 입력합니다.

#### <a name="phone-call-verification"></a>전화 통화 확인

SSPR 또는 Azure Multi-Factor Authentication 중에 전화 통화 확인을 사용하면 사용자가 등록한 전화번호로 자동 음성 전화가 걸려옵니다. 로그인 프로세스를 완료하려면 사용자에게 키패드에서 PIN 번호와 #을 입력하라는 메시지가 안내가 표시됩니다.

### <a name="office-phone-verification"></a>사무실 전화 확인

사무실 전화 특성은 Azure AD 관리자에 의해 관리되며 사용자가 스스로 등록할 수 없습니다.

SSPR 또는 Azure Multi-Factor Authentication 중에 전화 통화 확인을 사용하면 사용자가 등록한 전화번호로 자동 음성 전화가 걸려옵니다. 로그인 프로세스를 완료하려면 사용자에게 키패드에서 PIN 번호와 #을 입력하라는 메시지가 안내가 표시됩니다.

### <a name="troubleshooting-phone-options"></a>전화 옵션 문제 해결

Azure AD에 관한 전화 인증에 문제가 있는 경우 다음 문제 해결 단계를 검토합니다.

* 단일 디바이스에서 차단된 발신자 ID입니다.
   * 디바이스에 구성된 모든 차단된 숫자를 검토합니다.
* 전화번호 또는 국가/지역 코드가 잘못되었거나 개인 전화번호와 회사 전화번호 간에 혼란이 있습니다.
   * 사용자 개체 및 구성된 인증 방법의 문제를 해결합니다. 올바른 전화번호가 등록되었는지 확인합니다.
* 잘못된 PIN을 입력했습니다.
   * 사용자가 자신의 계정에 등록된 대로 올바른 PIN을 사용했는지 확인합니다.
* 통화가 음성 사서함으로 전달됩니다.
   * 사용자의 전화기가 켜져 있고 사용자의 지역에서 해당 서비스를 사용할 수 있는지 확인하거나 대체 방법을 사용합니다.
* 사용자가 차단됨
   * Azure AD 관리자가 Azure Portal에서 사용자의 차단을 해제하도록 합니다.
* 디바이스가 SMS에 가입되지 않았습니다.
   * 사용자가 디바이스에서 방법을 변경하거나 SMS를 활성화하도록 합니다.
* 검색된 휴대폰 입력 없음, DTMF 톤 누락 문제, 여러 디바이스에서 차단된 발신자 ID, 여러 디바이스에서 차단된 SMS 등의 결함 있는 통신 공급자
   * Microsoft는 여러 통신 공급자를 사용하여 인증을 위한 전화 통화와 SMS 메시지를 라우팅합니다. 위의 문제가 발생한 경우 사용자가 5분 이내에 5번 이상 방법을 시도하도록 하고 Microsoft 지원에 문의할 때 해당 사용자의 정보를 제공하도록 합니다.

## <a name="security-questions"></a>보안 질문

로그인 이벤트 중에는 본인 확인 질문을 인증 방법으로 사용하지 않습니다. 대신 SSPR(셀프 서비스 암호 재설정) 프로세스 중에는 본인 확인 질문을 사용하여 누구인지 확인할 수 있습니다. 관리자 계정은 SSPR에서 본인 확인 질문을 확인 방법으로 사용할 수 없습니다.

사용자가 SSPR에 등록하면 사용할 인증 방법을 선택하라는 메시지가 표시됩니다. 사용자가 본인 확인 질문을 사용하도록 선택하는 경우 메시지로 표시되는 일련의 질문에서 선택한 다음, 자신의 답변을 제공합니다.

![본인 확인 질문에 대한 인증 방법 및 옵션을 보여 주는 Azure Portal의 스크린샷](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> 보안 질문은 디렉터리에서 사용자 개체에 대해 비공개적으로 안전하게 저장되며 등록하는 동안 사용자만이 답변할 수 있습니다. 관리자는 사용자의 질문 또는 대답을 읽거나 수정할 수 없습니다.

어떤 사람들이 다른 사용자의 질문에 대한 답을 알고 있을 수도 있으므로 보안 질문은 다른 방법보다 덜 안전할 수 있습니다. SSPR에 본인 확인 질문을 사용하는 경우 다른 방법과 함께 사용하는 것이 좋습니다. 전화 또는 등록된 디바이스가 없는 경우에만 SSPR 프로세스 중에 Microsoft Authenticator 앱 또는 전화 인증을 사용하여 ID를 확인하고 사용자에게 본인 확인 질문을 선택하라는 메시지가 표시될 수 있습니다.

### <a name="predefined-questions"></a>미리 정의된 질문

다음의 미리 정의된 본인 확인 질문은 SSPR에서 확인 방법으로 사용할 수 있습니다. 해당 본인 확인 질문은 모두 사용자의 브라우저 로캘을 기반으로 하여 Office 365 언어의 전체 집합으로 번역 및 지역화됩니다.

* 배우자/파트너를 처음 만난 도시는 어디인가요?
* 부모님이 처음 만난 도시는 어디인가요?
* 가장 가까운 형제 자매가 사는 도시는 어디인가요?
* 아버지가 출생하신 도시는 어디인가요?
* 첫 직장이 있는 도시는 어디인가요?
* 어머니가 출생하신 도시는 어디인가요?
* 2000년에 새해를 맞은 도시는 어디인가요?
* 고등학교에서 가장 좋아했던 선생님의 성은 무엇인가요?
* 지원했지만 다니지 않은 대학의 이름은 무엇인가요?
* 첫 번째 결혼 피로연을 열었던 장소의 이름은 무엇인가요?
* 아버지의 중간 이름은 무엇인가요?
* 가장 좋아하는 음식은 무엇인가요?
* 외할머니의 성함은 무엇인가요?
* 어머니의 중간 이름은 무엇인가요?
* 가장 손위인 형제 자매의 생년월일은 언제인가요? (예: 1985년 11월)
* 가장 손위인 형제 자매의 중간 이름은 무엇인가요?
* 친할아버지의 성함은 무엇인가요?
* 가장 손아래인 형제 자매의 중간 이름은 무엇인가요?
* 6학년 때 다닌 학교는 어디인가요?
* 어린 시절 베스트 프렌드의 성함은 무엇인가요?
* 첫 번째 배우자의 성함은 무엇인가요?
* 가장 좋아하는 초등학교 선생님의 성은 무엇인가요?
* 첫 번째 자동차 또는 오토바이의 제조업체와 모델은 무엇인가요?
* 처음 다닌 학교의 이름은 무엇인가요?
* 태어난 병원의 이름은 무엇인가요?
* 어린 시절 첫 번째 집의 거리 이름은 무엇인가요?
* 어린 시절 영웅의 이름은 무엇인가요?
* 좋아하는 봉제 인형의 이름은 무엇인가요?
* 첫 번째 애완동물의 이름은 무엇인가요?
* 어린 시절 별명은 무엇인가요?
* 고등학교 때 가장 좋아한 스포츠는 무엇인가요?
* 첫 번째 직업은 무엇인가요?
* 어린 시절 전화 번호의 마지막 4자리는 무엇인가요?
* 어린 시절 자라서 되고 싶었던 것은 무엇인가요?
* 지금까지 만난 가장 유명한 사람은 누구인가요?

### <a name="custom-security-questions"></a>사용자 지정 보안 질문

유연성을 강화하기 위해 고유한 사용자 지정 본인 확인 질문을 정의할 수 있습니다. 사용자 지정 보안 질문은 최대 200자입니다.

사용자 지정 본인 확인 질문은 기본 본인 확인 질문의 경우와 같이 자동으로 지역화되지 않습니다. 모든 사용자 지정 질문은 사용자의 브라우저 로캘이 다른 경우에도 관리 사용자 인터페이스에 입력한 언어와 동일한 언어로 표시됩니다. 지역화된 질문이 필요한 경우 미리 정의된 질문을 사용해야 합니다.

### <a name="security-question-requirements"></a>보안 질문 요구 사항

기본 및 사용자 지정 본인 확인 질문에는 다음 요구 사항 및 제한 사항이 적용됩니다.

* 최소 응답 문자 제한은 3자입니다.
* 최대 응답 문자 제한은 40자입니다.
* 사용자는 동일한 질문에 대해 한 번만 응답할 수 있습니다.
* 사용자는 둘 이상의 질문에 대해 동일한 응답을 제공할 수 없습니다.
* 유니코드 문자를 포함한 모든 문자 집합을 사용하여 질문과 응답을 정의할 수 있습니다.
* 정의된 질문 수는 등록에 필요한 질문 수보다 크거나 같아야 합니다.

## <a name="email-address"></a>메일 주소

메일 주소는 직접 인증 방법으로 사용할 수 없습니다. 메일 주소는 SSPR(셀프 서비스 암호 재설정)에 대한 확인 옵션으로만 사용할 수 있습니다. SSPR 중에 메일 주소를 선택하면 사용자에게 메일을 보내 인증/확인 프로세스를 완료합니다.

SSPR에 등록하는 동안 사용자는 사용할 메일 주소를 제공합니다. 회사 계정이 아닌 다른 메일 계정을 사용하여 SSPR 중에 해당 메일에 액세스할 수 있는지 확인하는 것이 좋습니다.

## <a name="app-passwords"></a>앱 암호

일부 오래된 비 브라우저 앱에서는 인증 프로세스에서 일시 중지 또는 중단을 인식하지 못합니다. 사용자가 다단계 인증을 사용하도록 설정하고 이러한 오래된 비 브라우저 앱 중 하나를 사용하려고 시도하면 일반적으로 성공적으로 인증할 수 없습니다. 앱 암호를 사용하면 사용자는 중단 없이 오래된 비 브라우저 앱으로 계속 인증할 수 있습니다.

기본적으로 사용자가 앱 암호를 만들 수 없습니다. 사용자가 앱 암호를 만들 수 있도록 해야 할 경우 사용자의 Azure Multi-Factor Authentication 속성에 대한 ‘서비스 설정’에서 **사용자가 브라우저에 기반하지 않는 앱에 로그인하기 위해 앱 암호를 만들 수 있음**을 선택합니다.

![다단계 인증에 앱 암호의 사용자를 허용하는 서비스 설정을 보여 주는 Azure Portal의 스크린샷](media/concept-authentication-methods/app-password-authentication-method.png)

사용자별 MFA를 통하지 않고 조건부 액세스 정책을 사용하여 Azure Multi-Factor Authentication을 적용하는 경우 앱 암호를 만들 수 없습니다. 조건부 액세스 정책을 사용하여 액세스를 제어하는 최신 애플리케이션은 앱 암호가 필요하지 않습니다.

조직이 SSO(Single Sign-On)에 대해 Azure AD를 사용하여 페더레이션되고 Azure Multi-Factor Authentication을 사용하는 경우 다음 사항을 고려하세요.

* Azure AD로 앱 암호를 확인하므로 페더레이션을 우회합니다. 앱 암호를 설정할 때에만 페더레이션이 사용됩니다. 페더레이션된(SSO) 사용자의 경우 암호는 조직 ID에 저장됩니다. 사용자가 회사를 떠나는 경우 해당 정보는 DirSync를 사용하는 조직 ID에 유입되어야 합니다. 계정 사용 안 함 또는 삭제 이벤트는 동기화에 최대 3시간이 걸리며 Azure AD에서 앱 암호의 사용 안 함/삭제가 지연됩니다.
* 앱 암호를 사용할 경우 온-프레미스 클라이언트 액세스 제어 설정은 적용되지 않습니다.
* 온-프레미스 인증 로깅 또는 감사 기능은 앱 암호에 사용할 수 없습니다.
* 특정 고급 아키텍처 디자인은 Multi-Factor Authentication을 사용하는 경우 인증 위치에 따라 조직의 사용자 이름과 암호 및 앱 암호를 조합하여 사용할 필요가 있습니다.
    * 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 조직의 사용자 이름과 암호를 사용합니다.
    * Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호를 사용합니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [SSPR(셀프 서비스 암호 재설정)에 대한 자습서][tutorial-sspr] 및 [Azure Multi-Factor Authentication][tutorial-azure-mfa]을 참조하세요.

SSPR 개념에 대한 자세한 내용은 [Azure AD 셀프 서비스 암호 재설정 작동 방법][concept-sspr]을 참조하세요.

MFA 옵션에 대해 자세히 알아보려면 [Azure Multi-Factor Authentication 작동 방법][concept-mfa]을 참조하세요.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md