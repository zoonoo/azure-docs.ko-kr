---
title: 기본 새로 고침 토큰 (PRT) 및 Azure AD-Azure Active Directory
description: 의 역할은 무엇 이며 Azure Active Directory에서 PRT (주 새로 고침 토큰)를 어떻게 관리 하나요?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b9240b863eef4d460cd8d3a47304fb96ffb4bc8
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917784"
---
# <a name="what-is-a-primary-refresh-token"></a>기본 새로 고침 토큰은 무엇 인가요?

PRT (주 새로 고침 토큰)는 Windows 10, iOS 및 Android 장치에서 Azure AD 인증의 핵심 아티팩트입니다. 이러한 장치에 사용 되는 응용 프로그램 전체에서 SSO (Single Sign-On)를 사용 하도록 설정 하기 위해 Microsoft의 자사 토큰 브로커에 특별히 발급 된 JWT (JSON Web Token)입니다. 이 문서에서는 Windows 10 장치에서 PRT를 발급, 사용 및 보호 하는 방법에 대 한 세부 정보를 제공 합니다.

이 문서에서는 Azure AD에서 사용할 수 있는 여러 장치 상태 및 Single Sign-On Windows 10에서 작동 하는 방식에 대해 이미 알고 있다고 가정 합니다. Azure AD의 장치에 대 한 자세한 내용은 [Azure Active Directory의 장치 관리용 이란?](overview.md) 문서를 참조 하세요.

## <a name="key-terminology-and-components"></a>주요 용어 및 구성 요소

다음 Windows 구성 요소는 PRT를 요청 하 고 사용 하는 주요 역할을 합니다.

* CloudAP ( **Cloud Authentication provider** ): CloudAP는 windows 10 장치에 로그인 하는 사용자를 확인 하는 windows 로그인에 대 한 최신 인증 공급자입니다. CloudAP는 id 공급자가 해당 id 공급자의 자격 증명을 사용 하 여 Windows에 인증할 수 있도록 하는 플러그 인 프레임 워크를 제공 합니다.
* **웹 계정 관리자** (WAM): WAM은 Windows 10 장치의 기본 토큰 브로커입니다. 또한 WAM는 id 공급자가 해당 id 공급자를 사용 하는 응용 프로그램에 대해 SSO를 구성 하 고 사용할 수 있는 플러그 인 프레임 워크를 제공 합니다.
* **AZURE Ad CloudAP 플러그 인**: Windows 로그인 중에 azure ad를 사용 하 여 사용자 자격 증명을 확인 하는 CloudAP 프레임 워크를 기반으로 하는 azure ad 특정 플러그 인입니다.
* **AZURE AD WAM 플러그 인**: 인증을 위해 azure ad를 사용 하는 응용 프로그램에 SSO를 사용 하는 WAM 프레임 워크를 기반으로 하는 azure ad 특정 플러그 인입니다.
* **Dsreg**: 모든 장치 상태에 대 한 장치 등록 프로세스를 처리 하는 Windows 10의 Azure AD 관련 구성 요소입니다.
* **신뢰할 수 있는 플랫폼 모듈** (tpm): tpm은 장치에 기본 제공 되는 하드웨어 구성 요소로, 사용자 및 장치 암호에 대 한 하드웨어 기반 보안 기능을 제공 합니다. 자세한 내용은 [신뢰할 수 있는 플랫폼 모듈 기술 개요](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview)문서에서 찾을 수 있습니다.

## <a name="what-does-the-prt-contain"></a>PRT에는 무엇이 포함 되나요?

PRT는 일반적으로 모든 Azure AD 새로 고침 토큰에 포함 된 클레임을 포함 합니다. 또한 일부 장치 관련 클레임은 PRT에 포함 되어 있습니다. 다음과 같습니다.

* **장치 ID**: PRT는 특정 장치에서 사용자에 게 발급 됩니다. 장치 ID 클레임 `deviceID`는 사용자가 PRT를 발급 한 장치를 결정 합니다. 이 클레임은 나중에 PRT를 통해 가져온 토큰에 발급 됩니다. 장치 ID 클레임은 장치 상태 또는 규정 준수에 따라 조건부 액세스에 대 한 권한 부여를 결정 하는 데 사용 됩니다.
* **세션 키**: 세션 키는 Azure AD 인증 서비스에 의해 생성 되 고 PRT의 일부로 발급 되는 암호화 된 대칭 키입니다. 세션 키는 PRT를 사용 하 여 다른 응용 프로그램에 대 한 토큰을 가져오는 경우 소유 증명 역할을 합니다.

### <a name="can-i-see-whats-in-a-prt"></a>PRT의 기능을 확인할 수 있나요?

PRT는 모든 클라이언트 구성 요소에서 콘텐츠를 알 수 없는 Azure AD에서 전송 된 불투명 blob입니다. PRT 안에 있는 항목은 볼 수 없습니다.

## <a name="how-is-a-prt-issued"></a>PRT는 어떻게 실행 되나요?

장치 등록은 Azure AD에서 장치 기반 인증을 위한 필수 구성 요소입니다. PRT는 등록 된 장치 에서만 사용자에 게 발급 됩니다. 장치 등록에 대 한 자세한 내용은 [비즈니스용 Windows Hello 및 장치 등록](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)문서를 참조 하세요. 장치를 등록 하는 동안 dsreg 구성 요소는 두 가지 암호화 키 쌍 집합을 생성 합니다.

* 장치 키 (dkpub/dkpub)
* 전송 키 (tkpub/tkpub)

장치에 유효 하 고 작동 하는 TPM이 있는 상태에서 개인 키는 장치의 TPM에 바인딩되고, 장치 등록 프로세스 중에는 공개 키가 Azure AD로 전송 됩니다. 이러한 키는 PRT 요청 중에 장치 상태를 확인 하는 데 사용 됩니다.

PRT는 Windows 10 장치에서 사용자 인증을 수행 하는 동안 다음과 같은 두 가지 시나리오에서 실행 됩니다.

* **AZURE ad 조인** 또는 **하이브리드 azure ad 조인**: 사용자가 조직 자격 증명을 사용 하 여 로그인 하면 WINDOWS 로그온 중에 PRT가 발급 됩니다. PRT는 암호 및 비즈니스용 Windows Hello와 같은 모든 Windows 10 지원 자격 증명으로 실행 됩니다. 이 시나리오에서 Azure AD CloudAP 플러그 인은 PRT의 기본 인증 기관입니다.
* **AZURE AD 등록 장치**: 사용자가 Windows 10 장치에 보조 회사 계정을 추가할 때 PRT가 발급 됩니다. 사용자는 두 가지 방법으로 Windows 10에 계정을 추가할 수 있습니다.  
   * 앱에 로그인 한 후이 **장치에서이 계정 사용** 프롬프트를 통해 계정 추가 (예: Outlook)
   * **설정** > **계정의** 계정 추가 > **회사 또는 학교에 액세스** > **연결**

Azure ad에서 등록 된 장치 시나리오에서 Azure ad WAM 플러그 인은이 Azure AD 계정으로 Windows 로그온이 발생 하지 않기 때문에 PRT의 기본 인증 기관입니다.

> [!NOTE]
> 타사 id 공급자는 WS 신뢰 프로토콜을 지원 하 여 Windows 10 장치에서 PRT 발급을 사용 하도록 설정 해야 합니다. WS-TRUST를 사용 하지 않으면 하이브리드 Azure AD 조인 또는 Azure AD 조인 장치에서 사용자에 게 PRT를 발급할 수 없습니다.

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT의 수명은 무엇입니까?

일단 실행 되 면 PRT는 14 일 동안 유효 하며 사용자가 장치를 적극적으로 사용 하는 동안에는 계속 갱신 됩니다.  

## <a name="how-is-a-prt-used"></a>PRT는 어떻게 사용 되나요?

PRT는 Windows의 두 가지 주요 구성 요소에서 사용 됩니다.

* **AZURE Ad CloudAP 플러그 인**: Windows 로그인 중에 Azure ad CloudAP 플러그 인은 사용자가 제공한 자격 증명을 사용 하 여 azure AD에서 PRT를 요청 합니다. 또한 사용자가 인터넷 연결에 액세스할 수 없는 경우 캐시 된 로그인을 사용 하도록 PRT를 캐시 합니다.
* **AZURE AD WAM 플러그 인**: 사용자가 응용 프로그램에 액세스 하려고 하면 AZURE ad WAM 플러그 인은 PRT를 사용 하 여 Windows 10에서 SSO를 사용 하도록 설정 합니다. Azure AD WAM 플러그 인은 PRT를 사용 하 여 토큰 요청에 대해 WAM를 사용 하는 응용 프로그램에 대 한 새로 고침 및 액세스 토큰을 요청 합니다. 또한 브라우저 요청에 PRT를 삽입 하 여 브라우저에서 SSO를 사용 하도록 설정 합니다. Windows 10의 Browser SSO는 Microsoft Edge (기본적으로) 및 Chrome (Windows 10 계정 또는 Office Online 확장을 통해)에서 지원 됩니다.

## <a name="how-is-a-prt-renewed"></a>PRT는 어떻게 갱신 되나요?

PRT는 다음과 같은 두 가지 방법으로 갱신 됩니다.

* **4 시간 마다 AZURE AD CloudAP 플러그 인**: CloudAP 플러그 인은 Windows 로그인 중 4 시간 마다 PRT를 갱신 합니다. 사용자가 해당 시간 동안 인터넷에 연결 되지 않은 경우 CloudAP 플러그 인은 장치가 인터넷에 연결 된 후에 PRT를 갱신 합니다.
* **앱 토큰 요청 중 AZURE AD WAM 플러그 인**: WAM 플러그 인은 응용 프로그램에 대 한 자동 토큰 요청을 사용 하도록 설정 하 여 Windows 10 장치에서 SSO를 활성화 합니다. WAM 플러그 인은 다음 두 가지 방법으로 이러한 토큰 요청 중에 PRT를 갱신할 수 있습니다.
   * 앱 요청은 자동으로 액세스 토큰에 대해 WAM 해당 앱에 사용할 수 있는 새로 고침 토큰이 없습니다. 이 경우 WAM는 PRT를 사용 하 여 앱에 대 한 토큰을 요청 하 고 응답에서 새 PRT를 다시 가져옵니다.
   * 앱이 액세스 토큰에 대해 WAM 요청 하지만 PRT가 잘못 되었거나 azure AD에 추가 권한 부여가 필요 합니다 (예: Azure Multi-Factor Authentication). 이 시나리오에서 WAM는 사용자가 다시 인증 하거나 추가 확인을 제공 하도록 요구 하는 대화형 로그온을 시작 하 고 인증에 성공 하면 새 PRT를 발급 합니다.

### <a name="key-considerations"></a>주요 고려 사항

* PRT는 네이티브 앱 인증 중에만 실행 되 고 갱신 됩니다. PRT는 브라우저 세션 중에 갱신 되거나 발급 되지 않습니다.
* Azure AD 조인 및 하이브리드 Azure AD 조인 장치에서 CloudAP 플러그 인은 PRT의 기본 인증 기관입니다. WAM 기반 토큰 요청 중에 PRT가 갱신 되 면 prt가 CloudAP 플러그 인으로 다시 전송 됩니다. 그러면 해당 prt를 수락 하기 전에 Azure AD를 사용 하 여 PRT의 유효성을 확인 합니다.

## <a name="how-is-the-prt-protected"></a>PRT는 어떻게 보호 되나요?

PRT는 사용자가 로그인 한 장치에 바인딩하여 보호 됩니다. Azure AD 및 Windows 10은 다음 방법을 통해 PRT 보호를 사용 하도록 설정 합니다.

* **처음 로그인 하는 동안**: 장치를 등록 하는 동안 생성 된 암호 키를 사용 하 여 서명 요청에 의해 PRT가 발급 됩니다. 유효 하 고 작동 하는 TPM이 있는 장치에서 장치 키는 악의적인 액세스를 방지 하는 TPM에 의해 보호 됩니다. 해당 하는 장치 키 서명의 유효성을 검사할 수 없는 경우에는 PRT가 실행 되지 않습니다.
* **토큰 요청 및 갱신 중**: PRT가 발급 되 면 Azure AD는 장치에 암호화 된 세션 키도 발급 합니다. 이는 생성 되어 장치 등록의 일부로 Azure AD로 전송 되는 tkpub (공개 전송 키)로 암호화 됩니다. 이 세션 키는 TPM으로 보호 되는 tkpriv (개인 전송 키)로만 암호 해독할 수 있습니다. 세션 키는 Azure AD로 전송 된 모든 요청에 대 한 POP (소유 증명) 키입니다.  세션 키는 TPM에 의해 보호 되며 다른 OS 구성 요소는이 키에 액세스할 수 없습니다. 토큰 요청 또는 PRT 갱신 요청은 TPM을 통해이 세션 키로 안전 하 게 서명 되므로 변조는 불가능 합니다. Azure AD는 해당 세션 키로 서명 되지 않은 장치의 모든 요청을 무효화 합니다.

악의적인 행위자는 TPM으로 이러한 키의 보안을 설정 하 여 키를 도용 하거나, 공격자가 장치를 물리적으로 소유 하는 경우에도 TPM에 액세스할 수 없으므로 다른 곳에서 PRT를 재생할 수 없습니다.  따라서 TPM을 사용 하면 자격 증명 도난에 대 한 Azure AD 조인, 하이브리드 Azure AD 조인 및 Azure AD 등록 장치의 보안이 크게 향상 됩니다. 성능 및 안정성을 위해 TPM 2.0은 Windows 10의 모든 Azure AD 장치 등록 시나리오에 권장 되는 버전입니다.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>앱 토큰 및 브라우저 쿠키는 어떻게 보호 되나요?

**앱 토큰**: 앱이 WAM를 통해 토큰을 요청 하는 경우 Azure AD는 새로 고침 토큰과 액세스 토큰을 발급 합니다. 그러나 WAM는 앱에 대 한 액세스 토큰만 반환 하 고 사용자의 DPAPI (데이터 보호 응용 프로그래밍 인터페이스) 키로 암호화 하 여 캐시에서 새로 고침 토큰을 보호 합니다. WAM는 세션 키로 요청을 서명 하 여 새로 고침 토큰을 안전 하 게 사용 하 여 추가 액세스 토큰을 발급 합니다. DPAPI 키는 azure AD 자체의 Azure AD 기반 대칭 키로 보호 됩니다. 장치에서 DPAPI 키를 사용 하 여 사용자 프로필의 암호를 해독 해야 하는 경우 Azure AD는 세션 키로 암호화 된 DPAPI 키를 제공 합니다 .이 키는 CloudAP 플러그 인에서 TPM을 해독 하도록 요청 합니다. 이 기능은 보안 새로 고침 토큰의 일관성을 보장 하 고 자체 보호 메커니즘을 구현 하는 응용 프로그램을 방지 합니다.  

**브라우저 쿠키**: windows 10에서는 Azure AD가 Internet Explorer 및 Microsoft Edge의 브라우저 SSO를 기본적으로 지원 하거나 windows 10 계정 확장을 통해 Google Chrome에서 지원 합니다. 보안은 쿠키를 보호 하는 것 뿐만 아니라 쿠키를 전송 하는 끝점에 대해서도 빌드됩니다. 브라우저 쿠키는 세션 키를 활용 하 여 쿠키를 서명 및 보호 함으로써 PRT와 동일한 방식으로 보호 됩니다.

사용자가 브라우저 상호 작용을 시작 하면 브라우저 (또는 확장)가 COM native client 호스트를 호출 합니다. Native client 호스트는 페이지가 허용 된 도메인 중 하나에 있는지 확인 합니다. 브라우저는 nonce를 비롯 한 다른 매개 변수를 네이티브 클라이언트 호스트로 보낼 수 있지만 native client 호스트는 호스트 이름 유효성 검사를 보장 합니다. Native client 호스트는 CloudAP 플러그 인에서 PRT 쿠키를 요청 하 여 TPM 보호 세션 키로 만들고 서명 합니다. PRT 쿠키는 세션 키로 서명 되므로 변조 될 수 없습니다. 이 PRT 쿠키는 Azure AD에 대 한 요청 헤더에 포함 되어 원본 장치의 유효성을 검사 합니다. Chrome 브라우저를 사용 하는 경우 native client 호스트의 매니페스트에 명시적으로 정의 된 확장 에서만 이러한 요청을 수행할 수 없도록 하는 확장을 호출할 수 있습니다. Azure AD는 PRT 쿠키의 유효성을 검사 한 후 브라우저에 세션 쿠키를 발급 합니다. 이 세션 쿠키에는 PRT와 함께 발급 된 것과 동일한 세션 키도 포함 되어 있습니다. 후속 요청 중에는 세션 키의 유효성을 검사 하 여 쿠키를 장치에 효과적으로 바인딩하고 다른 곳에서 재생 하는 것을 방지 합니다.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT는 언제 MFA 클레임을 얻을 까 요?

PRT는 특정 시나리오에서 MFA (multi-factor authentication) 클레임을 가져올 수 있습니다. MFA 기반 PRT를 사용 하 여 응용 프로그램에 대 한 토큰을 요청 하는 경우 MFA 클레임은 이러한 앱 토큰으로 전송 됩니다. 이 기능은이를 필요로 하는 모든 앱에 대해 MFA 챌린지를 방지 하 여 사용자에 게 원활한 환경을 제공 합니다. PRT는 다음과 같은 방법으로 MFA 클레임을 가져올 수 있습니다.

* **비즈니스용 Windows hello로 로그인**: 비즈니스용 windows hello는 암호를 대체 하 고 암호화 키를 사용 하 여 강력한 2 단계 인증을 제공 합니다. 비즈니스용 Windows Hello는 장치에서 사용자에 게 적용 되며, 자체적으로 MFA를 프로 비전 해야 합니다. 사용자가 비즈니스용 Windows Hello를 사용 하 여 로그인 할 때 사용자의 PRT는 MFA 클레임을 가져옵니다. 이 시나리오는 스마트 카드 인증이 ADFS에서 MFA 클레임을 생성 하는 경우 스마트 카드를 사용 하 여 로그인 하는 사용자 에게도 적용 됩니다.
   * 비즈니스용 Windows Hello가 multi-factor authentication으로 간주 되기 때문에 MFA 클레임은 PRT를 새로 고칠 때 업데이트 되므로 사용자가 비즈니스용 WIndows Hello를 사용 하 여 로그인 하면 MFA 기간이 지속적으로 확장 됩니다.
* **WAM 대화형 로그인 중 mfa**: WAM를 통한 토큰 요청 중에 사용자가 앱에 액세스 하기 위해 mfa를 수행 해야 하는 경우이 상호 작용 중에 갱신 된 PRT는 mfa 클레임으로 imprinted 됩니다.
   * 이 경우 mfa 클레임은 지속적으로 업데이트 되지 않으므로 MFA 기간은 디렉터리에 설정 된 수명에 따라 결정 됩니다.
   * 이전 기존 PRT 및 RT를 앱에 액세스 하는 데 사용 하는 경우 PRT 및 RT는 첫 번째 인증 증명으로 간주 됩니다. 두 번째 증명 및 imprinted MFA 클레임에는 새가 필요 합니다. 새 PRT 및 RT도 발급 됩니다.
* **장치 등록 중 mfa**: 관리자가 Azure AD에서 장치를 [등록 하는 데 mfa를 요구](device-management-azure-portal.md#configure-device-settings)하도록 장치 설정을 구성한 경우 사용자는 mfa를 수행 하 여 등록을 완료 해야 합니다. 이 과정에서 사용자에 게 발급 된 PRT에는 등록 중에 가져온 MFA 클레임이 있습니다. 이 기능은 가입 작업을 수행한 사용자 에게만 적용 되며 해당 장치에 로그인 하는 다른 사용자에 게는 적용 되지 않습니다.
   * WAM 대화형 로그인과 마찬가지로 MFA 클레임은 지속적으로 업데이트 되지 않으므로 MFA 기간은 디렉터리에 설정 된 수명에 따라 결정 됩니다.

Windows 10은 각 자격 증명에 대 한 PRTs의 분할 된 목록을 유지 관리 합니다. 따라서 비즈니스용 Windows Hello, 암호 또는 스마트 카드 각각에 대 한 PRT가 있습니다. 이 분할은 사용 된 자격 증명에 따라 MFA 클레임이 격리 되도록 하 고 토큰 요청 중에 혼합 되지 않도록 합니다.

## <a name="how-is-a-prt-invalidated"></a>PRT는 어떻게 무효화 됩니까?

다음 시나리오에서는 PRT가 무효화 됩니다.

* **잘못 된 사용자**: Azure AD에서 사용자를 삭제 하거나 사용 하지 않도록 설정한 경우 해당 PRT는 무효화 되며 응용 프로그램에 대 한 토큰을 가져오는 데 사용할 수 없습니다. 삭제 된 사용자 또는 사용 하지 않도록 설정 된 사용자가 이전에 장치에 로그인 한 경우에는 CloudAP가 유효 하지 않은 상태를 인식할 때까지 캐시 된 로그인에서 로그인 합니다. 사용자가 잘못 된 것으로 확인 되 면 CloudAP는 후속 로그온을 차단 합니다. 잘못 된 사용자는 자격 증명을 캐시 하지 않은 새 장치에 자동으로 로그인 하지 못하게 됩니다.
* **잘못 된 장치**: Azure AD에서 장치를 삭제 하거나 사용 하지 않도록 설정 하면 해당 장치에서 가져온 PRT는 무효화 되며 다른 응용 프로그램에 대 한 토큰을 가져오는 데 사용할 수 없습니다. 사용자가 이미 잘못 된 장치에 로그인 한 경우에는이 작업을 계속할 수 있습니다. 그러나 장치의 모든 토큰이 무효화 되 고 사용자가 해당 장치의 모든 리소스에 대 한 SSO를 갖지 않습니다.
* **암호 변경**: 사용자가 암호를 변경한 후 이전 암호를 사용 하 여 얻은 PRT는 Azure AD에서 무효화 됩니다. 암호 변경으로 인해 사용자가 새 PRT를 가져옵니다. 이러한 무효화는 다음과 같은 두 가지 방법으로 발생할 수 있습니다.
   * 사용자가 새 암호를 사용 하 여 Windows에 로그인 하는 경우 CloudAP는 이전 PRT를 삭제 하 고 새 암호를 사용 하 여 새 PRT를 발급 하도록 Azure AD를 요청 합니다. 사용자가 인터넷에 연결 되어 있지 않은 경우 새 암호의 유효성을 검사할 수 없습니다. Windows에서 사용자가 이전 암호를 입력 하도록 요구할 수 있습니다.
   * 사용자가 이전 암호를 사용 하 여 로그인 하거나 Windows에 로그인 한 후 암호를 변경한 경우 WAM 기반 토큰 요청에 대해 이전 PRT가 사용 됩니다. 이 시나리오에서 사용자는 WAM 토큰 요청 중에 다시 인증 하 라는 메시지를 표시 하 고 새 PRT를 발급 합니다.
* **Tpm 문제**: 경우에 따라 장치의 tpm이 오류가 발생 하거나 실패 하 여 tpm으로 보호 되는 키를 액세스할 수 없게 수 있습니다. 이 경우 장치는 암호화 키의 소유를 증명할 수 없으므로 기존 PRT를 사용 하 여 PRT를 가져오거나 토큰을 요청할 수 없습니다. 따라서 모든 기존 PRT는 Azure AD에 의해 무효화 됩니다. Windows 10에서 오류를 검색 하면 복구 흐름을 시작 하 여 새 암호화 키로 장치를 다시 등록 합니다. 하이브리드 Azure Ad 조인을 사용 하는 경우 초기 등록과 마찬가지로 사용자 입력 없이 복구가 자동으로 수행 됩니다. Azure AD 조인 또는 Azure AD 등록 장치의 경우 장치에 대 한 관리자 권한이 있는 사용자가 복구를 수행 해야 합니다. 이 시나리오에서 복구 흐름은 사용자가 장치를 성공적으로 복구 하는 것을 안내 하는 Windows 프롬프트에 의해 시작 됩니다.

## <a name="detailed-flows"></a>자세한 흐름

다음 다이어그램에서는 PRT를 발급, 갱신 및 사용 하 여 응용 프로그램에 대 한 액세스 토큰을 요청 하는 기본 세부 정보를 보여 줍니다. 또한 이러한 상호 작용 중에는 앞서 언급 한 보안 메커니즘이 적용 되는 방법도 설명 합니다.

### <a name="prt-issuance-during-first-sign-in"></a>처음 로그인 하는 동안 PRT 발급

![처음 로그인 하는 동안 PRT 발급 상세 흐름](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Azure AD 조인 장치에서이 exchange는 사용자가 Windows에 로그온 할 수 있기 전에 PRT를 실행 하는 동기식으로 발생 합니다. 하이브리드 Azure AD 조인 장치에서 온-프레미스 Active Directory는 기본 인증 기관입니다. 따라서 사용자는 TGT를 획득 하 여 로그인 할 수 있을 때까지 대기 하는 반면, PRT 발급은 비동기적으로 발생 합니다. Logon은 Azure AD 자격 증명을 사용 하지 않으므로이 시나리오는 Azure AD에 등록 된 장치에는 적용 되지 않습니다.

| 단계 | 설명 |
| :---: | --- |
| A | 사용자가 로그인 UI에 암호를 입력 합니다. LogonUI는 인증 버퍼의 자격 증명을 LSA에 전달 하 여이를 내부적으로 CloudAP에 전달 합니다. CloudAP는이 요청을 CloudAP 플러그인으로 전달 합니다. |
| B | CloudAP 플러그 인은 사용자에 대 한 id 공급자를 식별 하는 영역 검색 요청을 시작 합니다. 사용자의 테 넌 트가 페더레이션 공급자를 설정 하는 경우 Azure AD는 페더레이션 공급자의 MEX (Metadata Exchange endpoint) 끝점을 반환 합니다. 그렇지 않은 경우 Azure AD는 사용자가 Azure AD를 사용 하 여 인증할 수 있음을 나타내는 사용자가 관리 됨을 반환 합니다. |
| C | 사용자가 관리 되는 경우 CloudAP는 Azure AD에서 nonce를 가져옵니다. 사용자가 페더레이션 된 경우 CloudAP 플러그 인은 페더레이션 공급자에서 사용자 자격 증명을 사용 하 여 SAML 토큰을 요청 합니다. 수신 되 면 SAML 토큰은 Azure AD에서 nonce를 요청 합니다. |
| D | CloudAP 플러그 인은 사용자의 자격 증명, nonce 및 broker 범위를 사용 하 여 인증 요청을 생성 하 고, 장치 키 (dkpriv)를 사용 하 여 요청에 서명 하 고 Azure AD에 보냅니다. 페더레이션된 환경에서 CloudAP 플러그 인은 사용자 자격 증명 대신 페더레이션 공급자가 반환 하는 SAML 토큰을 사용 합니다. |
| E | Azure AD는 사용자 자격 증명, nonce 및 장치 서명의 유효성을 검사 하 고 장치가 테 넌 트에서 유효한 지 확인 하 고 암호화 된 PRT를 발급 합니다. 또한 PRT와 함께 Azure AD는 tkpub (전송 키)를 사용 하 여 Azure AD에서 암호화 된 세션 키 라는 대칭 키를 발급 합니다. 또한 세션 키는 PRT에도 포함 되어 있습니다. 이 세션 키는 PRT를 사용 하는 후속 요청에 대 한 PoP (소유 증명) 키 역할을 합니다. |
| F | CloudAP 플러그 인은 암호화 된 PRT 및 세션 키를 CloudAP에 전달 합니다. CloudAP 전송 키 (tkpriv)를 사용 하 여 세션 키의 암호를 해독 하 고 TPM의 고유한 키를 사용 하 여 다시 암호화 하도록 TPM을 요청 합니다. CloudAP는 PRT와 함께 암호화 된 세션 키를 캐시에 저장 합니다. |

### <a name="prt-renewal-in-subsequent-logons"></a>후속 로그온 시 PRT 갱신

![후속 로그온 시 PRT 갱신](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 단계 | 설명 |
| :---: | --- |
| A | 사용자가 로그인 UI에 암호를 입력 합니다. LogonUI는 인증 버퍼의 자격 증명을 LSA에 전달 하 여이를 내부적으로 CloudAP에 전달 합니다. CloudAP는이 요청을 CloudAP 플러그인으로 전달 합니다. |
| B | 사용자가 이전에 사용자에 로그온 한 경우 Windows는 캐시 된 로그인을 시작 하 고 사용자를 기록할 자격 증명의 유효성을 검사 합니다. 4 시간 마다 CloudAP 플러그 인은 PRT 갱신을 비동기적으로 시작 합니다. |
| C | CloudAP 플러그 인은 사용자에 대 한 id 공급자를 식별 하는 영역 검색 요청을 시작 합니다. 사용자의 테 넌 트가 페더레이션 공급자를 설정 하는 경우 Azure AD는 페더레이션 공급자의 MEX (Metadata Exchange endpoint) 끝점을 반환 합니다. 그렇지 않은 경우 Azure AD는 사용자가 Azure AD를 사용 하 여 인증할 수 있음을 나타내는 사용자가 관리 됨을 반환 합니다. |
| D | 사용자가 페더레이션 된 경우 CloudAP 플러그 인은 페더레이션 공급자에서 사용자 자격 증명을 사용 하 여 SAML 토큰을 요청 합니다. 수신 되 면 SAML 토큰은 Azure AD에서 nonce를 요청 합니다. 사용자가 관리 되는 경우 CloudAP는 Azure AD에서 nonce를 직접 가져옵니다. |
| E | CloudAP 플러그 인은 사용자의 자격 증명, nonce 및 기존 PRT를 사용 하 여 인증 요청을 생성 하 고, 세션 키를 사용 하 여 요청에 서명 하 고 Azure AD로 보냅니다. 페더레이션된 환경에서 CloudAP 플러그 인은 사용자 자격 증명 대신 페더레이션 공급자가 반환 하는 SAML 토큰을 사용 합니다. |
| F | Azure AD는 PRT에 포함 된 세션 키와 비교 하 여 세션 키 서명의 유효성을 검사 하 고, nonce의 유효성을 검사 하 고, 장치가 테 넌 트에서 유효한 지 확인 하 고, 새 PRT를 발급 합니다. 앞에서 볼 수 있듯이, PRT는 전송 키 (tkpub)로 암호화 된 세션 키와 함께 다시 제공 됩니다. |
| G | CloudAP 플러그 인은 암호화 된 PRT 및 세션 키를 CloudAP에 전달 합니다. CloudAP는 TPM에서 전송 키 (tkpriv)를 사용 하 여 세션 키의 암호를 해독 하 고 TPM의 고유한 키를 사용 하 여 해당 키를 다시 암호화 하도록 요청 합니다. CloudAP는 PRT와 함께 암호화 된 세션 키를 캐시에 저장 합니다. |

### <a name="prt-usage-during-app-token-requests"></a>앱 토큰 요청 중에 PRT 사용

![앱 토큰 요청 중에 PRT 사용](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 단계 | 설명 |
| :---: | --- |
| A | 응용 프로그램 (예: Outlook, OneNote 등)은 WAM에 대 한 토큰 요청을 시작 합니다. 그런 다음 WAM는 Azure AD WAM 플러그 인을 요청 하 여 토큰 요청을 처리 합니다. |
| B | 응용 프로그램에 대 한 새로 고침 토큰을 이미 사용할 수 있는 경우 Azure AD WAM 플러그 인은이 토큰을 사용 하 여 액세스 토큰을 요청 합니다. 장치 바인딩의 증명을 제공 하기 위해 WAM 플러그 인은 세션 키를 사용 하 여 요청에 서명 합니다. Azure AD는 세션 키의 유효성을 검사 하 고, 세션 키로 암호화 된 앱에 대 한 새 새로 고침 토큰 및 액세스 토큰을 발급 합니다. WAM 플러그 인은 토큰의 암호를 해독 하도록 클라우드 AP 플러그 인을 요청 하며,이는 세션 키를 사용 하 여 암호를 해독 하도록 TPM을 요청 합니다. 그러면 WAM 플러그 인이 두 토큰을 모두 가져옵니다. 그런 다음 WAM 플러그 인은 응용 프로그램에 대 한 액세스 토큰만 제공 하지만 DPAPI를 사용 하 여 새로 고침 토큰을 다시 암호화 하 고 자체 캐시에 저장 합니다.  |
| C |  응용 프로그램에 대 한 새로 고침 토큰을 사용할 수 없는 경우 Azure AD WAM 플러그 인은 PRT를 사용 하 여 액세스 토큰을 요청 합니다. 소유 증명을 제공 하기 위해 WAM 플러그 인은 세션 키를 사용 하 여 PRT를 포함 하는 요청에 서명 합니다. Azure AD는 PRT에 포함 된 세션 키와 비교 하 여 세션 키 서명의 유효성을 검사 하 고, 장치가 올바른지 확인 하 고, 응용 프로그램에 대 한 액세스 토큰 및 새로 고침 토큰을 발급 합니다. 또한 Azure AD는 새로 고침 주기를 기반으로 하는 새 PRT를 발급할 수 있습니다. 이러한 모든 항목은 세션 키로 암호화 됩니다. |
| D | WAM 플러그 인은 토큰의 암호를 해독 하도록 클라우드 AP 플러그 인을 요청 하며,이는 세션 키를 사용 하 여 암호를 해독 하도록 TPM을 요청 합니다. 그러면 WAM 플러그 인이 두 토큰을 모두 가져옵니다. 그런 다음 WAM 플러그 인은 응용 프로그램에 대 한 액세스 토큰만 제공 하지만 DPAPI를 사용 하 여 새로 고침 토큰을 다시 암호화 하 고 자체 캐시에 저장 합니다. WAM 플러그 인은이 응용 프로그램에 대해 전달 되는 새로 고침 토큰을 사용 합니다. 또한 WAM 플러그 인은 새로운 PRT를 클라우드 AP 플러그인에 다시 제공 합니다 .이 플러그인은 Azure AD를 사용 하 여 PRT의 유효성을 검사 한 후 자체 캐시로 업데이트 합니다. 클라우드 AP 플러그인은 앞으로 새 PRT를 사용 합니다. |
| E | WAM는 WAM에 새로 발급 된 액세스 토큰을 제공 하며, 다시 호출 하는 응용 프로그램에 다시 제공 합니다.|

### <a name="browser-sso-using-prt"></a>PRT를 사용 하는 브라우저 SSO

![PRT를 사용 하는 브라우저 SSO](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 단계 | 설명 |
| :---: | --- |
| A | 사용자는 자격 증명을 사용 하 여 Windows에 로그인 하 여 PRT를 가져옵니다. 사용자가 브라우저를 열면 브라우저 또는 확장명이 레지스트리에서 Url을 로드 합니다. |
| B | 사용자가 Azure AD 로그인 URL을 열면 브라우저나 확장에서 레지스트리에서 가져온 URL의 유효성을 검사 합니다. 일치 하는 경우 브라우저는 토큰을 가져오기 위해 native client 호스트를 호출 합니다. |
| C | Native client 호스트는 url이 Microsoft id 공급자 (Microsoft 계정 또는 Azure AD)에 속해 있는지 확인 하 고, URL에서 보낸 nonce를 추출 하 고, CloudAP 플러그 인을 호출 하 여 PRT 쿠키를 가져옵니다. |
| D | CloudAP 플러그 인은 PRT 쿠키를 만들고 TPM 바인딩된 세션 키를 사용 하 여 로그인 한 다음 다시 native client 호스트로 보냅니다. 쿠키는 세션 키로 서명 되므로 변조 될 수 없습니다. |
| E | Native client 호스트는이 PRT 쿠키를 브라우저에 반환 합니다. 그러면이 PRT 쿠키를 Azure AD의 요청 토큰 및 요청 헤더의 일부로 포함 됩니다. |
| F | Azure AD는 PRT 쿠키에서 세션 키 서명의 유효성을 검사 하 고, nonce의 유효성을 검사 하 고, 장치가 테 넌 트에서 올바른지 확인 하 고, 웹 페이지에 대 한 ID 토큰과 브라우저의 암호화 된 세션 쿠키를 발급 합니다. |

## <a name="next-steps"></a>다음 단계

PRT 관련 문제를 해결 하는 방법에 대 한 자세한 내용은 [windows 10 및 Windows Server 2016 장치에 조인 된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-current.md)문서를 참조 하세요.
