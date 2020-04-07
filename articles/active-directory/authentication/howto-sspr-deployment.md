---
title: Azure Active Directory 셀프 서비스 암호 재설정에 대한 배포 고려 사항
description: Azure AD 셀프 서비스 암호 재설정의 성공적인 구현을 위한 배포 고려 사항 및 전략에 대해 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11521ec074b63843b873c39102b68bf185d2821
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676735"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Azure Active Directory 셀프 서비스 암호 재설정 배포 계획

> [!IMPORTANT]
> 이 배포 계획은 Azure AD 셀프 서비스 암호 재설정(SSPR)을 배포하기 위한 지침 및 모범 사례를 제공합니다.
>
> **최종 사용자이고 계정에 다시 로그인해야 하는 경우 로 [https://aka.ms/sspr](https://aka.ms/sspr)이동하십시오. **

[셀프 서비스 암호 재설정(SSPR)은](https://www.youtube.com/watch?v=tnb2Qf4hTP8) 사용자가 IT 직원에게 문의하지 않고 암호를 재설정할 수 있는 Azure Active Directory(AD) 기능입니다. 사용자는 신속하게 자신을 차단을 해제하고 그들이 어디에 있든 하루 중 시간에 상관없이 작업을 계속할 수 있습니다. 직원들이 스스로 차단을 해제할 수 있도록 허용함으로써 조직은 가장 일반적인 암호 관련 문제에 대한 비생산적인 시간과 높은 지원 비용을 줄일 수 있습니다.

SSPR에는 다음과 같은 주요 기능이 있습니다.

* 셀프 서비스를 사용하면 최종 사용자가 관리자 나 헬프 데스크에 문의하지 않고 만료되었거나 만료되지 않은 암호를 재설정할 수 있습니다.
* [암호 쓰기 를](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) 사용하면 온-프레미스 암호를 관리하고 클라우드를 통해 계정 잠금을 해결할 수 있습니다.
* 암호 관리 활동 보고서는 관리자에게 조직에서 발생하는 암호 재설정 및 등록 활동에 대한 통찰력을 제공합니다.

이 배포 가이드에서는 SSPR 롤아웃을 계획하고 테스트하는 방법을 보여 주며, 이를 보여 주실 수 있습니다.

SSPR이 실행 중인 것을 신속하게 확인하고 다시 돌아와 추가 배포 고려 사항을 이해하려면 다음을 수행하십시오.

> [!div class="nextstepaction"]
> [셀프 서비스 암호 재설정(SSPR) 활성화](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>SSPR에 대해 알아보기

SSPR에 대해 자세히 알아보십시오. [작동 방식: Azure AD 셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>주요 이점

SSPR을 사용하도록 설정하는 주요 이점은 다음과 같습니다.

* **비용을 관리합니다.** SSPR은 사용자가 스스로 암호를 재설정할 수 있도록 하여 IT 지원 비용을 절감합니다. 또한 암호 분실 및 잠금으로 인해 손실되는 시간 비용을 줄일 수 있습니다. 

* **직관적 인 사용자 경험**. 사용자가 암호를 재설정하고 모든 장치 또는 위치에서 주문형 계정 차단을 해제할 수 있는 직관적인 일회성 사용자 등록 프로세스를 제공합니다. SSPR을 통해 사용자는 더 빠르게 작업하고 생산성을 높일 수 있습니다.

* **유연성과 보안**. SSPR을 사용하면 기업이 클라우드 플랫폼이 제공하는 보안 및 유연성에 액세스할 수 있습니다. 관리자는 새로운 보안 요구 사항을 수용하기 위해 설정을 변경하고 로그인을 방해하지 않고 사용자에게 이러한 변경 내용을 롤아웃할 수 있습니다.

* **강력한 감사 및 사용 추적**. 조직은 사용자가 자신의 암호를 재설정하는 동안 비즈니스 시스템의 보안을 유지할 수 있습니다. 강력한 감사 로그에는 암호 재설정 프로세스의 각 단계에 대한 정보가 포함됩니다. 이러한 로그는 API에서 사용할 수 있으며 사용자가 데이터를 SIEM(보안 인시던트 및 SIEM) 시스템으로 가져올 수 있도록 합니다.

### <a name="licensing"></a>라이선스

Azure Active Directory는 사용자별로 라이선스가 부여되므로 각 사용자가 사용하는 기능에 대해 적절한 라이선스가 필요합니다. SSPR에 대한 그룹 기반 라이선스를 권장합니다. 

에디션 및 기능을 비교하고 그룹 또는 사용자 기반 라이선스를 사용하도록 설정하려면 [Azure AD 셀프 서비스 암호 재설정에 대한 라이선스 요구 사항을](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)참조하세요.

가격 책정에 대한 자세한 내용은 [Azure Active Directory 가격 책정을](https://azure.microsoft.com/pricing/details/active-directory/)참조하십시오.

### <a name="prerequisites"></a>사전 요구 사항

* 적어도 평가판 라이선스가 설정된 작동 중인 Azure AD 테넌트 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 전역 관리자 권한이 있는 계정


### <a name="training-resources"></a>교육 리소스

| 리소스| 링크 및 설명 |
| - | - |
| 동영상| [더 나은 IT 확장성으로 사용자 역량 강화](https://youtu.be/g9RpRnylxS8) 
| |[셀프 서비스 암호 재설정이란 무엇입니까?](https://youtu.be/hc97Yx5PJiM)|
| |[셀프 서비스 암호 재설정 배포](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Azure AD의 사용자에 대한 셀프 서비스 암호 재설정을 구성하는 방법은 무엇입니까?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Azure Active Directory에 [사용자] 보안 정보를 등록하도록 준비하는 방법](https://youtu.be/gXuh0XS18wA) |
| 온라인 과정|[Microsoft Azure 활성 디렉터리에서 ID 관리](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) SSPR을 사용하여 사용자에게 현대적 보호 환경을 제공합니다. 특히["Azure Active Directory 사용자 및 그룹 관리"](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)모듈을 참조하십시오. |
|복수시력 유료 코스 |[ID 및 액세스 관리 문제](https://www.pluralsight.com/courses/identity-access-management-issues) 조직에서 알아야 할 IAM 및 보안 문제에 대해 알아봅니다. 특히 "기타 인증 방법" 모듈을 참조하십시오.|
| |[마이크로소프트 엔터프라이즈 모빌리티 제품군 시작](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) 인증, 권한 부여, 암호화 및 보안 모바일 환경을 허용하는 방식으로 온-프레미스 자산을 클라우드로 확장하는 모범 사례를 알아봅니다. 특히 "Microsoft Azure Active Directory Premium의 고급 기능 구성" 모듈을 참조하십시오.
|자습서 |[Azure AD 셀프 서비스 암호 재설정 파일럿 롤아웃 완료](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[암호 쓰기 저장을 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Windows 용 로그인 화면에서 Azure AD 암호 재설정 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| FAQ|[암호 관리가 자주 묻는 질문](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>솔루션 아키텍처

다음 예제는 일반적인 하이브리드 환경에 대한 암호 재설정 솔루션 아키텍처에 대해 설명합니다.

![솔루션 아키텍처 다이어그램](./media/howto-sspr-deployment//solutions-architecture.png)

워크플로에 대한 설명

암호를 재설정하려면 사용자가 [암호 재설정 포털로](https://aka.ms/sspr)이동합니다. 신원을 증명하기 위해 이전에 등록된 인증 방법 또는 방법을 확인해야 합니다. 암호를 성공적으로 재설정하면 재설정 프로세스가 시작됩니다.

* 클라우드 전용 사용자의 경우 SSPR은 새 암호를 Azure AD에 저장합니다. 

* 하이브리드 사용자의 경우 SSPR은 Azure AD Connect 서비스를 통해 온-프레미 Active Directory에 암호를 다시 씁니다. 

참고: [암호 해시 동기화(PHS)가](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 비활성화된 사용자의 경우 SSPR은 온-프레미 Active Directory에만 암호를 저장합니다.

### <a name="best-practices"></a>모범 사례

조직에서 널리 사용되는 다른 응용 프로그램 이나 서비스와 함께 SSPR을 배포 하여 사용자가 신속 하 게 등록 할 수 있습니다. 이 작업을 수행하면 많은 양의 로그인이 생성되고 등록이 유도됩니다.

SSPR을 배포하기 전에 각 암호 재설정 호출의 수와 평균 비용을 결정할 수 있습니다. 이 데이터 포스트 배포를 사용하여 SSPR이 조직에 가져오는 가치를 표시할 수 있습니다.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>SSPR 및 MFA에 대한 통합 등록 활성화

조직에서 SSPR 및 다단계 인증에 대한 통합 등록 환경을 사용하도록 설정하는 것이 좋습니다. 이 통합 등록 환경을 사용하도록 설정하면 두 기능을 모두 활성화하려면 등록 정보를 한 번만 선택하면 됩니다.

결합된 등록 환경은 조직이 SSPR 및 Azure 다단계 인증을 모두 사용하도록 설정할 필요가 없습니다. 결합된 등록은 조직에 더 나은 사용자 환경을 제공합니다. 자세한 내용은 [결합 보안 정보 등록(미리 보기)을](concept-registration-mfa-sspr-combined.md) 참조하십시오.

## <a name="plan-the-deployment-project"></a>구축 프로젝트 계획

사용자 환경에서 이 배포에 대한 전략을 결정하는 동안 조직의 요구 사항을 고려합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 이해 관계자 참여

기술 프로젝트가 실패하면 일반적으로 영향, 결과 및 책임에 대한 불일치 기대로 인해 그렇게 합니다. 이러한 위험을 방지하려면 [올바른 이해 관계자와](https://aka.ms/deploymentplans) 프로젝트의 이해 관계자 역할을 잘 이해하여 이해 관계자와 프로젝트 입력 및 책임 사항을 문서화해야 합니다.

#### <a name="required-administrator-roles"></a>필수 관리자 역할


| 비즈니스 역할/페르소나| Azure AD 역할(필요한 경우) |
| - | - |
| 레벨 1 헬프 데스크| 암호 관리자 |
| 레벨 2 헬프 데스크| 사용자 관리자 |
| SSPR 관리자| 전역 관리자 |


### <a name="plan-communications"></a>통신 계획

통신은 새로운 서비스의 성공에 매우 중요합니다. 사용자 환경이 어떻게 변경되는지, 언제 변경될지, 문제가 발생할 경우 지원을 받는 방법에 대해 사전에 사용자와 통신해야 합니다. Microsoft [다운로드 센터에서 셀프 서비스 암호 재설정 롤아웃 자료를](https://www.microsoft.com/download/details.aspx?id=56768) 검토하여 최종 사용자 통신 전략을 계획하는 방법에 대한 아이디어를 확인합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

SSPR의 초기 구성은 테스트 환경에 있는 것이 좋습니다. 조직의 사용자 하위 집합에 대해 SSPR을 사용하도록 설정하여 파일럿 그룹으로 시작합니다. [시험 운용에 대한 모범 사례를](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)참조하십시오.

그룹을 만들려면 [Azure Active Directory 에서 그룹을 만들고 구성원을 추가하는](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)방법을 참조하세요. 

## <a name="plan-configuration"></a>계획 구성

권장 값과 함께 SSPR을 사용하려면 다음 설정이 필요합니다.

| 영역 | 설정 | 값 |
| --- | --- | --- |
| **SSPR 속성** | 셀프 서비스 암호 재설정 사용 | 파일럿 을 위한 **선택된** 그룹 / 생산을 위한 **모든** |
| **인증 방법** | 등록에 필요한 인증 방법 | 재설정에 필요한 것보다 항상 1 이상 |
|   | 재설정에 필요한 인증 방법 | 1개 또는 2개 |
| **등록** | 로그인 시 사용자가 등록하도록 요구 | 예 |
|   | 사용자가 인증 정보를 다시 확인해야 하기 전의 일 수 | 90 – 180일 |
| **알림** | 사용자에게 암호 재설정에 대해 알림 | 예 |
|   | 다른 관리자가 암호를 재설정하면 모든 관리자에게 알림 | 예 |
| **사용자 지정** | 헬프 데스크 링크 사용자 지정 | 예 |
|   | 사용자 지정 헬프 데스크 이메일 또는 URL | 지원 사이트 또는 이메일 주소 |
| **온-프레미스 통합** | 온-프레미스 AD에 암호를 다시 쓰기 | 예 |
|   | 사용자가 암호를 재설정하지 않고 계정 잠금을 해제할 수 있도록 허용 | 예 |

### <a name="sspr-properties"></a>SSPR 속성

SSPR을 사용하도록 설정하는 경우 파일럿 환경에서 적절한 보안 그룹을 선택합니다.

* 모든 사람에게 SSPR 등록을 적용하려면 **모두** 옵션을 사용하는 것이 좋습니다.
* 그렇지 않으면 적절한 Azure AD 또는 AD 보안 그룹을 선택합니다.

### <a name="authentication-methods"></a>인증 방법

SSPR을 사용하도록 설정하면 사용자는 관리자가 활성화한 인증 방법에 데이터가 있는 경우에만 암호를 재설정할 수 있습니다. 방법은 전화, 인증자 응용 프로그램 알림, 보안 질문 등을 포함한다. 자세한 내용은 [인증 방법이란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

다음 인증 방법 설정을 권장합니다.

* **등록하는 데 필요한 인증 방법을** 재설정하는 데 필요한 숫자보다 하나 이상 설정합니다. 여러 인증을 허용하면 사용자가 재설정해야 할 때 유연성을 확보할 수 있습니다.

* 조직에 적합한 수준으로 **재설정하는 데 필요한 메서드 수를** 설정합니다. 하나는 최소한의 마찰을 필요로하지만, 두 보안 자세를 증가시킬 수 있습니다. 

참고: 사용자는 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)의 암호 정책 및 제한 사항에 구성된 인증 방법이 있어야 합니다.

### <a name="registration-settings"></a>등록 설정

집합 **예에** **로그인할 때 사용자가 등록하도록 요구합니다.** 이 설정을 사용하려면 로그인할 때 사용자가 등록해야 하므로 모든 사용자가 보호됩니다.

조직에 더 짧은 기간에 대한 비즈니스 요구가 없는 경우, 사용자가 인증 정보를 **90일에서** **180일** 사이로 **재확인하라는 메시지가** 표시됩니다.

### <a name="notifications-settings"></a>알림 설정

다른 관리자가 암호를 **예로**재설정할 때 **암호 재설정시 사용자에게 알림과** **모든 관리자에게 알림** 모두를 구성합니다. 둘 다에서 **예를** 선택하면 사용자가 암호를 재설정할 때 이를 인식할 수 있으므로 보안이 강화됩니다. 또한 관리자가 암호를 변경할 때 모든 관리자가 이를 인식할 수 있도록 합니다. 사용자 또는 관리자가 알림을 받고 변경을 시작하지 않은 경우 잠재적인 보안 문제를 즉시 보고할 수 있습니다.

### <a name="customization-settings"></a>사용자 지정 설정

문제가 발생하는 사용자가 즉시 도움을 받을 수 있도록 헬프 데스크 전자 메일 또는 URL을 사용자 지정하는 것이 중요합니다. 이 옵션을 사용자가 잘 알고 있는 일반적인 헬프 데스크 전자 메일 주소 또는 웹 페이지로 설정합니다. 

자세한 내용은 [셀프 서비스 암호 재설정에 대한 Azure AD 기능 사용자 지정을](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)참조하십시오.

### <a name="password-writeback"></a>암호 쓰기 저장

**암호 쓰기 백** [Azure AD 연결](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) 사용 및 클라우드에서 암호 재설정을 다시 기존 온-프레미스 디렉터리로 실시간으로 기록 합니다. 자세한 내용은 [암호 쓰기 를 참조하세요.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

다음 설정을 권장합니다.

* **온-프레미스 AD에 다시 암호를 쓰기가** **예로**설정되어 있는지 확인합니다. 
* 사용자가 암호를 **예로** **재설정하지 않고 계정 잠금을 해제하도록** 허용합니다.

기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다.

### <a name="administrator-password-setting"></a>관리자 암호 설정

관리자 계정에는 높은 사용 권한이 있습니다. 온-프레미스 엔터프라이즈 또는 도메인 관리자는 SSPR을 통해 암호를 재설정할 수 없습니다. 온-프레미스 관리자 계정에는 다음과 같은 제한 사항이 있습니다.

* 온프레문 환경에서만 암호를 변경할 수 있습니다.
* 비밀 질문과 답변을 암호를 재설정하는 방법으로 사용할 수 없습니다.

온프레미 Active Directory 관리자 계정을 Azure AD와 동기화하지 않는 것이 좋습니다.

### <a name="environments-with-multiple-identity-management-systems"></a>여러 ID 관리 시스템을 갖춘 환경

일부 환경에는 여러 ID 관리 시스템이 있습니다. Oracle AM 및 SiteMinder와 같은 온-프레미스 ID 관리자는 암호에 대한 AD와 동기화해야 합니다. MIM(Microsoft ID 관리자)을 사용하여 PCNS(암호 변경 알림 서비스)와 같은 도구를 사용하여 이 작업을 수행할 수 있습니다. 이 보다 복잡한 시나리오에 대한 정보를 찾으려면 [도메인 컨트롤러에서 MIM 암호 변경 알림 서비스 배포](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)문서를 참조하십시오.

## <a name="plan-testing-and-support"></a>계획 테스트 및 지원

초기 파일럿 그룹에서 조직 전체에 이르는 배포의 각 단계에서 결과가 예상대로 이루어지도록 합니다.

### <a name="plan-testing"></a>계획 테스트

배포가 예상대로 작동하는지 확인하려면 테스트 사례 집합을 계획하여 구현의 유효성을 검사합니다. 테스트 사례를 평가하려면 암호가 있는 관리자가 아닌 테스트 사용자가 필요합니다. 사용자를 만들어야 하는 경우 [Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)를 참조합니다.

다음 표에는 정책에 따라 조직에서 예상결과를 문서화하는 데 사용할 수 있는 유용한 테스트 시나리오가 포함되어 있습니다.
<br>


| 비즈니스 사례| 예상 결과 |
| - | - |
| SSPR 포털은 회사 네트워크 내에서 액세스할 수 있습니다.| 조직에 의해 결정됩니다. |
| SSPR 포털은 회사 네트워크 외부에서 액세스할 수 있습니다.| 조직에 의해 결정됩니다. |
| 사용자가 암호 재설정을 사용하도록 설정하지 않은 경우 브라우저에서 사용자 암호 재설정| 사용자가 암호 재설정 흐름에 액세스할 수 없습니다. |
| 사용자가 암호 재설정에 등록하지 않은 경우 브라우저에서 사용자 암호 재설정| 사용자가 암호 재설정 흐름에 액세스할 수 없습니다. |
| 암호 재설정 등록을 적용하면 사용자가 로그인| 사용자에게 보안 정보를 등록하라는 메시지를 표시합니다. |
| 암호 재설정 등록이 완료되면 사용자가 로그인| 사용자에게 보안 정보를 등록하라는 메시지를 표시합니다. |
| SSPR 포털은 사용자가 라이센스가 없는 경우 액세스할 수 있습니다.| 액세스 가능 |
| Windows 10 Azure AD 조인 또는 하이브리드 Azure AD 조인 장치 잠금 화면에서 사용자 암호 재설정| 사용자는 암호를 재설정할 수 있습니다. |
| 관리자가 SSPR 등록 및 사용 데이터를 거의 실시간으로 사용할 수 있습니다.| 감사 로그를 통해 사용할 수 있습니다. |

[Azure AD 셀프 서비스 암호 재설정 파일럿 롤 완료를](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)참조할 수도 있습니다. 이 자습서에서는 조직에서 SSPR을 파일럿으로 롤아웃하고 관리자가 아닌 계정을 사용하여 테스트합니다.

### <a name="plan-support"></a>계획 지원

SSPR은 일반적으로 사용자 문제를 일으키지는 않지만 발생할 수 있는 문제를 처리할 수 있도록 지원 담당자를 준비시키는 것이 중요합니다. 관리자는 Azure AD 포털을 통해 최종 사용자의 암호를 재설정할 수 있지만 셀프 서비스 지원 프로세스를 통해 문제를 해결하는 것이 좋습니다.

지원 팀의 성공을 위해 사용자로부터 받은 질문에 따라 FAQ를 만들 수 있습니다. 다음은 몇 가지 예입니다.

| 시나리오| 설명 |
| - | - |
| 사용자에게 사용 가능한 등록된 인증 방법이 없습니다.| 사용자가 암호를 재설정하려고 하지만 사용 가능한 등록 인증 방법이 없습니다(예: 휴대폰을 집에 두고 전자 메일에 액세스할 수 없음). |
| 사용자가 사무실이나 휴대폰에서 문자 또는 전화를 받지 않습니다.| 사용자가 문자 또는 전화를 통해 신원을 확인하려고 하지만 문자/전화를 받지 않습니다. |
| 사용자가 암호 재설정 포털에 액세스할 수 없습니다.| 사용자가 암호를 재설정하려고 하지만 암호 재설정이 활성화되지 않았으며 페이지에 액세스하여 암호를 업데이트할 수 없습니다. |
| 사용자가 새 암호를 설정할 수 없습니다.| 사용자가 암호 재설정 흐름 중에 확인을 완료하지만 새 암호를 설정할 수 없습니다. |
| 사용자가 Windows 10 장치에서 암호 재설정 링크가 표시되지 않습니다.| 사용자가 Windows 10 잠금 화면에서 암호를 재설정하려고 하지만 장치가 Azure AD에 가입되지 않았거나 Intune 장치 정책이 활성화되어 있지 않습니다. |

### <a name="plan-rollback"></a>롤백 계획

배포를 롤백하려면 다음을 수행합니다.

* 단일 사용자의 경우 보안 그룹에서 사용자를 제거합니다. 

* 그룹의 경우 SSPR 구성에서 그룹을 제거합니다.

* 모든 사용자의 경우 Azure AD 테넌트에 대해 SSPR을 사용하지 않도록 설정합니다.

## <a name="deploy-sspr"></a>SSPR 배포

배포하기 전에 다음을 수행했는지 확인하십시오.

1. [통신 계획을](#plan-communications)만들고 실행하기 시작했습니다.

1. 적절한 [구성 설정을](#plan-configuration)결정했습니다.

1. [파일럿](#plan-a-pilot) 및 프로덕션 환경에 대한 사용자 및 그룹을 식별했습니다.

1. 등록 및 셀프 서비스에 대한 [구성 설정이 결정되었습니다.](#plan-configuration)

1. 하이브리드 환경이 있는 경우 [구성된 암호 쓰기 제거입니다.](#password-writeback)


**이제 SSPR을 배포할 준비가 되었습니다!**

다음 영역 구성에 대한 전체 단계별 지침은 [셀프 서비스 암호 재설정 을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) 참조하십시오.

1. [인증 방법](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [등록 설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [알림 설정](#notifications-settings)

1. [사용자 지정 설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [온-프레미스 통합](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Windows에서 SSPR 사용
Windows 7, 8, 8.1 및 10을 실행하는 컴퓨터의 경우 [사용자가 Windows 로그인 화면에서 암호를 재설정하도록 설정할](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows) 수 있습니다.

## <a name="manage-sspr"></a>SSPR 관리

Azure AD는 감사 및 보고서를 통해 SSPR 성능에 대한 추가 정보를 제공할 수 있습니다.

### <a name="password-management-activity-reports"></a>암호 관리 활동 보고서 

Azure 포털에서 미리 빌드된 보고서를 사용하여 SSPR 성능을 측정할 수 있습니다. 적절히 라이선스를 받은 경우 사용자 지정 쿼리를 만들 수도 있습니다. 자세한 내용은 [Azure AD 암호 관리에 대한 보고 옵션을](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) 참조하십시오.

> [!NOTE]
>  [글로벌 관리자여야](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)하며 조직에 대해 이 데이터를 수집하려면 옵트인해야 합니다. 옵트인하려면 보고 탭 또는 Azure Portal의 감사 로그를 한 번 이상 방문해야 합니다. 그때까지는 조직에 대한 데이터가 수집되지 않습니다.

등록 및 암호 재설정에 대한 감사 로그를 30일 동안 사용할 수 있습니다. 회사 내의 보안 감사에 더 긴 보존이 필요한 경우 로그를 Azure [Sentinel,](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)Splunk 또는 ArcSight와 같은 SIEM 도구로 내보내고 사용해야 합니다.

![SSPR 보고 스크린샷](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>인증 방법 - 사용 및 인사이트

[사용 및 통찰력을](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) 통해 Azure MFA 및 SSPR과 같은 기능에 대한 인증 방법이 조직에서 어떻게 작동하는지 이해할 수 있습니다. 이 보고 기능은 조직에 등록하는 방법과 이를 사용하는 방법을 이해하는 수단을 제공합니다.

### <a name="troubleshoot"></a>문제 해결

* 셀프 [서비스 암호 재설정 문제 해결](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 참조 

* [자주 묻는 암호 관리 팔로우](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>유용한 문서

* [인증 방법이란?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [작동 방식: Azure AD 셀프 서비스 암호 재설정?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Azure AD의 셀프 서비스 암호 재설정 기능 사용자 지정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Azure Active Directory에서 암호 정책 및 제한](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) 문서를 검토하세요.

* [비밀번호 쓰기 저장이란?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>다음 단계

* SSPR 배포를 시작하려면 [Azure AD 셀프 서비스 암호 재설정 을](tutorial-enable-sspr.md) 참조하세요.

* [Azure AD 암호 보호 구현 고려](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Azure AD 스마트 잠금 구현 고려](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)