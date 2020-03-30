---
title: UPN(Azure 사용자 원칙 이름) 변경 계획 및 문제 해결
description: UPN 변경에 대한 알려진 문제 및 완화 사항 이해
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
ms.openlocfilehash: 695773da624bc8d4ccff09119d64fc43319ff488
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246435"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Azure Active 디렉터리에서 사용자 주체 이름 변경 계획 및 문제 해결

UPN(사용자 주체 이름)은 사용자 계정에 대한 인터넷 통신 표준인 특성입니다. UPN은 UPN 접두사(사용자 계정 이름) 및 UPN 접미사(DNS 도메인 이름)으로 구성됩니다. 접두사는 "@" 기호를 사용하여 접미사를 조인합니다. someone@example.com)을 입력합니다. UPN은 디렉터리 포리스트 내의 모든 보안 주체 개체 사이에서 고유해야 합니다. 

> [!NOTE]
> 개발자의 경우 USER objectID를 UPN이 아닌 변경할 수 없는 식별자로 사용하는 것이 좋습니다. 응용 프로그램이 현재 UPN을 사용하는 경우 환경을 개선하기 위해 사용자의 기본 전자 메일 주소와 일치하도록 UPN을 설정하는 것이 좋습니다.<br> **하이브리드 환경에서는 온-프레미스 디렉터리 및 Azure Active Directory에서 사용자에 대한 UPN이 동일해야 합니다.**

**이 문서에서는 UPN을 사용자 식별자로 사용하고 있다고 가정합니다. UPN 변경 계획 및 UPN 변경으로 인해 발생할 수 있는 문제에서 복구하는 문제를 해결합니다.**

## <a name="learn-about-upns-and-upn-changes"></a>UPN 및 UPN 변경 사항에 대해 알아보기
로그인 페이지는 종종 필요한 값이 실제로 UPN인 경우 사용자에게 전자 메일 주소를 입력하라는 메시지를 표시합니다. 따라서 기본 이메일 주소가 변경될 때마다 사용자의 UPN을 변경해야 합니다.

사용자의 기본 이메일 주소는 여러 가지 이유로 변경될 수 있습니다.

* 회사 리브랜딩

* 다른 회사 부서로 이동하는 직원 

* 합병 및 인수

* 직원 이름 변경

### <a name="types-of-upn-changes"></a>UPN 변경 유형

접두사, 접미사 또는 둘 다를 변경하여 UPN을 변경할 수 있습니다.

* **접두사 변경**.

   *  예를 들어 사용자의 이름이 변경된 경우 계정 이름을 변경할 수 있습니다.  
BSimon@contoso.com 을 통해BJohnson@contoso.com

   * 접두사에 대한 회사 표준을 변경할 수도 있습니다.  
Bsimon@contoso.com 을 통해Britta.Simon@contoso.com

* **접미사 를 변경합니다.** <br>

    예를 들어 사람이 부서를 변경한 경우 도메인을 변경할 수 있습니다. 

   * Britta.Simon@contosolabs.com에 대한 Britta.Simon@contoso.com <br>
     또는<br>
    *   Britta.Simon@labs.contoso.com에 대한 Britta.Simon@corp.contoso.com 

사용자의 기본 전자 메일 주소가 업데이트될 때마다 사용자의 UPN을 변경합니다. 이메일 변경 사유에 관계없이 UPN은 항상 일치하도록 업데이트되어야 합니다.

Active Directory에서 Azure AD로 의 초기 동기화 중에 사용자의 전자 메일이 UPN과 동일한지 확인합니다.

### <a name="upns-in-active-directory"></a>활성 디렉토리의 UPN

Active Directory에서 기본 UPN 접미사는 사용자 계정을 만든 도메인의 DNS 이름입니다. 대부분의 경우 이 이름은 인터넷에서 엔터프라이즈 도메인으로 등록하는 도메인 이름입니다. contoso.com 도메인에서 사용자 계정을 만드는 경우 기본 UPN은

username@contoso.com

 그러나 Active Directory 도메인 및 트러스트를 사용하여 [UPN 접미사를 더 추가할](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) 수 있습니다. 

예를 들어 labs.contoso.com 추가하고 사용자의 UPN 및 이메일이 이를 반영하도록 할 수 있습니다. 그러면 그들은

username@labs.contoso.com.

>[!IMPORTANT]
> Active 디렉터리 및 Azure Active Directory의 UPN이 일치하지 않으면 문제가 발생합니다. [Active Directory에서 접미사를 변경하는](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)경우 Azure AD 에서 일치하는 사용자 지정 도메인 이름이 [추가되고 확인되었는지](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)확인해야 합니다. 

![확인된 도메인의 스크린샷](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure 활성 디렉터리에서 UPN

사용자는 사용자 PrincipalName 특성의 값을 사용하여 Azure AD에 로그인합니다. 

온-프레미스 Active Directory와 함께 Azure AD를 사용하는 경우 Azure AD Connect 서비스를 사용하여 사용자 계정이 동기화됩니다. 기본적으로 Azure AD Connect 마법사는 온-프레미스 활성 디렉터리에서 Azure AD의 UPN으로 userPrincipalName 특성을 사용합니다. 사용자 지정 설치에서 다른 특성으로 변경할 수 있습니다.

단일 사용자의 UPN(사용자 주체 이름) 또는 전체 조직에 대해 UPN(사용자 주체 이름)을 업데이트할 때 정의된 프로세스가 있어야 합니다. 

이 문서에서 알려진 문제 및 해결 방법을 참조하십시오.

사용자 계정을 Active Directory에서 Azure AD로 동기화하는 경우 Active Directory의 UPN이 Azure AD의 확인된 도메인에 있는지 확인합니다.

![확인된 도메인의 스크린샷](./media/howto-troubleshoot-upn-changes/verified-domains.png)

userPrincipalName 특성의 값이 Azure AD에서 확인된 도메인과 일치하지 않는 경우 동기화 프로세스는 접미사를 기본 .onmicrosoft.com 값으로 바꿉니다.


### <a name="roll-out-bulk-upn-changes"></a>롤아웃 대량 UPN 변경

대량 UPN[변경에 대한 시험 운용에](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) 대한 모범 사례를 따릅니다. 또한 신속하게 해결할 수 없는 문제가 발견되면 UPN을 되돌리기 위한 테스트된 롤백 계획을 가지고 있습니다. 파일럿이 실행되면 다양한 조직 역할과 특정 앱 또는 장치 집합을 가진 소규모 사용자 집합을 대상으로 시작할 수 있습니다.

이 첫 번째 하위 집합을 통해 변경의 일부로 사용자가 예상해야 하는 것을 잘 알 수 있습니다. 사용자 통신에 이 정보를 포함합니다.

일반 작업의 일부로 개별 사용자에 대한 UPN 을 변경하기 위한 정의된 프로시저를 만듭니다. 알려진 문제 및 해결 해결 에 대한 설명서가 포함된 테스트된 절차를 갖는 것이 좋습니다.

다음 섹션에서는 UPN이 변경될 때 알려진 잠재적인 문제 및 해결 방법을 자세히 설명합니다.

## <a name="user-provisioning-known-issues-and-workarounds"></a>알려진 문제 및 해결 방법을 사용자 프로비전

[서비스로서의 소프트웨어(SaaS)](https://azure.microsoft.com/overview/what-is-saas/) 및 LoB(비즈니스 라인) 응용 프로그램은 종종 UPN을 사용하여 사용자를 찾고 역할을 포함한 사용자 프로필 정보를 저장합니다. 사용자가 처음으로 앱에 로그인할 때 사용자 프로필을 만들기 위해 [Just in Time 프로비전을](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) 사용하는 응용 프로그램은 UPN 변경 사항의 영향을 받을 수 있습니다.

**알려진 문제**<br>
사용자의 UPN을 변경하면 Azure AD 사용자와 응용 프로그램에서 만든 사용자 프로필 간의 관계가 끊어질 수 있습니다. 응용 프로그램에서 [Just in Time 프로비저닝을](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)사용하는 경우 새 사용자 프로필을 만들 수 있습니다. 이렇게 하려면 응용 프로그램 관리자가 이 관계를 해결하기 위해 수동으로 변경해야 합니다.

**해결**<br>
[Azure AD 자동화된 사용자 프로비저닝을](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) 사용하면 지원되는 클라우드 응용 프로그램에서 사용자 ID를 자동으로 생성, 유지 관리 및 제거할 수 있습니다. 응용 프로그램에서 자동화된 사용자 프로비저닝을 구성하면 응용 프로그램의 UPN이 자동으로 업데이트됩니다. 프로그레시브 롤아웃의 일부로 응용 프로그램을 테스트하여 UPN 변경의 영향을 받지 않는지 확인합니다.

## <a name="managed-devices-known-issues-and-workarounds"></a>관리되는 장치 알려진 문제 및 해결 해결

[Azure AD에 장치를 가져오면](https://docs.microsoft.com/azure/active-directory/devices/overview)클라우드 및 온-프레미스 리소스에서 단일 사인온(SSO)을 통해 사용자의 생산성을 극대화할 수 있습니다.

### <a name="azure-ad-joined-devices"></a>Azure AD 조인 디바이스

[Azure AD 조인된](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) 장치는 Azure AD에 직접 조인되며 사용자가 조직의 ID를 사용하여 장치에 로그인할 수 있도록 합니다.

**알려진 문제** <br>
사용자는 인증을 위해 Azure AD에 의존하는 응용 프로그램에 대해 단일 사인온 문제가 발생할 수 있습니다.

**해결** <br>
UPN 변경 사항이 Azure AD에 동기화될 때까지 충분한 시간을 허용합니다. 새 UPN이 Azure AD 포털에 반영되어 있는지 확인하면 사용자에게 새 UPN으로 로그인할 "다른 사용자" 타일을 선택하도록 요청합니다. [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)을 통해 확인할 수도 있습니다. 새 UPN으로 로그인한 후에도 이전 UPN에 대한 참조가 "직장 또는 학교 액세스" Windows 설정에 계속 나타날 수 있습니다.

![확인된 도메인의 스크린샷](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>하이브리드 Azure AD 가입 디바이스

[하이브리드 Azure AD 조인](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) 된 장치는 Active Directory 및 Azure AD에 조인됩니다. 환경에 온-프레미스 Active Directory 설치 공간이 있고 Azure AD에서 제공하는 기능을 활용하려는 경우 하이브리드 Azure AD 조인을 구현할 수 있습니다.

**알려진 문제** 

Windows 10 하이브리드 Azure AD 에 가입한 장치는 예기치 않은 재시작 및 액세스 문제가 발생할 수 있습니다.

새 UPN이 Azure AD에 동기화되기 전에 사용자가 Windows에 로그인하거나 기존 Windows 세션을 계속 사용하는 경우 조건부 액세스가 구성된 경우 인증을 위해 Azure AD를 사용하는 응용 프로그램에서 단일 사인온 문제가 발생할 수 있습니다. 을 사용하여 하이브리드 조인 된 장치를 사용하여 리소스에 액세스합니다. 

또한 다음 메시지가 표시 되어 1 분 후 다시 시작 해야 합니다. 

"PC가 1분 만에 자동으로 다시 시작됩니다. Windows에서 문제가 발생했으며 다시 시작해야 합니다. 지금 이 메시지를 닫고 작업을 저장해야 합니다."

**해결** 

장치는 Azure AD에서 가입해제하고 다시 시작해야 합니다. 다시 시작하면 장치가 Azure AD에 자동으로 다시 가입하고 사용자는 "다른 사용자" 타일을 선택하여 새 UPN을 사용하여 로그인해야 합니다. Azure AD에서 장치에 가입을 취소하려면 명령 프롬프트에서 다음 명령을 실행합니다.

**지레cmd /휴가**

사용자가 사용 중인 경우 비즈니스용 Windows Hello에 [다시 등록해야](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) 합니다. WINDOWS 7 및 8.1 장치는 UPN 변경 후 이 문제의 영향을 받지 않습니다.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft 인증자 알려진 문제 및 해결 해결

조직에서 조직 응용 프로그램 및 데이터에 로그인하고 액세스하기 위해 [Microsoft 인증자 앱을](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) 사용해야 할 수 있습니다. 사용자 이름이 앱에 나타날 수 있지만 사용자가 등록 프로세스를 완료할 때까지 계정은 확인 방법으로 작동하도록 설정되지 않습니다.

[Microsoft 인증자 앱에는](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) 다음 네 가지 주요 기능이 있습니다.

* 푸시 알림 또는 확인 코드를 통한 다단계 인증

* iOS 및 Android 장치에서 인증 브로커 역할을 하여 [중개 인증을](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth) 사용하는 응용 프로그램에 대해 단일 사인온을 제공합니다.

* Intune 앱 보호 및 장치 등록/관리와 같은 다른 기능에 대한 요구 사항인 Azure AD에 대한 장치 등록(Workplace 조인이라고도 함)은

* MFA 및 장치 등록이 필요한 전화 로그인.

### <a name="multi-factor-authentication-with-android-devices"></a>안드로이드 장치를 가진 다단계 인증

Microsoft 인증자 앱은 대역 외 확인 옵션을 제공합니다. [MFA(다단계 인증)는](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) 로그인 하는 동안 사용자에게 자동 전화 통화 또는 SMS를 배치하는 대신 사용자의 스마트폰 또는 태블릿에서 Microsoft 인증자 앱에 알림을 푸시합니다. 사용자는 단순히 승인을 탭 (또는 PIN 또는 생체 인식을 입력하고 자신의 로그인을 완료하기 위해 응용 프로그램에서 "인증"을 누릅니다).

사용자의 UPN을 변경하면 모바일 장치에 다음과 같은 문제가 발생할 수 있습니다.

**알려진 문제** 

이전 UPN이 여전히 사용자 계정에 표시되며 알림이 수신되지 않을 수 있습니다. [확인 코드는](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) 계속 작동합니다.

**해결**

알림이 수신되면 사용자에게 알림을 해제하고, 인증자 앱을 열고, "알림 확인" 옵션을 탭하고 MFA 프롬프트를 승인하도록 지시합니다. 그 후 계정에 표시된 UPN이 업데이트됩니다. 업데이트된 UPN이 새 계정으로 표시될 수 있으며 이는 다른 인증자 기능이 사용 중이기 때문입니다. 자세한 내용은 이 문서에서 알려진 추가 문제입니다.

### <a name="brokered-authentication"></a>중개 인증

마이크로소프트 인증 같은 안 드 로이드와 iOS 브로커에 사용 하려면:

* 단일 사인온(SSO) - 사용자가 각 응용 프로그램에 로그인할 필요가 없습니다.

* 장치 식별 - 브로커는 Workplace에 가입할 때 장치에서 생성된 장치 인증서에 액세스합니다.

* 응용 프로그램 식별 확인 - 응용 프로그램이 브로커를 호출하면 리디렉션 URL을 전달하고 브로커가 이를 확인합니다.

또한 응용 프로그램이 [조건부 액세스와](https://docs.microsoft.com/azure/active-directory/conditional-access/)같은 고급 기능에 참여할 수 있으며 [Microsoft Intune 시나리오를](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)지원합니다.

**알려진 문제**<br>
사용자는 응용 프로그램에서 전달된 login_hint 브로커에 저장된 UPN 간의 불일치로 인해 브로커 지원 로그인을 사용하는 새 응용 프로그램에 대해 보다 대화형 인증 프롬프트가 표시됩니다.

**해결** <br> 사용자는 Microsoft 인증자에서 계정을 수동으로 제거하고 브로커 지원 응용 프로그램에서 새 로그인을 시작해야 합니다. 계정은 초기 인증 후 자동으로 추가됩니다.

### <a name="device-registration"></a>디바이스 등록

Microsoft 인증자 앱은 Azure AD에 장치를 등록하는 책임을 집니다. 장치 등록을 사용하면 장치가 Azure AD에 인증할 수 있으며 다음 시나리오에 대한 요구 사항입니다.

* Intune 앱 보호

* 인튠 장치 등록

* 전화 로그인

**알려진 문제**<br>
UPN을 변경하면 새 UPN이 있는 새 계정이 Microsoft 인증자 앱에 나열되고 이전 UPN이 있는 계정은 계속 나열됩니다. 또한 이전 UPN앱 설정의 장치 등록 섹션에 표시됩니다. 장치 등록의 일반적인 기능이나 종속 시나리오에는 변화가 없습니다.

**해결** <br> Microsoft 인증자 앱에서 이전 UPN에 대한 모든 참조를 제거하려면 사용자에게 Microsoft 인증자에서 이전 계정과 새 계정을 모두 수동으로 제거하고 MFA에 다시 등록하고 장치를 다시 가입하도록 지시합니다.

### <a name="phone-sign-in"></a>전화 로그인

전화 로그인을 사용하면 사용자가 암호 없이 Azure AD에 로그인할 수 있습니다. 전화 로그인을 사용하려면 인증자 앱을 사용하여 MFA에 등록한 다음 인증자에서 직접 전화 로그인을 사용하도록 설정해야 합니다. 구성의 일부로 장치는 Azure AD에 등록합니다.

**알려진 문제** <br>
사용자는 알림을 받지 못하기 때문에 전화 로그인을 사용할 수 없습니다. 사용자가 알림 확인을 탭하면 오류가 발생합니다.

**해결**<br>
사용자는 전화 로그인에 대해 활성화된 계정의 드롭다운 메뉴를 선택하고 전화 로그인 사용 안 함(휴대폰 로그인 사용 안 함)을 선택해야 합니다. 원하는 경우 전화 로그인을 다시 활성화할 수 있습니다.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>보안 키(FIDO2) 알려진 문제 및 해결 해결

**알려진 문제** <br>
여러 사용자가 동일한 키에 등록되면 로그인 화면에 이전 UPN이 표시되는 계정 선택 페이지가 표시됩니다. 보안 키를 사용하여 로그인하는 것은 UPN 변경사항의 영향을 받지 않습니다.  

**해결**<br>
이전 UPN에 대한 참조를 제거하려면 사용자가 [보안 키를 재설정하고 다시 등록해야](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)합니다.

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive 알려진 문제 및 해결 해결

OneDrive 사용자는 UPN 변경 후 문제가 발생하는 것으로 알려져 있습니다. 자세한 내용은 [UPN 변경 사항이 OneDrive URL 및 OneDrive 기능에 미치는 영향을](https://docs.microsoft.com/onedrive/upn-changes)참조하십시오.

## <a name="next-steps"></a>다음 단계

다음 리소스를 참조하십시오.
* [Azure AD Connect: 설계 개념](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Azure AD userPrincipalName 채우기](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [마이크로소프트 아이덴티티 플랫폼 ID 토큰](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
