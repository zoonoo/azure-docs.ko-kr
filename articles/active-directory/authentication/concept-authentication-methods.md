---
title: 인증 방법 및 기능-Azure Active Directory
description: 로그인 이벤트를 개선 하 고 보호 하는 데 도움이 되는 Azure Active Directory에서 사용할 수 있는 다양 한 인증 방법 및 기능에 대해 알아봅니다.
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
ms.openlocfilehash: 3947bf0dcad598bf52a742c790a2f99538d6facb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116411"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Azure Active Directory에서 사용할 수 있는 인증 및 확인 방법은 무엇 인가요?

Azure Active Directory (Azure AD)의 계정에 대 한 로그인 환경의 일부로 사용자가 스스로 인증할 수 있는 다양 한 방법이 있습니다. 사용자 이름 및 암호는 사용자가 자격 증명을 제공 하는 가장 일반적인 방법입니다. Azure AD의 최신 인증 및 보안 기능을 사용 하면 기본 암호를 보완 하거나 추가 인증 방법으로 대체할 수 있습니다.

Azure AD의 사용자는 다음 인증 방법 중 하나를 사용 하 여 인증 하도록 선택할 수 있습니다.

* 기존 사용자 이름 및 암호
* 앱 암호 없는 로그인 Microsoft Authenticator
* OATH 하드웨어 토큰 또는 FIDO2 보안 키
* SMS 기반 암호 없는 로그인

Azure AD의 많은 계정이 SSPR (셀프 서비스 암호 재설정) 또는 Azure Multi-Factor Authentication에 대해 사용 하도록 설정 됩니다. 이러한 기능에는 전화 통화 또는 보안 질문과 같은 추가 인증 방법이 포함 됩니다. 사용자가 여러 인증 방법을 등록 하도록 요구 하는 것이 좋습니다. 한 가지 방법을 사용자가 사용할 수 없는 경우 다른 방법으로 인증 하도록 선택할 수 있습니다.

다음 표에서는 다양 한 시나리오에 사용할 수 있는 인증 또는 확인 방법을 간략하게 설명 합니다.

| 메서드 | 로그인 시 사용 | 확인 하는 동안 사용 |
| --- | --- | --- |
| [암호](#password) | 예 | MFA 및 SSPR |
| [Microsoft Authenticator 앱](#microsoft-authenticator-app) | 예(미리 보기) | MFA 및 SSPR |
| [FIDO2 보안 키 (미리 보기)](#fido2-security-keys) | 예 | MFA 전용 |
| [OATH 하드웨어 토큰 (미리 보기)](#oath-hardware-tokens) | 예 | SSPR 및 MFA |
| [SMS](#phone-options) | 예(미리 보기) | MFA 및 SSPR |
| [음성 통화](#phone-options) | 아니요 | MFA 및 SSPR |
| [보안 질문](#security-questions) | 아니요 | SSPR-전용 |
| [메일 주소](#email-address) | 아니요 | SSPR-전용 |
| [앱 암호](#app-passwords) | 아니요 | 특정 경우 MFA만 |

이 문서에서는 Azure AD에서 사용할 수 있는 다양 한 인증 및 확인 방법과 특정 제한 또는 제한 사항을 간략하게 설명 합니다.

![로그인 화면에서 사용하는 인증 방법](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>암호

Azure AD 암호는 주로 기본 인증 방법 중 하나입니다. 암호 인증 방법을 사용 하지 않도록 설정할 수 없습니다.

사용자가 로그인 하는 데 암호를 사용 하지 않는 경우 [SMS 기반 로그인](howto-authentication-sms-signin.md) 과 같은 인증 방법을 사용 하는 경우에도 암호는 사용 가능한 인증 방법으로 유지 됩니다.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 앱

Authenticator 앱은 Azure AD 회사 또는 학교 계정 또는 Microsoft 계정에 대 한 추가 보안 수준을 제공 하며 [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594)및 [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)에서 사용할 수 있습니다. Microsoft Authenticator 앱을 사용 하면 사용자가 로그인 중에 암호 없는 방식으로 인증 하거나 SSPR (셀프 서비스 암호 재설정) 또는 Azure Multi-Factor Authentication 이벤트 중에 추가 확인 옵션으로 인증할 수 있습니다.

사용자는 승인 또는 거부를 위해 모바일 앱을 통해 알림을 받거나, 인증자 앱을 사용 하 여 로그인 인터페이스에 입력할 수 있는 OATH 확인 코드를 생성할 수 있습니다. 알림 및 확인 코드를 모두 사용 하는 경우 인증자 앱을 등록 하는 사용자는 두 방법 중 하나를 사용 하 여 해당 id를 확인할 수 있습니다.

사용자 이름 및 암호 조합이 아닌 로그인 프롬프트에서 Authenticator 앱을 사용 하려면 [Microsoft Authenticator 앱 (미리 보기)을 사용 하 여 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md)을 참조 하세요.

> [!NOTE]
> SSPR를 사용 하도록 설정 하는 경우 사용자에 게 모바일 앱을 등록 하는 옵션이 없습니다. 대신, 사용자는에서 사용자의 모바일 앱을 등록 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 하거나에서 결합 된 보안 정보 등록의 일부로 등록할 수 있습니다 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>모바일 앱을 통한 알림

Authenticator 앱은 스마트폰 또는 태블릿에서 알림을 푸시하여 계정에 대 한 무단 액세스를 방지 하 고 사기성 트랜잭션을 중지 하는 데 도움이 될 수 있습니다. 사용자가 알림을 확인 하 고 합법적인 경우 **확인**을 선택 합니다. 그렇지 않으면 **Deny**를 선택할 수 있습니다.

![로그인 프로세스를 완료 하기 위한 Authenticator 앱 알림 예의 웹 브라우저 프롬프트 스크린샷](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> 조직에서 근무 하는 직원이 있거나 중국으로 여행 하는 경우 Android 장치에서 *모바일 앱 방법을 통한 알림이* 해당 국가에서 작동 하지 않습니다. 해당 사용자에 대해 대체 인증 방법을 사용할 수 있도록 설정 해야 합니다.

### <a name="verification-code-from-mobile-app"></a>모바일 앱의 확인 코드

인증 앱은 OATH 확인 코드를 생성 하는 소프트웨어 토큰으로 사용 될 수 있습니다. 사용자 이름 및 암호를 입력 한 후 인증 앱에서 제공 하는 코드를 로그인 인터페이스에 입력 합니다. 확인 코드는 두 번째 인증 형식을 제공합니다.

사용자는 언제 든 지 사용 하도록 구성 된 Microsoft Authenticator 앱과 같은 최대 5 개의 OATH 하드웨어 토큰 또는 인증자 응용 프로그램을 조합 하 여 사용할 수 있습니다.

> [!WARNING]
> 재설정을 위해 하나의 메서드만 필요한 경우 셀프 서비스 암호 재설정에 대 한 가장 높은 수준의 보안을 보장 하기 위해 사용자에 게 제공 되는 유일한 옵션은 확인 코드입니다.
>
> 두 가지 방법을 사용 해야 하는 경우 사용자는 다른 설정 된 메서드 외에도 알림 또는 확인 코드를 사용 하 여 다시 설정할 수 있습니다.

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO (Fast IDentity Online) 동맹은 오픈 인증 표준의 수준을 올리고 인증의 형태로 암호의 사용자를 줄이는 데 도움이 됩니다. FIDO2는 웹 인증 (WebAuthn) 표준을 통합 하는 최신 표준입니다.

사용자 이름 및 암호 조합이 아닌 로그인 프롬프트에서 FIDO2 보안 키를 사용 하려면 [암호 없는 FIDO2 보안 키 로그인 (미리 보기) 사용](howto-authentication-passwordless-security-key.md)을 참조 하세요.

사용자는 로그인 인터페이스에서 FIDO2 보안 키를 등록 한 다음 인증의 기본 수단으로 선택할 수 있습니다. 이러한 FIDO2 보안 키는 일반적으로 USB 장치 이지만 Bluetooth 또는 NFC를 사용할 수도 있습니다. 인증을 처리 하는 하드웨어 장치를 사용 하면 노출 되거나 추측 될 수 있는 암호가 없으므로 계정의 보안이 강화 됩니다.

Azure AD의 FIDO2 보안 키는 현재 미리 보기 상태입니다. 미리 보기에 대 한 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="oath-hardware-tokens"></a>OATH 하드웨어 토큰

OATH는 OTP(일회성 암호) 코드 생성 방법을 지정하는 공개 표준입니다. Azure AD는 30 초 또는 60 초의 OATH-TOTP SHA-1 토큰 사용을 지원 합니다. 고객은 선택한 공급 업체 로부터 이러한 토큰을 구매할 수 있습니다.

비밀 키는 모든 토큰과 호환 되지 않을 수 있는 128 자로 제한 됩니다. 비밀 키에 *는* 문자 a-z 또는 a-z 및 *1-7*숫자만 사용할 *수 있으며* , *Base32*로 인코딩해야 합니다.

Azure AD의 OATH 하드웨어 토큰은 현재 미리 보기로 제공 됩니다. 미리 보기에 대 한 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

![OATH 토큰을 MFA OATH 토큰 창에 업로드](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

토큰을 획득 한 후에는 다음 예제와 같이 UPN, 일련 번호, 비밀 키, 시간 간격, 제조업체 및 모델을 포함 하 여 쉼표로 구분 된 값 (CSV) 파일 형식으로 업로드 해야 합니다.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> CSV 파일에 머리글 행이 포함 되어 있는지 확인 합니다.

Csv 파일로 올바른 형식이 지정 되 면 관리자가 Azure Portal에 로그인 하 여 **Azure Active Directory**  >  **Security**  >  **MFA**  >  **OATH 토큰**으로 이동 하 고 결과 CSV 파일을 업로드할 수 있습니다.

CSV 파일의 크기에 따라 처리하는 데 몇 분 정도가 소요될 수 있습니다. **새로 고침** 단추를 선택 하 여 현재 상태를 가져옵니다. 파일에 오류가 있는 경우 해결할 오류를 나열 하는 CSV 파일을 다운로드할 수 있습니다. 다운로드 한 CSV 파일의 필드 이름은 업로드 된 버전과 다릅니다.

오류가 해결 되 면 관리자는 토큰에 대해 **활성화** 를 선택 하 고 토큰에 표시 되는 OTP를 입력 하 여 각 키를 활성화할 수 있습니다.

사용자는 언제 든 지 사용 하도록 구성 된 Microsoft Authenticator 앱과 같은 최대 5 개의 OATH 하드웨어 토큰 또는 인증자 응용 프로그램을 조합 하 여 사용할 수 있습니다.

## <a name="phone-options"></a>전화 옵션

문자 메시지를 사용 하는 직접 인증의 경우 [SMS 기반 인증 (미리 보기)에 대해 사용자를 구성 하 고 사용 하도록 설정할](howto-authentication-sms-signin.md)수 있습니다. SMS 기반 로그인은 프런트 라인 작업자에 게 적합 합니다. SMS 기반 로그인을 사용 하면 사용자가 응용 프로그램 및 서비스에 액세스 하기 위해 사용자 이름과 암호를 몰라도 됩니다. 대신 사용자가 등록 된 휴대폰 번호를 입력 하 고, 확인 코드를 포함 하는 문자 메시지를 받은 후 로그인 인터페이스에 입력 합니다.

사용자는 또한 Azure Multi-Factor Authentication 또는 SSPR (셀프 서비스 암호 재설정) 중에 사용 되는 인증의 보조 양식으로 휴대폰 또는 사무실 전화를 사용 하 여 자신을 확인할 수 있습니다.

제대로 작동 하려면 전화 번호는 + *1 4251234567*과 같이 *+ CountryCode PhoneNumber*형식 이어야 합니다.

> [!NOTE]
> 국가 번호와 전화 번호 사이에 공백이 필요합니다.
>
> 암호 재설정은 전화 내선 번호를 지원 하지 않습니다. *+ 1 4251234567X12345* 형식 에서도 호출이 배치 되기 전에 확장이 제거 됩니다.

### <a name="mobile-phone-verification"></a>휴대폰 확인

Azure Multi-Factor Authentication 또는 SSPR의 경우 사용자는 인증 코드를 사용 하 여 로그인 인터페이스에 입력 하는 문자 메시지를 수신 하도록 선택 하거나, 정의 된 pin 코드를 입력 하 라는 메시지와 함께 전화 통화를 받을 수 있습니다.

사용자가 휴대폰 번호를 디렉터리에 표시 하지 않지만 암호 재설정에 사용 하려는 경우 관리자는 디렉터리의 전화 번호를 채우지 않아야 합니다. 대신 사용자는에서 결합 된 보안 정보 등록을 통해 **인증 전화** 특성을 채워야 합니다 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . 관리자는 사용자의 프로필에서 이 정보를 볼 수 있지만 다른 곳에 게시되지는 않습니다.

![전화 번호가 채워진 인증 방법을 보여 주는 Azure Portal의 스크린샷](media/concept-authentication-methods/user-authentication-methods.png)

Microsoft는 동일한 번호를 사용 하 여 일관 된 SMS 또는 음성 기반 Azure Multi-Factor Authentication 프롬프트 배달을 보장 하지 않습니다. 사용자가 관심을 내는 경우에 따라 경로를 조정 하 여 SMS 배달 기능을 개선 하기 위해 언제 든 지 짧은 코드를 추가 하거나 제거할 수 있습니다. Microsoft는 미국 및 캐나다 외에도 국가/지역에 대 한 짧은 코드를 지원 하지 않습니다.

#### <a name="text-message-verification"></a>문자 메시지 확인

SSPR 또는 Azure Multi-Factor Authentication 중에 문자 메시지를 확인 하면 SMS가 확인 코드를 포함 하는 휴대폰 번호로 전송 됩니다. 로그인 프로세스를 완료 하려면 제공 된 확인 코드가 로그인 인터페이스에 입력 됩니다.

#### <a name="phone-call-verification"></a>전화 통화 확인

SSPR 또는 Azure Multi-Factor Authentication 중에 전화 통화 확인을 사용 하 여 사용자가 등록 한 전화 번호로 자동 음성 호출이 수행 됩니다. 로그인 프로세스를 완료 하려면 사용자에 게 해당 키패드의 pin 번호와 #을 입력 하 라는 메시지가 표시 됩니다.

### <a name="office-phone-verification"></a>사무실 전화 확인

Office phone 특성은 Azure AD 관리자에 의해 관리 되며 사용자 스스로 등록할 수 없습니다.

SSPR 또는 Azure Multi-Factor Authentication 중에 전화 통화 확인을 사용 하 여 사용자가 등록 한 전화 번호로 자동 음성 호출이 수행 됩니다. 로그인 프로세스를 완료 하려면 사용자에 게 해당 키패드의 pin 번호와 #을 입력 하 라는 메시지가 표시 됩니다.

### <a name="troubleshooting-phone-options"></a>전화 옵션 문제 해결

Azure AD에 대 한 전화 인증에 문제가 있는 경우 다음 문제 해결 단계를 검토 합니다.

* 단일 장치에서 차단 된 호출자 ID입니다.
   * 장치에 구성 된 차단 된 숫자를 검토 합니다.
* 전화 번호 또는 잘못 된 국가 코드 또는 개인 전화번호와 회사 전화 번호 간의 혼동.
   * 사용자 개체 및 구성 된 인증 방법 문제를 해결 합니다. 올바른 전화 번호가 등록 되었는지 확인 합니다.
* 잘못 된 PIN을 입력 했습니다.
   * 사용자가 자신의 계정에 대해 등록 된 대로 올바른 PIN을 사용 했는지 확인 합니다.
* 음성으로 전달 된를 호출 합니다.
   * 사용자가 전화를 켜고 해당 지역에서 해당 서비스를 사용할 수 있는지 확인 하거나 대체 방법을 사용 합니다.
* 사용자가 차단됨
   * Azure AD 관리자가 Azure Portal에서 사용자의 차단을 해제 하도록 합니다.
* SMS가 장치에서 구독 하지 않습니다.
   * 사용자가 장치에서 메서드를 변경 하거나 SMS를 활성화 하도록 합니다.
* 휴대폰 입력 없음, DTMF 누락 문제, 여러 장치에서 차단 된 호출자 ID, 여러 장치에서 차단 된 SMS 등의 잘못 된 통신 공급자
   * Microsoft는 여러 통신 공급자를 사용 하 여 인증을 위해 전화 통화와 SMS 메시지를 라우팅합니다. 위의 문제가 발생 한 경우 사용자가 5 분 이내에 5 번 이상 메서드를 사용 하 고 Microsoft 지원에 문의할 때 해당 사용자의 정보를 사용할 수 있도록 합니다.

## <a name="security-questions"></a>보안 질문

로그인 이벤트 중에는 보안 질문을 인증 방법으로 사용 하지 않습니다. 대신 SSPR (셀프 서비스 암호 재설정) 프로세스 중에 보안 질문을 사용 하 여 사용자가 누구 인지 확인할 수 있습니다. 관리자 계정은 SSPR를 사용 하는 확인 방법으로 보안 질문을 사용할 수 없습니다.

사용자가 SSPR에 등록 하면 사용할 인증 방법을 선택 하 라는 메시지가 표시 됩니다. 사용자가 보안 질문을 사용 하도록 선택 하는 경우 질문 집합에서 선택 하 여 질문을 한 다음 자체 답변을 제공 합니다.

![인증 방법 및 보안 질문에 대 한 옵션을 보여 주는 Azure Portal의 스크린샷](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> 보안 질문은 디렉터리에서 사용자 개체에 대해 비공개적으로 안전하게 저장되며 등록하는 동안 사용자만이 답변할 수 있습니다. 관리자가 사용자의 질문 또는 대답을 읽거나 수정할 수 있는 방법은 없습니다.

어떤 사람들이 다른 사용자의 질문에 대한 답을 알고 있을 수도 있으므로 보안 질문은 다른 방법보다 덜 안전할 수 있습니다. SSPR와 함께 보안 질문을 사용 하는 경우 다른 방법과 함께 사용 하는 것이 좋습니다. SSPR 프로세스 중에 Microsoft Authenticator 앱 또는 전화 인증을 사용 하 여 id를 확인 하 고, 해당 사용자에 게 전화 또는 등록 된 장치가 없는 경우에만 보안 질문을 선택 하 라는 메시지가 표시 될 수 있습니다.

### <a name="predefined-questions"></a>미리 정의된 질문

다음 미리 정의 된 보안 질문은 SSPR를 사용 하 여 확인 방법으로 사용할 수 있습니다. 이러한 모든 보안 질문은 사용자의 브라우저 로캘을 기반으로 하는 Office 365 언어의 전체 집합으로 변환 되 고 지역화 됩니다.

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
* 가장 손위인 형제 자매의 생년월일은 언제인가요? (예: 11 월 1985)
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

추가 유연성을 위해 고유한 사용자 지정 보안 질문을 정의할 수 있습니다. 사용자 지정 보안 질문은 최대 200자입니다.

사용자 지정 보안 질문은 기본 보안 질문의 경우와 같이 자동으로 지역화 되지 않습니다. 사용자 지정 질문은 사용자의 브라우저 로캘이 다른 경우에도 관리 사용자 인터페이스에 입력 하는 것과 동일한 언어로 표시 됩니다. 지역화된 질문이 필요한 경우 미리 정의된 질문을 사용해야 합니다.

### <a name="security-question-requirements"></a>보안 질문 요구 사항

기본 및 사용자 지정 보안 질문에 대 한 다음 요구 사항 및 제한 사항이 적용 됩니다.

* 최소 응답 문자 제한은 3자입니다.
* 최대 응답 문자 제한은 40자입니다.
* 사용자는 동일한 질문에 대해 한 번만 응답할 수 있습니다.
* 사용자는 둘 이상의 질문에 대해 동일한 응답을 제공할 수 없습니다.
* 유니코드 문자를 포함한 모든 문자 집합을 사용하여 질문과 응답을 정의할 수 있습니다.
* 정의된 질문 수는 등록에 필요한 질문 수보다 크거나 같아야 합니다.

## <a name="email-address"></a>전자 메일 주소

전자 메일 주소는 직접 인증 방법으로 사용할 수 없습니다. 전자 메일 주소는 SSPR (셀프 서비스 암호 재설정)에 대 한 확인 옵션 으로만 사용할 수 있습니다. SSPR 중에 전자 메일 주소를 선택 하면 사용자에 게 전자 메일을 보내 인증/확인 프로세스를 완료 합니다.

SSPR에 등록 하는 동안 사용자는 사용할 전자 메일 주소를 제공 합니다. 회사 계정이 아닌 다른 전자 메일 계정을 사용 하 여 SSPR 중에 액세스할 수 있는지 확인 하는 것이 좋습니다.

## <a name="app-passwords"></a>앱 암호

이전에는 브라우저가 아닌 특정 앱이 인증 프로세스에서 일시 중지 또는 중단을 이해 하지 못합니다. 사용자가 multi-factor authentication을 사용 하도록 설정 하 고 이러한 이전의 비 브라우저 앱 중 하나를 사용 하려고 하면 일반적으로 성공적으로 인증할 수 없습니다. 사용자는 앱 암호를 사용 하 여 중단 없이 이전의 브라우저가 아닌 앱으로 계속 인증할 수 있습니다.

기본적으로 사용자가 앱 암호를 만들 수 없습니다. 사용자가 앱 암호를 만들 수 있도록 허용 해야 하는 경우 사용자가 **앱 암호를 만들 수 있도록 허용** 을 선택 하 여 사용자의 Azure Multi-Factor Authentication 속성에 대 한 *서비스 설정* 아래에서 비 브라우저 앱에 로그인 합니다.

![앱 암호의 사용자를 허용 하는 multi-factor authentication에 대 한 서비스 설정을 보여 주는 Azure Portal의 스크린샷](media/concept-authentication-methods/app-password-authentication-method.png)

사용자 단위 MFA를 통하지 않고 조건부 액세스 정책을 사용 하 여 Azure Multi-Factor Authentication를 적용 하는 경우 앱 암호를 만들 수 없습니다. 조건부 액세스 정책을 사용 하 여 액세스를 제어 하는 최신 응용 프로그램에는 앱 암호가 필요 하지 않습니다.

조직에서 Azure AD를 사용 하 여 SSO (Single Sign-On)를 페더레이션 하 고 Azure Multi-Factor Authentication를 사용 하는 경우 다음 사항을 고려해 야 합니다.

* Azure AD에서 앱 암호를 확인 하므로 페더레이션을 무시 합니다. 앱 암호를 설정할 때에만 페더레이션이 사용됩니다. 페더레이션된(SSO) 사용자의 경우 암호는 조직 ID에 저장됩니다. 사용자가 회사를 떠나는 경우 해당 정보는 DirSync를 사용하는 조직 ID에 유입되어야 합니다. 계정 사용 안 함 또는 삭제 이벤트를 동기화 하는 데 최대 3 시간이 걸릴 수 있으며,이로 인해 Azure AD에서 앱 암호의 사용 안 함/삭제가 지연 됩니다.
* 온-프레미스 클라이언트 Access Control 설정은 앱 암호에서 적용 되지 않습니다.
* 앱 암호에는 온-프레미스 인증 로깅 또는 감사 기능을 사용할 수 없습니다.
* 특정 고급 아키텍처 디자인에서는 인증 된 위치에 따라 다단계 인증을 사용 하는 경우 조직의 사용자 이름과 암호 및 앱 암호를 조합 하 여 사용 해야 할 수 있습니다.
    * 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 조직의 사용자 이름과 암호를 사용합니다.
    * Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호를 사용합니다.

## <a name="next-steps"></a>다음 단계

시작하려면 [SSPR(셀프 서비스 암호 재설정)에 대한 자습서][tutorial-sspr] 및 [Azure Multi-Factor Authentication][tutorial-azure-mfa]을 참조하세요.

SSPR 개념에 대 한 자세한 내용은 [AZURE AD 셀프 서비스 암호 재설정 작동 방식][concept-sspr]을 참조 하세요.

MFA 개념에 대해 자세히 알아보려면 [Azure Multi-Factor Authentication 작동 방식][concept-mfa]을 참조 하세요.

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md