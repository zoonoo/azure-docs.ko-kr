---
title: '애플리케이션 관리: 모범 사례 및 권장 사항 | 마이크로 소프트 문서'
description: Azure Active Directory에서 응용 프로그램 관리에 대한 모범 사례 및 권장 사항을 알아봅니다. 응용 프로그램 프록시를 사용하여 온-프레미스 앱 자동 프로비저닝 및 게시에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085292"
---
# <a name="application-management-best-practices"></a>애플리케이션 관리 모범 사례
이 문서에는 Azure Active Directory(Azure AD)의 응용 프로그램 관리, 자동 프로비저닝 사용 및 응용 프로그램 프록시를 사용하여 온-프레미스 앱을 게시하기 위한 권장 사항 및 모범 사례가 포함되어 있습니다.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>클라우드 앱 및 단일 사인온 권장 사항
| 권장 | 주석 |
| --- | --- |
| 앱에 대한 Azure AD 응용 프로그램 갤러리를 확인합니다.  | Azure AD에는 엔터프라이즈 단일 사인온(SSO)으로 사용할 수 있는 수천 개의 사전 통합된 응용 프로그램이 포함된 갤러리가 있습니다. 앱별 설정 지침은 [SaaS 앱 자습서 목록을](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)참조하십시오.  | 
| 페더레이션된 SAML 기반 SSO 사용  | 응용 프로그램이 이를 지원하는 경우 암호 기반 SSO 및 ADFS 대신 Azure AD와 함께 페더레이션된 SAML 기반 SSO를 사용합니다.  | 
| 인증서 서명에 SHA-256 사용  | Azure AD는 기본적으로 SHA-256 알고리즘을 사용하여 SAML 응답에 서명합니다. 응용 프로그램에 SHA-1이 필요하지 않는 한 SHA-256을 사용하십시오(인증서 [서명 옵션](certificate-signing-options.md) 및 응용 프로그램 [로그인 문제](application-sign-in-problem-application-error.md)참조).  | 
| 사용자 할당 필요  | 기본적으로 사용자는 할당되지 않고 엔터프라이즈 응용 프로그램에 액세스할 수 있습니다. 그러나 응용 프로그램이 역할을 노출하거나 응용 프로그램이 사용자의 액세스 패널에 표시되도록 하려면 사용자 할당이 필요합니다. (응용 [프로그램 통합에 대한 개발자 지침](developer-guidance-for-integrating-applications.md)참조))  | 
| 사용자에게 내 앱 액세스 패널 배포 | [의](end-user-experiences.md) `https://myapps.microsoft.com` 액세스 패널은 사용자에게 할당된 클라우드 기반 응용 프로그램에 대한 단일 진입점을 제공하는 웹 기반 포털입니다. 그룹 관리 및 셀프 서비스 암호 재설정과 같은 추가 기능이 추가되면 사용자는 액세스 패널에서 찾을 수 있습니다. [액세스 패널 배포 계획을](access-panel-deployment-plan.md)참조하십시오.
| 그룹 할당 사용  | 구독에 포함된 경우 지속적인 액세스 관리를 그룹 소유자에게 위임할 수 있도록 응용 프로그램에 그룹을 할당합니다. (응용 [프로그램 통합에 대한 개발자 지침](developer-guidance-for-integrating-applications.md)참조))   | 
| 인증서 관리 프로세스 설정 | 서명 인증서의 최대 수명은 3년입니다. 인증서 만료로 인한 중단을 방지하거나 최소화하려면 역할 및 전자 메일 그룹을 사용하여 인증서 관련 변경 알림을 면밀히 모니터링할 수 있습니다. |

## <a name="provisioning-recommendations"></a>프로비저닝 권장 사항
| 권장 | 주석 |
| --- | --- |
| 자습서를 사용하여 클라우드 앱으로 프로비저닝 설정 | 추가하려는 갤러리 앱에 대한 프로비저닝 구성에 대한 단계별 지침은 [SaaS 앱 자습서 목록을](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) 확인하십시오. |
| 프로비저닝 로그(미리 보기)를 사용하여 상태 모니터링 | [프로비저닝 로그는](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 개별 사용자의 상태를 포함하여 프로비저닝 서비스에서 수행하는 모든 작업에 대한 세부 정보를 제공합니다. |
| 프로비저닝 알림 전자 메일에 메일 그룹 할당 | 프로비저닝 서비스에서 보내는 중요 경고의 가시성을 높이려면 알림 전자 메일 설정에 메일 그룹을 할당합니다. |


## <a name="application-proxy-recommendations"></a>응용 프로그램 프록시 권장 사항
| 권장 | 주석 |
| --- | --- |
| 내부 리소스에 대한 원격 액세스를 위해 응용 프로그램 프록시 사용 | 응용 프로그램 프록시는 원격 사용자에게 내부 리소스에 대한 액세스 권한을 부여하고 VPN 또는 역방향 프록시의 필요성을 대체하는 데 권장됩니다. 대기 시간이 추가될 수 있으므로 회사 네트워크 내에서 리소스에 액세스하기 위한 것이 아닙니다.
| 사용자 지정 도메인 사용 | 사용자 및 응용 프로그램 간의 URL이 네트워크 내부 또는 외부에서 작동하도록 응용 프로그램에 대한 사용자 지정 도메인 을 설정합니다(사용자 [지정 도메인 구성](application-proxy-configure-custom-domain.md)참조). 또한 브랜딩을 제어하고 URL을 맞춤설정할 수도 있습니다.  사용자 지정 도메인 이름을 사용하는 경우 Microsoft에서 신뢰할 수 있는 인증서가 아닌 기관에서 공용 인증서를 획득하도록 계획합니다. Azure 응용 프로그램 프록시는 표준([와일드카드)](application-proxy-wildcard.md)또는 SAN 기반 인증서를 지원합니다. (응용 [프로그램 프록시 계획](application-proxy-deployment-plan.md)참조) |
| 응용 프로그램 프록시를 배포하기 전에 사용자 동기화 | 응용 프로그램 프록시를 배포하기 전에 온-프레미스 디렉터리에서 사용자 ID를 동기화하거나 Azure AD에서 직접 만듭니다. ID 동기화를 사용하면 Azure AD를 사용하여 사용자에게 앱 프록시 게시 응용 프로그램에 대한 액세스 권한을 부여하기 전에 사용자를 미리 인증할 수 있습니다. 또한 단일 사인온(SSO)을 수행하는 데 필요한 사용자 식별자 정보를 제공합니다. (응용 [프로그램 프록시 계획](application-proxy-deployment-plan.md)참조) |
| 고가용성 및 부하 균형 조정을 위한 팁 팔로우 | 사용자, 응용 프로그램 프록시 커넥터 및 백 엔드 앱 서버 간에 트래픽이 흐르는 방법을 알아보고 성능 및 부하 분산을 최적화하기 위한 팁을 얻으려면 [응용 프로그램 프록시 커넥터 및 응용 프로그램의 고가용성 및 부하 분산을](application-proxy-high-availability-load-balancing.md)참조하십시오. |
| 여러 커넥터 사용 | 둘 이상의 응용 프로그램 프록시 커넥터를 사용하여 복원력, 가용성 및 확장성을 높일 수 [있습니다(응용 프로그램 프록시 커넥터](application-proxy-connectors.md)참조). 커넥터 그룹을 만들고 각 커넥터 그룹에 두 개 이상의 커넥터가 있는지 확인합니다(세 개의 커넥터가 최적). |
| 커넥터 서버를 응용 프로그램 서버 가까이에 위치하고 커넥터 서버가 동일한 도메인에 있는지 확인합니다. | 성능을 최적화하려면 커넥터 서버를 응용 프로그램 서버 가까이에 물리적으로 찾습니다(네트워크 [토폴로지 고려 사항](application-proxy-network-topology.md)참조). 또한 커넥터 서버와 웹 응용 프로그램 서버는 동일한 Active Directory 도메인에 속해야 하거나 신뢰 하는 도메인에 걸쳐 있어야 합니다. 이 구성은 통합 Windows 인증(IWA) 및 Kerberos 제한 위임(KCD)이 있는 SSO에 필요합니다. 서버가 다른 도메인에 있는 경우 SSO에 리소스 기반 위임을 사용해야 [합니다(응용 프로그램 프록시를 사용한 단일 사인온의 경우 KCD](application-proxy-configure-single-sign-on-with-kcd.md)참조). |
| 커넥터에 대한 자동 업데이트 사용 | 최신 기능 및 버그 수정을 위해 커넥터에 대한 자동 업데이트를 사용하도록 설정합니다. Microsoft는 이전에 최신 커넥터 버전과 한 버전에 대한 직접 지원을 제공합니다. (응용 [프로그램 프록시 릴리스 버전 기록](application-proxy-release-version-history.md)참조)) |
| 온-프레미스 프록시 우회 | 보다 쉽게 유지 관리를 위해 Azure 서비스에 직접 연결되도록 온-프레미스 프록시를 우회하도록 커넥터를 구성합니다. (응용 [프로그램 프록시 커넥터 및 프록시 서버 를](application-proxy-configure-connectors-with-proxy-servers.md)참조하십시오.) |
| 웹 응용 프로그램 프록시를 통해 Azure AD 응용 프로그램 프록시 사용 | 대부분의 온-프레미스 시나리오에 Azure AD 응용 프로그램 프록시를 사용합니다. 웹 응용 프로그램 프록시는 AD FS용 프록시 서버가 필요하고 Azure Active Directory에서 사용자 지정 도메인을 사용할 수 없는 시나리오에서만 선호됩니다. (응용 [프로그램 프록시 마이그레이션을](application-proxy-migration.md)참조하십시오.) |
