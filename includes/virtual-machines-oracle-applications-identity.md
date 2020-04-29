---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "68361558"
---
### <a name="identity-tier"></a>Id 계층 

Microsoft Oracle 파트너 관계를 통해 Azure, OCI 및 Oracle 응용 프로그램에서 통합 id를 설정할 수 있습니다. JD Edwards EnterpriseOne 또는 PeopleSoft 응용 프로그램 제품군의 경우 Azure AD와 Oracle IDCS 사이에 Single Sign-On을 설정 하는 데 Oracle HTTP 서버 (OHS) 인스턴스가 필요 합니다.

OHS는 응용 프로그램 계층에 대 한 역방향 프록시 역할을 합니다. 즉, 최종 응용 프로그램에 대 한 모든 요청이이를 통과 합니다. Oracle Access Manager WebGate는 최종 응용 프로그램으로 이동 하는 모든 요청을 가로채는 OHS 웹 서버 플러그 인입니다. 액세스 되는 리소스가 보호 되는 경우 (인증 된 세션 필요) WebGate는 사용자의 브라우저를 통해 Id 클라우드 서비스를 사용 하 여 OIDC 인증 흐름을 시작 합니다. Openid connect Connect 웹 게이트에서 지원 되는 흐름에 대 한 자세한 내용은 [Oracle Access Manager 설명서](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)를 참조 하세요.

이 설정을 사용 하는 경우 Azure AD에 이미 로그인 한 사용자는 Oracle Id 클라우드 서비스를 통해 다시 로그인 하지 않고도 JD Edwards EnterpriseOne 또는 PeopleSoft 응용 프로그램으로 이동할 수 있습니다. 이 솔루션을 배포 하는 고객은 단일 자격 증명 집합, 향상 된 로그온 환경, 향상 된 보안 및 지원 센터 비용 절감을 포함 하 여 Single Sign-On의 이점을 얻을 수 있습니다.

Azure AD를 사용 하 여 JD Edwards EnterpriseOne 또는 PeopleSoft에 대 한 Single Sign-On를 설정 하는 방법에 대 한 자세한 내용은 관련 [Oracle 백서](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)를 참조 하세요.