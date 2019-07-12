---
title: Azure Active Directory를 사용 하 여 암호 없이 세계 이해 | Microsoft Docs
description: 이 가이드는 Ceo, Cio, Ciso, 수석 Id 설계자, 엔터프라이즈 설계자 및 보안 및 IT 의사 결정자는 Azure Active Directory 구현에 대 한 암호 없는 인증 방법을 선택 하는 일을 담당 합니다.
services: active-directory
keywords: 암호 없는, azure ad
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 00f66b6010bead3de131095a47ba1e419d2511c0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723442"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Azure Active Directory를 사용 하 여 암호 없이 세계

암호를 사용 하 여 관계를 나누는 차례입니다. 과거에는 암호는 우리에 게 좋은 되었습니다 하지만 오늘날의 디지털 작업 영역에 있는 비교적 쉽게 공격 벡터 해커에 대 한 합니다. 해커 좋아하는 암호 및 확인 하는 가장 일반적으로 고려할 때 연도, 월, 계절, 같은 용어를 포함 하는 Azure Active Directory (Azure AD)에서 거부 된 암호 하 하거나 로컬 스포츠 팀 이유 어렵지 않습니다. 게다가 [연구에 따르면](https://aka.ms/passwordguidance) 와 관련 된 변경 빈도, 길이 요구 사항 및 복잡성 요구 사항 등 암호 관리에 대 한 일반적인 권장 사항에 대 한 다양 한 이유로 스케줄링과 사용자 특성입니다.

세 가지 일반적으로 사용 되는 사용자 계정을 손상 공격 유형은 암호 스프레이 피싱 및 위반 재생 합니다. Azure AD와 같은 기능 [스마트 잠금](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)를 [금지 된 암호](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), 및 [암호 보호](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) 이러한 종류의 공격 으로부터 보호할 수 있습니다. 마찬가지로, 구현 [multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) (MFA) 또는 2 단계 인증을 두 번째 형식의 인증을 요구 하 여 추가 보안을 제공 합니다. 하지만 장기적으로 암호 없는 솔루션은 가장 안전한 인증 방법을 확인을 위한 최상의 솔루션입니다.

이 문서는 이해 하 고 Microsoft의 암호 없는 솔루션 및 하나 이상의 다음 옵션 중에서 선택 하면 도움말을 구현 하는 데 경험 시작.

* **비즈니스용 Windows Hello**합니다. Windows 10에서 Windows Hello 비즈니스에 대 한 대체 암호 Pc 및 모바일 장치에 강력한 2 단계 인증을 사용 합니다. 이 인증은 디바이스에 연결되는 사용자 자격 증명의 새로운 유형으로 구성되며 생체 인식 또는 PIN을 사용합니다.

* **암호 없는 로그인 Microsoft Authenticator를 사용 하 여**입니다. 암호를 사용 하지 않고 Azure AD 계정에 로그인 하려면 Microsoft Authenticator 앱을 사용할 수 있습니다. 와 마찬가지로 Windows Hello for Business의 기술, Microsoft Authenticator를 사용 하 여 키 기반 인증 장치에 연결 되는 생체 인식 또는 PIN을 사용 하는 사용자 자격 증명을 사용 하도록 설정 합니다.

* **보안 키 FIDO2**합니다. FIDO2는 모든 웹 사이트에서 고유 및 Windows Hello 또는 외부 보안 키와 같은 로컬 장치에 저장 되는 암호화 로그인 자격 증명을 제공 합니다. 이러한 보안 키의 암호 도용 피싱 위험 정보 훼손에 강하도록 되며 재생 공격. 생체 인식 또는 PIN을 통해 사용자 인증을 사용 하 여 결합 솔루션은 두 단계 확인 모임에 대 한 최신 보안 요구 사항

## <a name="the-future-of-passwordless-authentication"></a>암호 없는 인증의 미래

이 요즘, 은행, 신용 카드 회사 및 다른 조직 및 온라인 서비스는 대부분 보호 계정의 id를 두 번 확인 해야 하므로: 전화, 텍스트 또는 앱에서 사용자 암호를 다시 사용 하 여 한 번입니다. 공유 되는 암호의 보안 문제를 해결 하는 multi-factor authentication 인증을 하는 동안 도난 또는 추측을 해결 하지는 않습니다 기억 하는 데 있어 불편 비율입니다. 사용자 및 조직은 필요한 오늘날의 클라우드 시대의 매우 안전한 암호 없는 인증 방법은 *고* 편리 합니다.

![편의성 및 보안](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows Hello 비즈니스, 암호 없는 로그인에 Microsoft Authenticator 및 FIDO2 보안 키를 간단 하 고 일반적인 아키텍처를 공유 하는 모든 사용자가 모두 매우 안전 하 고 사용을 편리 하는 인증 방법을 제공 하는 합니다. 세 가지 모두는 기술을 기반으로 공개/개인 키 필요 로컬 제스처는 생체 인식 또는 PIN 및 개인 키를 단일 장치로 이동 하 고 안전 하 게 바인딩된 같은 저장 및 공유할 수 없습니다.

## <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

Windows 10에서 Windows Hello 비즈니스에 대 한 대체 암호 Pc 및 장치에 강력한 2 단계 인증을 사용 합니다. 새로운 유형의 장치에 연결 되 고 사용자가 Azure AD 인증을 받을 수 있는 PIN 또는 생체 인식 제스처를 사용 하는 사용자 자격 증명의 인증 구성 및 온-프레미스 Active Directory. 장치에 단순히 서명 사용 Windows Hello for Business 쉽습니다. PIN 또는 지문 또는 안 면 인식 같은 생체 인식 제스처를 사용 합니다.

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello 비즈니스 시나리오

Windows 비즈니스용 Hello는 지정 된 자신의 Windows PC가 정보 근로자에 적합 합니다. 생체 인식 자격 증명 소유자 이외의 사용자 로부터 액세스를 방지 하는 사용자의 PC에 직접 연결 됩니다. PKI 통합 및에서 single sign-on (SSO)에 대 한 기본 제공 지원을 사용 하 여 Windows Hello 비즈니스에 대 한 메서드를 제공 쉽고 편리 하 게 원활 하 게 온-프레미스 회사 리소스에 액세스 하 고 클라우드에서 합니다.

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello 비즈니스 배포 고려 사항

Windows Hello for Business는 장치 등록, 프로비저닝 및 인증을 위해 여러 구성 요소를 사용 하는 분산된 시스템. 따라서 배포 조직 내의 여러 팀에서 적절 한 계획이 필요 합니다. Windows Hello for Business [계획 가이드](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) Business 배포 및 고려해 야 할 옵션에 대 한 Windows Hello의 유형에 따라 결정을 내릴 수 있도록 사용할 수 있습니다.

온-프레미스 또는 하이브리드 배포용 있다고 예상 됩니다.

* 잘 연결 된 작업 네트워크

* 인터넷 액세스

* 비즈니스 프로 비전을 위한 Windows Hello는 동안 MFA를 지원 하기 위해 multi-factor Authentication 서버

* 적절 한 이름 확인, 내부 및 외부 이름

* 인증을 지원 하도록 active Directory와 사이트별으로 도메인 컨트롤러에 적절 한 수

* 2012 이상 버전의 active Directory 인증서 서비스

* Windows 10 버전 1903 실행 하는 하나 이상의 워크스테이션 컴퓨터

이후 모든 유형의 Windows Hello 비즈니스에 배포 도메인 컨트롤러에 대 한 신뢰를 루트로 엔터프라이즈에서 발급 한 인증서를 사용 하려고 하 여 만료 된 인증서를 자동으로 갱신 [서버 및 사용자에 대 한 자동 등록 구성 인증서](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/configure-server-certificate-autoenrollment)합니다. 온-프레미스 배포에 대 한 id 공급자가 Windows Server Active Directory Federation Services (AD FS) 역할을 실행 하는 온-프레미스 서버. 페더레이션 시스템에는 일반적으로 부하 분산된 서버 배열(팜이라고도 함)이 필요합니다. 이 팜은 인증 요청에 대한 고가용성을 보장하기 위해 내부 네트워크와 경계 네트워크 토폴로지에 구성됩니다.

Windows Hello에 대 한 필요를 작업 공간에서 비즈니스를 위한 그룹 정책으로 설정 하면 조직의 사용자가 Windows Hello를 사용 하는 방법을 설명 하 여 준비 하는 것이 좋습니다. 예를 들어 사용자가 새 장치를 설정 하면 메시지가 표시 됩니다 선택할 **이 장치는 내 조직에 속하는** 또는 **나만의 개인 장치**합니다. 회사 장치에 대 한 이전 선택 해야 합니다. 사용자 선택 해야 하는 연결 옵션에 알려지지도 해야 합니다. **Azure AD에 조인** 나 **로컬 계정 (도메인 가입 나중) 설정**합니다.

생체 인식 제스처를 사용 하 여 최종적으로 PIN을 잊은 인증한 후 일 수에 익숙한 사용자에 대 한 일반적인 것입니다. 지원 센터에 대 한 호출을 방지 하려면 것이 좋습니다 다시 설정 하는 기능을 사용 하 여 사용자를 제공 하는 잊어버린 [암호](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) 하 고 [Pin](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset)합니다.

배포할 때 Windows Hello for Business을 선택할 수 있는 여러 옵션이 있습니다. 여러 옵션을 제공 하면 거의 모든 조직 배포할 수 있습니다 Windows Hello 비즈니스에 대 한 합니다. 다음과 같은 유형의 지원 되는 배포를 고려 합니다.

* [키 트러스트 배포에 조인 된 하이브리드 Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [인증서 신뢰 배포에 조인 된 하이브리드 Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Azure AD 조인 Single sign-on 배포 가이드](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [프레미스 키 신뢰 배포](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [온-프레미스 인증서 신뢰 배포](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

표시 복잡 한 배포를 사용 하면 다양 한 옵션을 제공 합니다. 그러나 대부분의 조직은 확인할 수는 Windows Hello 비즈니스 배포에 대 한 종속 된 인프라의 대부분 구현 이미 했습니다. 그럼에도 불구 하 고 Windows Hello for Business는 분산된 시스템을 적절 한 계획이 것이 좋습니다. 알아야 할 것입니다.

참조 하는 것이 좋습니다 [는 Windows Hello 비즈니스 배포 계획](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) 특정 조직에 적합 한 최적의 배포 모델에 결정할 수 있도록 합니다. 그런 다음 할 계획에 따라 참조를 [Windows Hello 비즈니스 배포 가이드에 대 한](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) 기존 환경에서 Windows hello for Business를 올바르게 배포 되도록 합니다.

### <a name="how-windows-hello-for-business-works"></a>비즈니스용 Windows Hello의 작동 방식

#### <a name="user-sets-up-windows-hello-for-business"></a>Windows Hello for Business를 사용자 설정

되는 초기 2 단계 인증 후 사용자의 등록 중에 Windows Hello에 설치 되어 사용자의 장치 및 Windows 사용자는 생체 인식, 지문 또는 안 면 인식 또는 PIN 같은 수 있는 제스처를 설정 하도록 요청 합니다. 설정 되 면 사용자 id를 확인 하도록 제스처를 제공 합니다. Windows를 사용 하 여 Windows Hello 사용자를 인증 합니다.

Windows 10 장치의 기능에 따라 하거나 해야 TPM 소프트웨어 또는 하드웨어 신뢰할 수 있는 플랫폼 모듈 (TPM) 라는 기본 제공 보안 enclave 합니다. TPM에는 얼굴, 지문 또는 PIN 잠금 해제를 해야 하는 개인 키를 저장 합니다. 생체 인식 데이터 로밍 하지 및 외부 장치 또는 서버에 전송 되지 않습니다. 생체 인식 데이터를 도용 하는 공격자가 손상 될 수 있습니다 Windows Hello만 데이터를 저장 하므로 식별 생체 인식 장치에서 단일 컬렉션 지점이 없습니다.

> [!TIP]
> 화면에서 PIN 암호를 찾기란 이지만 실제로 더 안전 합니다. 암호 및 PIN의 중요 한 차이점은 된 PIN은는 설정 된 특정 장치에 연결 어디에서 나 계정에 로그인 할 수 암호를 도용 하는 사람입니다. 하지만 PIN을 훔칠 경우 문서를 업로드 하려면 물리적 장치를 너무 도용! 또한 PIN 장치에 로컬 이므로 해당 되지 않습니다 전송 아무 곳 이나 하므로 전송 중에 차단 하거나 서버에서 도난 수 없습니다.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>사용 하 여 사용자 Windows Hello for Business 로그인

Windows Hello 비즈니스 생체 인식 및 Pin 비대칭 (공개/개인 키)를 사용 하 여 인증에 대 한 암호화 합니다. 인증 하는 동안 암호화 중간자 개입 (mitm 메시지 가로채기) 공격을 아티팩트를 생성 된 보안 토큰을 도용 하 고 다른 위치에서 재생할 수 없습니다 있도록 인증 프로세스를 보호 하는 TLS 세션 키에 연결 됩니다.

Windows Hello 비즈니스에 대 한 편리한 로그인 환경을 제공 Azure AD에 사용자를 인증 하 고 Active Directory 리소스입니다. Azure AD 가입 장치에 로그인 하는 동안 Azure에 인증 하 고 필요에 따라 Active Directory에 인증할 수 있습니다. 하이브리드 Azure Active Directory 가입 장치에 로그인 하는 동안 Active Directory에 인증 하 고 백그라운드에서 Azure Active Directory에 인증 합니다.

![소스 이미지를 참조 하세요.](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

다음 단계에서는 Azure Active Directory에 대 한 로그인 인증을 보여 줍니다.

![Windows 10 잠금 화면](./media/azure-ad/azure-ad-pwdless-image3.png)

1. 사용자가 사용 하 여 Windows 생체 인식 또는 PIN 제스처입니다. 제스처는 Windows Hello 비즈니스 개인 키를의 잠금을 해제 하 고 클라우드 AP 공급자 라고 클라우드 인증 보안 지원 공급자에 게 전송 됩니다.

2. 클라우드 AP 공급자는 Azure Active Directory에서 nonce를 요청합니다.

3. Azure AD는 5 분 동안 유효 하는 nonce를 반환 합니다.

4. 클라우드 AP 공급자는 사용자의 개인 키를 사용 하 여 nonce를 서명 하 고 Azure Active Directory에 서명 된 nonce를 반환 합니다.

5. Azure Active Directory nonce 서명에 대 한 사용자의 안전 하 게 등록 된 공개 키를 사용 하 여 서명 된 nonce의 유효성을 검사 합니다. 그런 다음 Azure AD 서명의 유효성을 검사 한 후 반환 된 서명 된 nonce의 유효성을 검사 합니다. Nonce의 유효성을 검사 한 후 Azure AD는 PRT를 장치의 전송 키로 암호화 되 고 클라우드 AP 공급자에 게 반환 하는 세션 키를 사용 하 여 만듭니다.

6. 클라우드 AP 공급자 세션 키를 사용 하 여 암호화 된 PRT를 받습니다. 장치의 개인 전송 키를 사용 하 여 클라우드 AP 공급자 세션 키를 해독 하 고 장치의 TPM을 사용 하 여 세션 키를 보호 합니다.

7. Windows는 사용자가 클라우드 뿐만 아니라 Windows 액세스할 수 및 온-프레미스 다시 인증 하지 않고도 응용 프로그램을 성공적으로 인증 응답을 반환 하는 클라우드 AP 공급자 (SSO).

관련 Windows Hello 비즈니스에 대 한 다른 시나리오에서 인증 프로세스를 보다 자세히 확인할을 참조 하세요 [Windows Hello 비즈니스 및 인증을 위해](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key)합니다.

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>사용자가 해당 Windows Hello 비즈니스 자격 증명

합니다 [Microsoft PIN 재설정 서비스](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) 사용자가 필요한 경우 PIN을 재설정할 수 있도록 Azure AD의 기능입니다. 그룹 정책, Microsoft Intune 또는 호환 MDM을 사용 하 여 관리자로 구성할 수 안전 하 게 사용자를 요구 하지 않고 잊어버린된 PIN 설정을 통해 또는 잠금 화면 위에 재설정할 수 있도록 Microsoft PIN 재설정 서비스를 사용 하려면 Windows 10 장치 다시 등록 합니다.

경우에 따라 사용자가 암호를 사용 하도록 대체 해야 합니다. [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) 또 다른 기능은 Azure AD 사용자가 IT 직원에 게 문의 하지 않고도 암호를 재설정할 수 있도록 합니다. 사용자에 등록 해야 합니다 또는 셀프 서비스 암호 재설정 서비스를 사용 하 여 등록할 수 있습니다. 등록 하는 동안 사용자가 조직에서 사용 하도록 설정 하는 하나 이상의 인증 방법을 선택 합니다. SSPR을 사용 하면 신속 하 게 차단을 하 고 위치 또는 하루 중 시간에 관계 없이 작업을 계속할 수 있습니다. 를 허용 자체를 차단 해제 하 여 조직의 생산성 아닌 시간 및 가장 일반적인 암호 관련 문제에 대 한 높은 지원 비용을 줄일 수 있습니다.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>암호 없는 로그인 Microsoft Authenticator를 사용 하 여

암호 없는 로그인 Microsoft Authenticator를 사용 하 여이 휴대폰 로그인 사용 하 여 Azure AD 계정에 로그인 할 수 있는 다른 암호 없는 솔루션입니다. 여전히 알고와 해도 전화 로그인 암호를 입력을 건너뛸 수 있습니다 및 사용자의 지문, 얼굴, 또는 PIN을 사용 하 여 모바일 장치에서 모든 id 확인을 수행 함으로써 사용자의 신원을 확인 해야 합니다.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator 암호 없는 시나리오

Microsoft Authenticator 앱을 사용 하면 해당 id를 확인 하 고 해당 회사 또는 개인 계정에 인증할 수 있습니다. 일회용 암호를 사용 하 여 암호를 보강 하거나 푸시 알림 암호에 대 한 필요성을 완전히 대체 데도 수 있습니다. 사용자는 암호를 사용 하는 대신 지문 검색, 얼굴 또는 아이리스 인식 또는 PIN을 통해 해당 휴대폰을 사용 하 여 해당 id를 확인 합니다. 새로운 Windows Hello를 사용 하는 유사한 보안 기술을 기반으로,이 도구는 편리한 옵션을 사용자가 쉽게 모바일 장치에서 간단한 앱에 패키지 됩니다. Microsoft Authenticator 앱은 Android 및 iOS에 대 한 합니다.

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator 배포 고려 사항

Azure ad 암호 없는 로그인을 위해 Microsoft Authenticator 앱 사용에 대 한 필수 구성 요소는 다음과 같습니다.

* Azure Multi-factor Authentication에 대 한 최종 사용자를 활성화

* Microsoft Intune 또는 타사 모바일 장치 관리 (MDM) 솔루션을 사용 하 여 장치를 등록할 사용자를 위한 기능

이러한 요구 사항을 충족 하는 것으로 가정 합니다 관리자 암호 없는 전화 로그인 사용 테 넌 트에서를 사용 하 여 [Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) 휴대폰 로그인이 테 넌 트에서 활성화 되 면 최종 사용자에 게 않도록 선택할 수에서 회사 또는 학교 계정으로 선택 하 여 휴대폰을 사용 하 여 로그인 합니다 **계정** 선택 하는 앱의 화면 **휴대폰 로그인 사용** .

암호 없는 로그인 관리자가 활성화 가정 하 고, 최종 사용자가 다음 요구 사항을 충족 해야 합니다.

* Azure Multi-factor Authentication에 등록

* 최신 버전의 Microsoft Authenticator 8.0 이상, iOS를 실행 하는 장치 또는 Android 6.0에 설치 된 이상

* 푸시 알림 앱에 추가 사용 하 여 회사 또는 학교 계정

잠기거나 가능성을 방지 하려면 계정 또는 새 장치에서 계정을 다시 만들지 않아도 것이 좋습니다에 Microsoft Authenticator를 사용 하는 [계정 자격 증명 백업](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) 클라우드입니다. 백업 후에는 앱을 사용하여 정보를 새 디바이스에 복구하면 계정이 잠기거나 계정을 다시 만들어야 하는 문제를 피할 수 있습니다.

대부분의 사용자만 암호를 사용 하 여 인증 하는 데 익숙한, 이므로 조직 교육이 프로세스에 대 한 사용자는 중요 합니다. 인식 사용자에 대 한 기술 지원팀을 호출 하는 가능성을 줄일 수 있습니다 [문제](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) 와 관련 된 Microsoft Authenticator 앱을 사용 하 여 로그인 합니다.

> [!NOTE]
> 이 솔루션에 대 한 오류의 잠재적인 지점을 경우 로밍 사용자가 위치에 인터넷 연결이 없는 경우. FIDO2 보안 키 및 Windows Hello 비즈니스에 대 한 동일한 제한이 적용 되지 않습니다.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Microsoft Authenticator works와에 어떻게 암호 없는 로그인

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>사용자 설정 암호 없는 로그인 Microsoft Authenticator를 사용 하 여

Microsoft Authenticator 앱을 Azure AD 계정에 로그인 할 암호 없는 솔루션으로 사용할 수 있습니다, 관리자와 최종 사용자가 단계를 수행 해야 합니다.

먼저 관리자로 해야 [앱의 자격 증명으로 사용할 수 있도록](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) Windows PowerShell을 사용 하 여 테 넌 트에서입니다. 관리자가 Azure Multi-factor Authentication (Azure MFA)에 대 한 최종 사용자를 활성화 하 고 중 하나로 Microsoft Authenticator 앱을 구성 해야 합니다 [인증 방법을](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods)합니다.

최종 사용자가 해야 [다운로드 및 설치](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) Microsoft Authenticator 앱 및 [계정 설정](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) 확인 방법 중 하나로 Microsoft Authenticator 앱을 사용 하도록 합니다.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Microsoft Authenticator를 사용 하 여 암호 없는 로그인에 대 한 사용자

암호를 사용하지 않고 Microsoft Authenticator 앱을 사용하여 모든 Azure AD 계정에 로그인할 수 있습니다. Windows 10 잠금 화면에서 로그인 옵션으로 Microsoft Authenticator 앱은 포함 되지 않습니다 하는 동안 사용자가 여전히 자신의 사용자 이름을 입력 하 고 현재 상태를 확인 하려면 모바일 장치에서 푸시 알림을 받을 수 있습니다. 사용자가 로그인 화면에서 숫자를 일치 하는 다음 검색 얼굴, 지문 또는 개인 키를 잠금 해제 하 고 인증을 완료 하려면 PIN을 제공 하 여 현재 상태를 확인 합니다. 이 다단계 인증 방법은 암호 보다 더 안전 하 고 암호 및 코드를 입력 하는 보다 편리 합니다.

![로그인 인증자](./media/azure-ad/azure-ad-pwdless-image4.png)

Microsoft Authenticator를 사용 하 여 암호 없는 인증 그대로 Windows Hello for Business 하지만 좀 더 복잡 한 사용자가 해당 Azure AD는 Microsoft Authenticator 앱 버전은 찾을 수 있도록 식별 해야 하므로 동일한 기본 패턴을 따릅니다. 사용.

![인증 프로세스](./media/azure-ad/azure-ad-pwdless-image5.png)

1. 사용자가 자신의 사용자 이름을 입력 합니다.

2. Azure AD는 사용자가 강력한 자격 증명을 고이 강력한 자격 증명 흐름을 시작 하는 검색 합니다.

3. 통해 푸시 알림 서비스 (APNS (Apple) iOS 장치에서 또는 통해 FCM Firebase Cloud Messaging () Android 장치에서 앱에 알림이 전송 됩니다.

4. 사용자는 푸시 알림을 수신 하 고 앱을 엽니다.

5. Azure AD를 호출 하 고 현재 상태 증명의 챌린지 및 nonce를 수신 하는 앱.

6. 사용자가 생체 인식 또는 PIN 개인 키의 잠금을 해제 하려면를 입력 하 여 챌린지를 완료 합니다.

7. Nonce는 개인 키로 서명 되 고 Azure AD로 다시 전송 합니다.

8. Azure AD는 공개/개인 키 유효성 검사를 수행 하 고 토큰을 반환 합니다.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Microsoft Authenticator 자격 증명을 사용 하 여 암호 없는 로그인 사용자 관리

사용 하 여 [등록 결합](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), 사용자 등록 및 Azure Multi-factor Authentication 및 셀프 서비스 암호 재설정의 이점을 얻을 수 있습니다. 등록 하 고로 이동 하 여 이러한 설정을 관리 하는 사용자 자신의 [내 프로필 페이지](https://aka.ms/mysecurityinfo)합니다. SSPR을 사용 하도록 설정 하는 것 외에도 여러 인증 방법 및 작업 등록 지원을 결합 합니다.

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO2는 FIDO Alliance 표준의 최신 버전 이며의 두 구성 요소-W3C의 웹 인증 (WebAuthN) 표준과 해당 FIDO Alliance 클라이언트-Authenticator 프로토콜 (CTAP2). FIDO2 표준을 사용 하면 하드웨어, 모바일 및 생체 인식 기반 인증할 인증자를 쉽게 여러 앱 및 모바일 및 데스크톱 환경에서 웹 사이트를 사용 하 여 활용할 수 있도록 합니다.

Microsoft 및 업계 파트너 FIDO2 보안에 대해 장치에서 Windows Hello 공유 장치의 손쉽고 안전한 인증을 사용 하도록 함께 왔습니다. FIDO2 보안 키를 자격 증명을 수행 하 고 안전 하 게 인증할 수 있습니다는 [Azure AD](https://aka.ms/azuread418)-조직의 일부인 Windows 10 장치를 조인된 합니다.

WebAuthN 개발 및 강력한 구현의 웹 앱 및 서비스에서 암호 없는 인증을 사용 하도록 설정 하는 API를 정의 합니다. CTAP 프로토콜 WebAuthN 작업 및 인증자 역할 규격 FIDO 보안 키와 같은 외부 장치를 수 있습니다. 웹 인증을 사용 하 여 사용자가 로그인 할 수는 얼굴, 지문, PIN 또는 이식 가능한 FIDO2 보안 키를 사용 하 여 온라인 서비스 암호 대신 강력한 공개 키 자격 증명을 활용 하 여 합니다. 현재 WebAuthN Microsoft Edge 및 Chrome에 대 한 지원을 사용할 및 Firefox 개발 중입니다.

암호를 사용 하지 않고 장치와 FIDO2, WebAuthN, 및 CTAP 프로토콜을 준수 하는 토큰에 대 한 강력한 인증의 플랫폼 간 솔루션을 가져옵니다. Microsoft 파트너 중 다양 한 nfc 지원 스마트 카드 및 USB 보안 키와 같은 보안 키 형식 요소입니다.

### <a name="fido2-security-keys-scenarios"></a>FIDO2 보안 키 시나리오

Windows 10 잠금 화면에서 자격 증명 공급자로 보안 키를 선택 하 여 Azure AD에 로그인 할 FIDO2 보안 키를 사용할 수 있습니다. 사용자 이름 또는 암호가 아닙니다이 통해 이상적인 솔루션 Pc 여러 명이 공유 하는 첫 번째 줄 작업자에 대 한 필요 합니다. 회사 정책에 명시 되어 사용자의 자격 증명 장치의 물리적으로 분리 해야 하는 경우 뛰어난 인증 옵션 이기도 합니다. 사용자가 Windows 10 버전 1809 이상에서 Microsoft Edge 브라우저 내에서 해당 FIDO2 보안 키를 사용 하 여 웹 사이트에 로그인 할 수도 있습니다.

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 보안 키 배포 고려 사항

관리자는 Azure AD에서 FIDO2 지원을 사용 하도록 설정 하 고 사용자 또는 그룹 기능을 할당할 수 있습니다. 정책 키 프로 비전 되 고 허용 하거나 차단 하는 하드웨어 보안 키의 특정 집합을 같은 제한을 구성 하는 방법에 대해 만들 수도 있습니다. 최종 사용자에 게 키를 물리적으로 배포 되어야 합니다.

**암호 없는 사용 하도록 설정 하는 것에 대 한 요구 사항을 Azure AD에 로그인 하 고 FIDO2 보안 키를 사용 하 여 웹 사이트는 다음과 같습니다.**

* Azure AD

* Azure Multi-Factor Authentication

* 등록 미리 보기를 결합합니다.

* FIDO2 보안 키 미리 보기에는 호환 FIDO2 보안 키에 필요

* 웹 인증 (WebAuthN) Windows 10 버전 1809 이상에서 Microsoft Edge에 필요한

* 에 따라 Windows 로그인에 Azure AD가 필요한 FIDO2 가입 Windows 10 버전 1809 이상 (Windows 10 버전이 1903 이상는 최상의 환경을)

FIDO2 규격 폼 팩터 USB, NFC, 및 Bluetooth 장치를 포함합니다. 일부 플랫폼 이후 특정 요구를 충족 하는 폼 팩터 선택한 브라우저 아직 FIDO2 준수 하지 않는 것이 좋습니다.

각 조직 사용자에 대 한 프로토콜을 만들를 수행 하려면 관리자는 보안도 권장 도난당 하거나 분실 될 키입니다. 사용자나 관리자가 사용자의 프로필에서 해당 보안 키를 삭제 하 고 새로 프로 비전 할 수 있도록 사용자가 분실 하거나 도난당 한 키를 보고 해야 합니다.

### <a name="how-fido2-security-keys-works"></a>FIDO2 보안 키의 작동 원리

#### <a name="user-sets-up-fido2-security-key"></a>FIDO2 보안 키를 설정 하는 사용자

관리자 수 있지만 [키를 수동으로 프로 비전](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) 최종 사용자에 게 배포를 통해 프로 비전 하 고 Windows 10 잠금 화면에서 FIDO2 자격 증명 공급자를 사용 하도록 설정 지원 되며 [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). 관리자를 사용 해야 합니다 [Azure portal](https://portal.azure.com/) 암호 없는 인증 방법으로 하드웨어 토큰 장치를 사용 하도록 설정 합니다.

사용자가 사용 하 여 해당 키를 등록 하는 해야 FIDO2 보안 키를 배포 [등록 결합](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)합니다. 결합 된 등록을 사용 하 여 사용자가 한 번 등록 하 고 Azure Multi-factor Authentication 및 single sign-on 암호 재설정 (SSPR)의 이점을 누릴.

기본 multi-factor authentication 인증 방법으로 하드웨어 토큰을 선택 하는 것 외에도 추가 인증 옵션을 선택할 수도 것이 좋습니다.

* Microsoft Authenticator-알림

* Authenticator 앱 또는 하드웨어 토큰-코드

* 전화 통화

* 문자 메시지

#### <a name="user-using-fido2-security-key-for-sign-in"></a>FIDO2 보안 키를 사용 하 여 로그인에 대 한 사용자

FIDO2는 인증자 및 공개/개인 키 암호화 개인 키를 확인 하 고 공개 키를 배달 합니다. Windows Hello 및 보안 키와 같은 기본 제공 플랫폼 인증자를 사용 하도록 설정 하는 데 사용 되는 폼 팩터 간에 추상화 계층을 제공 사용할 수 있는 식별자로 외부 리소스에 액세스 합니다. FIDO2 보안 키에는 개인 키를 저장 하 고는 생체 인식 또는 PIN 잠금 해제를 요구 하는 고유한 기본 제공 보안 enclave 장착 되어 있습니다. 자격 증명 다시 사용할 수 없습니다, 재생, 또는 서비스 간에 공유 및 위반 피싱 및 MiTM 공격 또는 서버에 적용 되지 않습니다.

![FIDO2 로그인](./media/azure-ad/azure-ad-pwdless-image6.png)

FIDO2 보안 키는 폼 팩터 관계 없이 보안 인증을 제공합니다. 보안 키 자격 증명을 보유 하 고 (보안 키에 통합) 지문 또는 Windows 로그인 시 입력 핀과 같은 추가 두 번째 요소를 사용 하 여 보호 되어야 합니다. Microsoft 파트너 중 다양 한 보안 키 형식 요소입니다. 몇 가지 예로 USB 보안 키 및 NFC 스마트 카드를 사용 하도록 설정 합니다.

> [!NOTE]
> 특정 기능 및 확장 되도록 FIDO2 CTAP 프로토콜에서 보안 키 구현 해야 합니다 [Microsoft 호환](https://aka.ms/fido2securitykeys)합니다. Microsoft는 Windows 10 및 Azure Active Directory와의 호환성에 대 한 이러한 솔루션을 테스트 합니다.

![FIDO2 로그인 프로세스](./media/azure-ad/azure-ad-pwdless-image9.png)

1. 사용자는 컴퓨터에 FIDO2 장치를 연결 합니다.

2. Windows는 FIDO2 보안 키를 검색합니다.

3. Windows 인증 요청을 보냅니다.

4. Azure AD에서 nonce 다시 보냅니다.

5. 사용자가 제스처 FIDO2 보안 키의 보안 enclave에 저장 된 개인 키를 잠금 해제를 완료 합니다.

6. 개인 키를 사용 하 여 nonce를 서명 하는 FIDO2 보안 키입니다.

7. 서명 된 nonce 사용 하 여 PRT 토큰 요청은 Azure AD로 전송 됩니다.

8. Azure AD FIDO2 공개 키를 사용 하 여 서명 된 nonce를 확인 합니다.

9. Azure AD는 온-프레미스 리소스에 액세스할 수 있도록 PRT를 반환 합니다.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>사용자 관리 FIDO2 보안 키 자격 증명

Microsoft Authenticator 앱과 마찬가지로 FIDO2 보안 키에 대 한 자격 증명 관리 최종 사용자에 대 한 결합 된 등록 환경에 의존합니다.

## <a name="deciding-a-passwordless-method"></a>암호 없는 메서드를 결정합니다.

암호 없는 이러한 세 가지 옵션 중에서 선택 하는 작업은 회사의 보안, 플랫폼 및 앱 요구 사항에 따라 다릅니다.

Microsoft 암호 없는 기술을 선택할 때 고려해 야 할 몇 가지 요소는 다음과 같습니다.

||**비즈니스용 Windows Hello**|**암호 없는 로그인 Microsoft Authenticator 앱을 사용 하 여**|**FIDO2 보안 키**|
|:-|:-|:-|:-|
|**필수 구성 요소**| Windows 10 버전 1809 이상<br>Azure Active Directory| Microsoft Authenticator 앱<br>전화 (iOS 및 Android 장치를 Android 6.0을 실행 이상.)|Windows 10 버전 1809 이상<br>Azure Active Directory|
|**모드**|플랫폼|소프트웨어|하드웨어|
|**시스템 및 장치**|PC 사용 하 여 기본 제공 신뢰할 수 있는 플랫폼 모듈 (TPM)<br>PIN 및 생체 인식 |휴대폰에서 PIN 및 생체 인식|Microsoft 호환 FIDO2 보안 장치|
|**사용자 환경**|PIN 또는 생체 인식 (얼굴, 아이리스, 또는 지문)를 사용 하 여 Windows 장치를 사용 하 여 로그인 합니다.<br>Windows Hello 인증 된 장치에 연결 됩니다. 사용자 장치 및 회사 리소스에 액세스 하려면 PIN 또는 생체 인식 요소와 같은 로그인 구성 해야 합니다.|지문 검색, 얼굴 또는 아이리스 인식, 휴대 전화를 사용 하 여 로그인 하거나 고정 합니다.<br>사용자가 자신의 PC 또는 모바일 폰에서, 회사 또는 개인 계정에 로그인합니다.|FIDO2 보안 장치 (생체 인식, PIN 및 NFC)을 사용 하 여 로그인<br>사용자는 조직 컨트롤에 따라 장치에 액세스 하 고에 따라 PIN, 생체 인식 USB 보안 키 및 스마트 카드 nfc 지원, 키 또는 착용 식 장치 등 장치를 사용 하 여 인증할 수 있습니다.|
|**사용된 시나리오**| Windows 장치를 사용 하 여 암호 없는 환경.<br>전용된 작업 PC에 대 한 single sign on 기능을 사용 하 여 장치 및 해당 응용 프로그램입니다.|암호 없는 아무 곳 이나 휴대폰을 사용 하 여 솔루션입니다.<br>모든 장치에서 회사 또는 개인 응용 프로그램 웹에 액세스 하기 위해 적용할 수 있습니다.|생체 인식, PIN 및 NFC를 사용 하 여 작업자에 대 한 암호를 사용 하지 않는 환경.<br>공유 Pc 및 여기서 휴대 전화를 연결이 실행 가능한 옵션이 아닙니다 (예: 지원 센터 담당자, 공용 키오스크 또는 병원 팀)에 적용|

다음 표에서를 사용 하는 메서드가 지원할 요구 사항 및 사용자를 선택 합니다.

|가상 사용자|시나리오|환경|암호 없는 기술|
|:-|:-|:-|:-|
|**관리자**|관리 작업에 대 한 장치에 대 한 보안 액세스|할당 된 Windows 10 장치|Windows Hello 비즈니스 및/또는 FIDO2 보안 키|
|**관리자**|비 Windows 장치에서 관리 작업| 휴대폰 또는 비 windows 장치|암호 없는 로그인 Microsoft Authenticator 앱을 사용 하 여|
|**정보 근로자**|생산성 작업|할당 된 Windows 10 장치|Windows Hello 비즈니스 및/또는 FIDO2 보안 키|
|**정보 근로자**|생산성 작업| 휴대폰 또는 비 windows 장치|암호 없는 로그인 Microsoft Authenticator 앱을 사용 하 여|
|**메일용 작업자**|팩터리, 공장, 일반 정품 또는 데이터 항목에서 키오스크|Windows 10 장치를 공유|FIDO2 보안 키|

## <a name="getting-started"></a>시작

암호 없는 인증은의 미래 및 보다 안전한 환경 경로 경향입니다. 조직에서는이 변경에 대 한 계획을 시작 하는 것이 좋습니다 암호에 대 한 종속성을 줄이고 있습니다. 시작 하려면 다음과 같은 목표를 고려 합니다.

* MFA + Microsoft Authenticator 앱 조건부 액세스에 대 한 사용자를 사용 합니다.

* Azure AD 암호 보호 롤아웃 + 업데이트 정책입니다.

* 결합 된 등록을 사용 하 여 SSPR에 대 한 사용자를 사용 합니다.

* 이동성에 대 한 Microsoft Authenticator 앱을 배포 합니다.

* 배포 Windows Hello for Business (1903: 최신 상태로 유지).

* 휴대폰을 사용할 수 없는 사용자에 대 한 FIDO2 장치를 배포 합니다.

* 가능한 경우 암호 기반 인증을 사용 하지 않도록 설정 합니다.

이러한 목표를 달성 하려면 다음 방법을 좋습니다.

1. Azure MFA 및 조건부 액세스 등의 기능 활용 하기 위해 Azure Active Directory를 사용 하도록 설정 합니다.

2. 보호 하기 위해 multi-factor authentication을 사용 합니다.

3. 사용자가 암호를 사용 하도록 대체 해야 이벤트에 셀프 서비스 암호 재설정을 사용 합니다.

4. Microsoft Authenticator 휴대폰 로그인 추가 이동성에 대 한 배포 합니다.

5. 배포 Windows Hello 비즈니스에 대 한 모든 Windows 10 장치에 있습니다.

6. FIDO2 보안 키를 준비 합니다.

> [!NOTE]
> Azure Active Directory 가리킵니다 [라이선스 페이지](https://azure.microsoft.com/pricing/details/active-directory/) 암호 없는 메서드에 대 한 라이선스 요구 사항에 대 한 세부 정보에 대 한 합니다.

## <a name="conclusion"></a>결론

지난 몇 년간 Microsoft는 암호 없이 세계를 사용 하도록 설정 하기를 계속 합니다. Windows 10을 사용 하 여 Microsoft 도입 된 Windows Hello 비즈니스에 강력한, 하드웨어 single-sign Azure Active Directory 및 Active Directory를 사용 하도록 설정 하는 이중 자격 증명을 보호 합니다. 와 마찬가지로 Windows Hello for Business의 기술, Microsoft Authenticator 앱을 사용 하 여 키 기반 인증 모바일 장치에 연결 되 고는 생체 인식 또는 PIN을 사용 하는 사용자 자격 증명을 사용 하도록 설정. 이제 FIDO2 보안 키를 자격 증명을 수행 하 고 Windows 10 잠금 화면에서 자격 증명 공급자로 보안 키를 선택 하 여 Azure AD에 로그인 할 수 있습니다. 암호 없는이 솔루션의 세 가지 모두 암호 스프레이 피싱 위험을 줄이려면 재생 공격 하 고 사용자에 게 로그인 하 고 어디에서 나 데이터에 액세스 하는 매우 안전 하 고 편리한 방법을 제공 합니다.

생체 인식 및 광범위 하 게 액세스할 수 있는 장치에서 공개 키 암호화와 같은 최신 multi-factor authentication 인증 기술을 도입 의미에 따라 회사의 identity 위험을 줄일 수 있는 가장 강력한 단계 중 하나입니다. 암호 없는 장기적인 관점에서 보안 인증에 대 한 및 여전히 진행 중입니다. 새로운 요구 사항 지정 되 면 조직 준비할 수 자체 암호 없는 기술을 이동 하려면 계획을 만들어 합니다.

## <a name="next-steps"></a>다음 단계

* 개요 [암호 없는 란?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Azure AD에서 암호 없는 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)
