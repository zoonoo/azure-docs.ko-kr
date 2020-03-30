---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361558"
---
### <a name="identity-tier"></a>아이덴티티 티어 

Microsoft-Oracle 파트너십을 사용하면 Azure, OCI 및 Oracle 응용 프로그램 전반에 걸쳐 통합 ID를 설정할 수 있습니다. JD 에드워즈 엔터프라이즈원 또는 피플소프트 애플리케이션 제품군의 경우 Azure AD와 Oracle IDCS 간에 단일 사인온을 설정하려면 오라클 HTTP 서버(OHS)의 인스턴스가 필요합니다.

OHS는 응용 프로그램 계층에 대한 역방향 프록시 역할을 하며, 이는 최종 응용 프로그램에 대한 모든 요청이 이를 통해 전달된다는 것을 의미합니다. Oracle Access Manager WebGate는 최종 응용 프로그램으로 가는 모든 요청을 가로채는 OHS 웹 서버 플러그인입니다. 액세스 중인 리소스가 보호되는 경우(인증된 세션이 필요함) WebGate는 사용자의 브라우저를 통해 ID 클라우드 서비스를 통해 OIDC 인증 흐름을 시작합니다. OpenID Connect WebGate에서 지원하는 흐름에 대한 자세한 내용은 [Oracle 액세스 관리자 설명서를](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)참조하십시오.

이 설정을 사용하면 Azure AD에 이미 로그인한 사용자는 Oracle ID 클라우드 서비스를 통해 다시 로그인하지 않고 JD Edwards EnterpriseOne 또는 PeopleSoft 응용 프로그램으로 이동할 수 있습니다. 이 솔루션을 배포하는 고객은 단일 자격 증명 집합, 향상된 사인온 환경, 향상된 보안 및 헬프 데스크 비용 절감 을 포함하여 단일 사인온의 이점을 누릴 수 있습니다.

Azure AD를 사용하여 JD Edwards EnterpriseOne 또는 PeopleSoft에 대한 단일 사인온 설정에 대한 자세한 내용은 연결된 [Oracle 백서를](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)참조하십시오.