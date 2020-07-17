---
title: Azure UPN (사용자 계정 이름) 변경 계획 및 문제 해결
description: UPN 변경에 대 한 알려진 문제 및 완화 이해
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 885d30305ba2b186052e17b9b455b2248bca541b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608520"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Azure Active Directory에서 사용자 계정 이름 변경 계획 및 문제 해결

UPN (사용자 계정 이름)은 사용자 계정에 대 한 인터넷 통신 표준인 특성입니다. UPN은 UPN 접두사(사용자 계정 이름) 및 UPN 접미사(DNS 도메인 이름)으로 구성됩니다. 접두사는 "@" 기호를 사용 하 여 접미사를 조인 합니다. 예: someone@example.com. UPN은 디렉터리 포리스트 내의 모든 보안 주체 개체 사이에서 고유해야 합니다. 

**이 문서에서는 사용자가 UPN을 사용 하 고 있다고 가정 합니다. UPN 변경에 대 한 계획을 수립 하 고 UPN 변경으로 인해 발생할 수 있는 문제를 해결 합니다.**

> [!NOTE]
> 개발자의 경우 값이 변경 될 수 있으므로 UPN 또는 전자 메일 주소가 아닌 변경할 수 없는 식별자로 사용자 objectID를 사용 하는 것이 좋습니다.


## <a name="learn-about-upns-and-upn-changes"></a>Upn 및 UPN 변경에 대 한 자세한 정보
필수 값이 실제로 UPN 인 경우 로그인 페이지에서 사용자에 게 전자 메일 주소를 입력 하 라는 메시지를 표시 하는 경우가 많습니다. 따라서 기본 전자 메일 주소가 변경 될 때마다 사용자의 UPN을 변경 해야 합니다.

사용자의 기본 전자 메일 주소는 다음과 같은 다양 한 이유로 변경 될 수 있습니다.

* 회사 재 브랜딩

* 직원 들이 다른 회사 부서로 전환 

* 인수 및 인수

* 직원 이름 변경

### <a name="types-of-upn-changes"></a>UPN 변경 유형

접두사, 접미사 또는 둘 다를 변경 하 여 UPN을 변경할 수 있습니다.

* **접두사 변경**

   *  예를 들어 사용자 이름이 변경 된 경우 계정 이름을 변경할 수 있습니다.  
BSimon@contoso.com대상BJohnson@contoso.com

   * 접두사에 대 한 회사 표준도 변경할 수 있습니다.  
Bsimon@contoso.com대상Britta.Simon@contoso.com

* **접미사를 변경**합니다. <br>

    예를 들어 사용자가 부서를 변경한 경우 해당 도메인을 변경할 수 있습니다. 

   * Britta.Simon@contoso.com - Britta.Simon@contosolabs.com <br>
     또는<br>
    * Britta.Simon@corp.contoso.com - Britta.Simon@labs.contoso.com 

기본 전자 메일 주소가 업데이트 될 때마다 사용자의 UPN을 변경 하는 것이 좋습니다.

Active Directory에서 Azure AD로의 초기 동기화 중에 사용자의 전자 메일이 Upn과 동일한 지 확인 합니다.

### <a name="upns-in-active-directory"></a>Active Directory의 Upn

Active Directory에서 기본 UPN 접미사는 사용자 계정을 만든 도메인의 DNS 이름입니다. 대부분의 경우 인터넷에서 엔터프라이즈 도메인으로 등록 하는 도메인 이름입니다. Contoso.com 도메인에서 사용자 계정을 만들 경우 기본 UPN은

username@contoso.com

 그러나 Active Directory 도메인 및 트러스트를 사용 하 여 [더 많은 UPN 접미사를 추가할](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) 수 있습니다. 

예를 들어 labs.contoso.com를 추가 하 고 사용자의 Upn 및 전자 메일에이를 반영 하도록 할 수 있습니다. 그러면 다음과 같은 상태가 됩니다.

username@labs.contoso.com.

>[!IMPORTANT]
> [Active Directory에서 접미사를 변경](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)하는 경우 [Azure AD에서](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)일치 하는 사용자 지정 도메인 이름이 추가 되 고 확인 되었는지 확인 해야 합니다. 

![확인 된 도메인의 스크린샷](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure Active Directory의 Upn

사용자는 userPrincipalName 특성의 값을 사용 하 여 Azure AD에 로그인 합니다. 

온-프레미스 Active Directory와 함께 Azure AD를 사용 하는 경우 사용자 계정은 Azure AD Connect 서비스를 사용 하 여 동기화 됩니다. 기본적으로 Azure AD Connect 마법사는 온-프레미스 Active Directory의 userPrincipalName 특성을 Azure AD의 UPN으로 사용 합니다. 사용자 지정 설치에서 다른 특성으로 변경할 수 있습니다.

단일 사용자의 UPN (사용자 계정 이름) 또는 전체 조직을 업데이트할 때 프로세스를 정의 하는 것이 중요 합니다. 

이 문서의 알려진 문제 및 해결 방법을 참조 하세요.

사용자 계정을 Active Directory에서 Azure AD로 동기화 하는 경우 Active Directory의 Upn이 Azure AD의 확인 된 도메인에 매핑되는지 확인 합니다.

![확인 된 도메인의 스크린샷](./media/howto-troubleshoot-upn-changes/verified-domains.png)

UserPrincipalName 특성의 값이 Azure AD의 확인 된 도메인에 해당 하지 않는 경우 동기화 프로세스에서 접미사를 onmicrosoft.com 값으로 바꿉니다.


### <a name="roll-out-bulk-upn-changes"></a>대량 UPN 변경 내용 롤아웃

대량 UPN 변경에 대 한 [파일럿의 모범 사례](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) 를 따르세요. 또한 신속 하 게 해결할 수 없는 문제를 발견 한 경우 Upn을 되돌리기 위한 테스트 된 롤백 계획도 있습니다. 파일럿이 실행 되 면 다양 한 조직 역할과 특정 앱 또는 장치 집합을 사용 하 여 소규모 사용자 집합을 대상으로 지정할 수 있습니다.

사용자의 첫 번째 하위 집합을 통해 사용자는 변경의 일부로 서 사용자에 게 필요한 작업을 파악할 수 있습니다. 사용자 통신에이 정보를 포함 합니다.

일반 작업의 일부로 개별 사용자의 Upn을 변경 하는 정의 된 프로시저를 만듭니다. 알려진 문제 및 해결 방법에 대 한 설명서를 포함 하는 테스트 된 절차를 수행 하는 것이 좋습니다.

다음 섹션에서는 Upn이 변경 될 때 발생할 수 있는 잠재적인 문제 및 해결 방법을 설명 합니다.

## <a name="apps-known-issues-and-workarounds"></a>앱의 알려진 문제 및 해결 방법

[SaaS (Software as a service)](https://azure.microsoft.com/overview/what-is-saas/) 및 LoB (기간 업무) 응용 프로그램은 종종 upn을 사용 하 여 사용자를 찾고 역할을 비롯 한 사용자 프로필 정보를 저장 합니다. 사용자가 처음으로 앱에 로그인 할 때 [just-in-time 프로 비전](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) 을 사용 하 여 사용자 프로필을 만드는 응용 프로그램은 UPN 변경의 영향을 받을 수 있습니다.

**알려진 문제**<br>
사용자의 UPN을 변경 하면 Azure AD 사용자와 응용 프로그램에서 만든 사용자 프로필 간의 관계가 손상 될 수 있습니다. 응용 프로그램에서 [just-in-time 프로 비전](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)을 사용 하는 경우 새 사용자 프로필을 만들 수 있습니다. 이 관계를 수정 하려면 응용 프로그램 관리자가 수동으로 변경 해야 합니다.

**해결 방법**<br>
[AZURE AD 자동 사용자 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 을 사용 하면 지원 되는 클라우드 응용 프로그램에서 사용자 id를 자동으로 생성, 유지 관리 및 제거할 수 있습니다. 응용 프로그램에 자동화 된 사용자 프로 비전을 구성 하면 응용 프로그램의 Upn이 자동으로 업데이트 됩니다. 프로그레시브 롤아웃의 일부로 응용 프로그램을 테스트 하 여 UPN 변경의 영향을 받지 않는 유효성을 검사 합니다.
개발자 인 경우 [응용 프로그램에 SCIM 지원을 추가](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) 하 여 Azure Active Directory에서 자동 사용자 프로비저닝을 사용 하도록 설정 하는 것이 좋습니다. 

## <a name="managed-devices-known-issues-and-workarounds"></a>관리 되는 장치 알려진 문제 및 해결 방법

[장치를 AZURE AD로 가져오면](https://docs.microsoft.com/azure/active-directory/devices/overview)클라우드 및 온-프레미스 리소스에서 SINGLE SIGN-ON (SSO)를 통해 사용자의 생산성을 극대화 합니다.

### <a name="azure-ad-joined-devices"></a>Azure AD 조인 디바이스

[AZURE ad 조인](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) 장치는 azure ad에 직접 가입 되며 사용자가 조직의 id를 사용 하 여 장치에 로그인 할 수 있습니다.

**알려진 문제** <br>
사용자는 인증을 위해 Azure AD에 의존 하는 응용 프로그램에 대 한 Single Sign-On 문제를 겪을 수 있습니다.

**해결 방법** <br>
이 섹션에서 설명 하는 문제는 Windows 10 2020 업데이트 (2004)에서 해결 되었습니다.

**해결 방법** <br>
UPN 변경 내용이 Azure AD로 동기화 될 때까지 충분 한 시간을 허용 합니다. 새 UPN이 Azure AD 포털에 반영 되는지 확인 한 후 사용자에 게 "기타 사용자" 타일을 선택 하 여 새 UPN으로 로그인 하도록 요청 합니다. [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)을 통해 확인할 수도 있습니다. 새 UPN을 사용 하 여 로그인 한 후에는 이전 UPN에 대 한 참조가 "회사 또는 학교 액세스" 창 설정에 계속 나타날 수 있습니다.

![확인 된 도메인의 스크린샷](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 가입 디바이스

[하이브리드 AZURE ad 조인](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) 장치는 Active Directory 및 Azure ad에 가입 됩니다. 사용자 환경에 온-프레미스 Active Directory 공간이 있고 Azure AD에서 제공 하는 기능을 활용 하려는 경우 하이브리드 Azure AD 조인을 구현할 수 있습니다.

**알려진 문제** 

Windows 10 하이브리드 Azure AD 조인 장치에서 예기치 않은 다시 시작 및 액세스 문제가 발생할 수 있습니다.

새 UPN이 Azure AD에 동기화 되기 전에 사용자가 Windows에 로그인 하거나 기존 Windows 세션을 계속 사용 하는 경우, 하이브리드 조인 장치를 사용 하 여 리소스에 액세스 하도록 조건부 액세스를 구성한 경우 인증에 Azure AD를 사용 하는 응용 프로그램의 Single Sign-On 문제가 발생할 수 있습니다. 

또한 다음 메시지가 표시 되 고 1 분 후에 다시 시작 됩니다. 

"1 분 후에 PC가 자동으로 다시 시작 됩니다. Windows에서 문제가 발생 하 여 다시 시작 해야 합니다. 지금이 메시지를 닫고 작업을 저장 해야 합니다. "

**해결 방법** <br>
이 섹션에서 설명 하는 문제는 Windows 10 2020 업데이트 (2004)에서 해결 되었습니다.

**해결 방법** 

장치가 Azure AD에서 가입 다시 시작 되어야 합니다. 다시 시작한 후에는 장치에서 자동으로 Azure AD에 다시 조인 하 고 사용자는 "기타 사용자" 타일을 선택 하 여 새 UPN을 사용 하 여 로그인 해야 합니다. Azure AD에서 장치를 가입 해제 하려면 명령 프롬프트에서 다음 명령을 실행 합니다.

**dsregcmd.exe/leave**

사용자는 비즈니스용 Windows Hello를 사용 하는 경우 [다시 등록](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) 해야 합니다. UPN이 변경 된 후에도 Windows 7 및 8.1 장치는이 문제의 영향을 받지 않습니다.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>알려진 문제 및 해결 방법 Microsoft Authenticator

조직에서 [Microsoft Authenticator 앱](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) 을 사용 하 여 로그인 하 고 조직 응용 프로그램 및 데이터에 액세스 해야 할 수 있습니다. 사용자 이름은 앱에 나타날 수 있지만 사용자가 등록 프로세스를 완료할 때까지 계정이 확인 방법으로 작동 하도록 설정 되지 않습니다.

[Microsoft Authenticator 앱](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) 에는 네 가지 주요 기능이 있습니다.

* 푸시 알림 또는 확인 코드를 통한 multi-factor authentication

* 조정 된 [인증](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth) 을 사용 하는 응용 프로그램에 대 한 Single Sign-On를 제공 하기 위해 IOS 및 Android 장치에서 인증 브로커로 작동

* 장치 등록 (Workplace Join 라고도 함)은 Azure AD에 대 한 Intune 앱 보호 및 장치 등록/관리와 같은 다른 기능을 요구 하는 요구 사항입니다.

* 휴대폰 로그인-MFA 및 장치 등록이 필요 합니다.

### <a name="multi-factor-authentication-with-android-devices"></a>Android 장치 Multi-Factor Authentication

Microsoft Authenticator 앱은 대역 외 인증 옵션을 제공 합니다. 로그인 하는 동안 사용자에 게 자동 전화 통화 또는 SMS를 배치 하는 대신 [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) 는 사용자의 스마트폰 또는 태블릿에서 Microsoft Authenticator 앱에 알림을 푸시합니다. 사용자는 앱에서 승인 (또는 PIN 또는 생체 인식을 입력 하 고 "인증"을 탭) 하 여 로그인을 완료할 수 있습니다.

**알려진 문제** 

사용자의 UPN을 변경 하면 이전 UPN이 사용자 계정에 계속 표시 되 고 알림이 수신 되지 않을 수 있습니다. [확인 코드](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) 는 계속 작동 합니다.

**해결 방법**

알림이 수신 되 면 사용자에 게 알림을 해제 하 고 인증자 앱을 연 다음 "알림 확인" 옵션을 탭 하 고 MFA 프롬프트를 승인 하도록 지시 합니다. 그러면 계정에 표시 되는 UPN이 업데이트 됩니다. 업데이트 된 UPN은 새 계정으로 표시 될 수 있으며,이는 다른 인증자 기능을 사용 하기 때문입니다. 자세한 내용은이 문서의 알려진 추가 문제를 참조 하세요.

### <a name="brokered-authentication"></a>조정 된 인증

Android 및 iOS broker (예: Microsoft Authenticator 사용):

* SSO (Single sign-on)-사용자는 각 응용 프로그램에 로그인 할 필요가 없습니다.

* 장치 식별-broker는 작업 공간에 연결 된 장치에서 만든 장치 인증서에 액세스 합니다.

* 응용 프로그램 id 확인-응용 프로그램에서 broker를 호출 하면 해당 리디렉션 URL을 전달 하 고 broker는이를 확인 합니다.

또한 응용 프로그램은 [조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/)와 같은 고급 기능에 참여할 수 있으며 [Microsoft Intune 시나리오](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)를 지원 합니다.

**알려진 문제**<br>
응용 프로그램에 의해 전달 된 login_hint와 broker에 저장 된 UPN이 일치 하지 않아 broker 기반 로그인을 사용 하는 새 응용 프로그램에 대 한 더 많은 대화형 인증 프롬프트가 표시 됩니다.

**해결 방법** <br> 사용자는 Microsoft Authenticator에서 수동으로 계정을 제거 하 고 브로커 지원 응용 프로그램에서 새 로그인을 시작 해야 합니다. 초기 인증 후 계정이 자동으로 추가 됩니다.

### <a name="device-registration"></a>디바이스 등록

Microsoft Authenticator 앱은 Azure AD에 장치를 등록 하는 일을 담당 합니다. 장치 등록을 통해 장치는 Azure AD에 인증할 수 있으며 다음 시나리오의 요구 사항입니다.

* Intune 앱 보호

* Intune 장치 등록

* 휴대폰 로그인

**알려진 문제**<br>
UPN을 변경 하면 새 UPN을 가진 새 계정이 Microsoft Authenticator 앱에 나열 되 고, 이전 UPN이 있는 계정은 계속 나열 됩니다. 또한 앱 설정의 장치 등록 섹션에 이전 UPN이 표시 됩니다. 장치 등록 또는 종속 시나리오의 정상적인 기능은 변경 되지 않습니다.

**해결 방법** <br> Microsoft Authenticator 앱에서 이전 UPN에 대 한 모든 참조를 제거 하려면 사용자에 게 이전 Microsoft Authenticator 계정과 새 계정을 모두 수동으로 제거 하 고 MFA에 등록 한 후 장치에 다시 참가 하도록 사용자에 게 지시 합니다.

### <a name="phone-sign-in"></a>휴대폰 로그인

휴대폰 로그인을 사용 하면 사용자가 암호 없이 Azure AD에 로그인 할 수 있습니다. 휴대폰 로그인을 사용 하도록 설정 하려면 사용자가 Authenticator 앱을 사용 하 여 MFA에 등록 한 다음 인증자에서 직접 휴대폰 로그인을 사용 하도록 설정 해야 합니다. 구성의 일부로 장치는 Azure AD에 등록 됩니다.

**알려진 문제** <br>
사용자는 알림을 수신 하지 않으므로 휴대폰 로그인을 사용할 수 없습니다. 사용자가 알림 검사를 탭 하면 오류가 발생 합니다.

**해결 방법**<br>
사용자는 휴대폰 로그인을 사용 하도록 설정 된 계정에서 드롭다운 메뉴를 선택 하 고 휴대폰 로그인 사용 안 함을 선택 해야 합니다. 원할 경우 휴대폰 로그인을 다시 사용 하도록 설정할 수 있습니다.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>FIDO2 (보안 키) 알려진 문제 및 해결 방법

**알려진 문제** <br>
여러 사용자가 동일한 키에 등록 되 면 로그인 화면에 이전 UPN이 표시 되는 계정 선택 페이지가 표시 됩니다. 보안 키를 사용 하는 로그인은 UPN 변경의 영향을 받지 않습니다.  

**해결 방법**<br>
이전 Upn에 대 한 참조를 제거 하려면 사용자가 [보안 키를 다시 설정 하 고 다시 등록](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)해야 합니다.

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive의 알려진 문제 및 해결 방법

UPN이 변경 된 후에는 OneDrive 사용자가 문제를 경험 하는 것으로 알려져 있습니다. 자세한 내용은 [UPN 변경이 ONEDRIVE URL 및 onedrive 기능에 미치는 영향](https://docs.microsoft.com/onedrive/upn-changes)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 리소스를 참조 하세요.
* [Azure AD Connect: 설계 개념](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Azure AD userPrincipalName 채우기](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Microsoft id 플랫폼 ID 토큰](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
