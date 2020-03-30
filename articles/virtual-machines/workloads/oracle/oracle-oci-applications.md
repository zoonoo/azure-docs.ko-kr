---
title: Azure 가상 머신에 Oracle 앱을 배포하는 아키텍처 | 마이크로 소프트 문서
description: 애플리케이션 아키텍처는 E-비즈니스 스위트, JD 에드워즈 엔터프라이즈원, 피플소프트를 포함한 오라클 앱을 Azure 또는 OCI(Oracle Cloud Infrastructure)에 데이터베이스가 있는 Microsoft Azure 가상 머신에 배포합니다.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: b183a4d4922c89f60ccb19b3e3e978216f33cc9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70100082"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Azure에 Oracle 응용 프로그램을 배포하는 아키텍처

마이크로소프트와 오라클은 고객이 오라클 E-비즈니스 스위트, JD 에드워즈 엔터프라이즈원, 피플소프트 와 같은 오라클 애플리케이션을 클라우드에 배포할 수 있도록 협력해 온 다. Microsoft Azure와 OCI(Oracle Cloud Infrastructure) 간의 미리 보기 [개인 네트워크 상호 연결이](configure-azure-oci-networking.md) 도입됨으로 이제 Oracle 응용 프로그램을 Azure 또는 OCI의 백 엔드 데이터베이스와 함께 Azure에 배포할 수 있습니다. Oracle 응용 프로그램을 Azure Active Directory와 통합할 수 있으므로 사용자가 Azure Active Directory(Azure AD) 자격 증명을 사용하여 Oracle 응용 프로그램에 로그인할 수 있도록 단일 사인온을 설정할 수 있습니다.

OCI는 DBaaS, 엑사데이타 클라우드 서비스, 오라클 RAC 및 서비스로서의 인프라(IaaS)를 포함한 오라클 애플리케이션을 위한 다양한 오라클 데이터베이스 옵션을 제공합니다. 현재 자율 데이터베이스는 오라클 애플리케이션에 지원되는 백 엔드가 아닙니다.

가용성이 높고 안전한 방식을 포함하여 Azure에서 Oracle 응용 프로그램을 배포하는 [데는 여러 가지 옵션이](oracle-overview.md) 있습니다. 또한 Azure는 [Azure에서 오라클](oracle-vm-solutions.md) 응용 프로그램을 전적으로 실행하도록 선택한 경우 배포할 수 있는 Oracle 데이터베이스 VM 이미지도 제공합니다.

다음 섹션에서는 Microsoft와 Oracle이 오라클 E-비즈니스 제품군, JD Edwards EnterpriseOne 및 피플소프트를 교차 클라우드 구성 또는 전적으로 Azure에 배포하기 위한 아키텍처 권장 사항을 간략하게 설명합니다. Microsoft와 Oracle은 이러한 응용 프로그램을 테스트하고 성능이 이러한 응용 프로그램에 대해 Oracle에서 설정한 표준을 충족하는지 확인했습니다.

## <a name="architecture-considerations"></a>아키텍처 고려 사항

Oracle 응용 프로그램은 Azure의 동일하거나 여러 가상 컴퓨터에서, 그리고 선택적으로 OCI에서 호스팅할 수 있는 여러 서비스로 구성됩니다. 

응용 프로그램 인스턴스는 개인 또는 공용 끝점으로 설정할 수 있습니다. Microsoft와 Oracle은 응용 프로그램 관리를 위해 별도의 서브넷에 공용 IP 주소가 있는 *대역 호스트 VM을* 설정하는 것이 좋습니다. 그런 다음 데이터베이스 계층을 포함한 다른 컴퓨터에 개인 IP 주소만 할당합니다. 

클라우드 간 아키텍처에서 응용 프로그램을 설정할 때 Azure 가상 네트워크의 IP 주소 공간이 OCI 가상 클라우드 네트워크의 개인 IP 주소 공간과 겹치지 않도록 계획해야 합니다.

보안을 강화하려면 서브넷 수준에서 네트워크 보안 그룹을 설정하여 특정 포트 및 IP 주소의 트래픽만 허용되도록 합니다. 예를 들어 중간 계층의 컴퓨터는 가상 네트워크 내에서만 트래픽을 수신해야 합니다. 외부 트래픽이 중간 계층 시스템에 직접 도달해서는 안 됩니다.

고가용성의 경우 동일한 가용성 집합 또는 다른 가용성 영역에서 다른 서버의 중복 인스턴스를 설정할 수 있습니다. 가용성 영역을 사용하면 99.99%의 가동 시간 SLA를 달성할 수 있으며 가용성 집합을 사용하면 리전에서 99.95%의 가동 시간 SLA를 달성할 수 있습니다. 이 문서에 표시된 샘플 아키텍처는 두 가용성 영역에 배포됩니다.

교차 클라우드 상호 연결을 사용하여 응용 프로그램을 배포할 때 기존 ExpressRoute 회로를 계속 사용하여 Azure 환경을 온-프레미스 네트워크에 연결할 수 있습니다. 그러나 OCI에 상호 연결하려면 온-프레미스 네트워크에 연결하는 회로와 는 별도의 ExpressRoute 회로가 필요합니다.

## <a name="e-business-suite"></a>E-비즈니스 스위트

오라클 E-비즈니스 스위트(EBS)는 공급망 관리(SCM) 및 CRM(고객 관계 관리)을 포함한 애플리케이션 제품군입니다. OCI의 관리되는 데이터베이스 포트폴리오를 활용하기 위해 Microsoft Azure와 OCI 간의 교차 클라우드 상호 연결을 사용하여 EBS를 배포할 수 있습니다. 이 구성에서 프레젠테이션 및 응용 프로그램 계층은 다음 아키텍처 다이어그램에 나와 있는 것처럼 Azure 및 OCI의 데이터베이스 계층에서 실행됩니다(그림 1).

![E-비즈니스 스위트 크로스 클라우드 아키텍처](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*그림 1: E-비즈니스 스위트 크로스 클라우드 아키텍처* 

이 아키텍처에서 Azure의 가상 네트워크는 교차 클라우드 상호 연결을 사용하여 OCI의 가상 클라우드 네트워크에 연결됩니다. 응용 프로그램 계층은 Azure에서 설정되지만 데이터베이스는 OCI에서 설정됩니다. 특정 포트의 특정 서브넷에서만 트래픽을 허용하도록 네트워크 보안 그룹이 있는 자체 서브넷에 각 구성 요소를 배포하는 것이 좋습니다.

또한 이 아키텍처는 한 지역의 두 가용성 영역에서 Oracle Data Guard를 사용하여 구성된 가용성이 높은 Oracle 데이터베이스를 사용하여 Azure에서 전적으로 배포할 수 있습니다. 다음 다이어그램(그림 2)은 이 아키텍처 패턴의 예입니다.

![E-비즈니스 스위트 Azure 전용 아키텍처](media/oracle-oci-applications/ebs-arch-azure.png)

*그림 2: E-비즈니스 스위트 Azure 전용 아키텍처*

다음 섹션에서는 상위 수준에서 다른 구성 요소를 설명합니다.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>응용 프로그램(중간) 계층

응용 프로그램 계층은 자체 서브넷에서 격리됩니다. 내결함성과 쉬운 패치 관리를 위해 여러 개의 가상 시스템이 설정되어 있습니다. 이러한 VM은 Azure NetApp 파일 및 울트라 SSD에서 제공하는 공유 저장소에 의해 지원될 수 있습니다. 이 구성을 사용하면 가동 중지 시간 없이 패치를 보다 쉽게 배포할 수 있습니다. 응용 프로그램 계층의 컴퓨터는 공용 로드 밸런서에 의해 정면으로 이동하여 계층의 한 컴퓨터가 오류로 인해 오프라인 상태가 되어도 EBS 응용 프로그램 계층에 대한 요청이 처리되도록 해야 합니다.

### <a name="load-balancer"></a>부하 분산 장치

Azure 로드 밸런서를 사용하면 워크로드의 여러 인스턴스에 트래픽을 분산하여 고가용성을 보장할 수 있습니다. 이 경우 공용 로드 밸런서가 설정되는데, 이는 사용자가 웹을 통해 EBS 응용 프로그램에 액세스할 수 있기 때문입니다. 로드 밸러블러는 중간 계층의 두 컴퓨터에 부하를 분배합니다. 보안을 강화하려면 사이트 간 VPN 또는 ExpressRoute 및 네트워크 보안 그룹을 사용하여 회사 네트워크에서 시스템에 액세스하는 사용자만 트래픽을 허용합니다.

### <a name="database-tier"></a>데이터베이스 계층

이 계층은 Oracle 데이터베이스를 호스팅하고 자체 서브넷으로 분리됩니다. 응용 프로그램 계층에서 Oracle 특정 데이터베이스 포트 1521의 데이터베이스 계층에만 트래픽을 허용하는 네트워크 보안 그룹을 추가하는 것이 좋습니다.

Microsoft와 오라클은 고가용성 설정을 권장합니다. Azure의 고가용성은 Oracle Data Guard를 사용하여 두 개의 가용성 영역에 두 개의 Oracle 데이터베이스를 설정하거나 OCI에서 Oracle Database Exadata 클라우드 서비스를 사용하여 달성할 수 있습니다. Oracle Database Exadata 클라우드 서비스를 사용하는 경우 데이터베이스가 두 개의 서브넷에 배포됩니다. 또한 오라클 데이터 가드를 사용하여 두 개의 가용성 도메인으로 OCI의 VM에 Oracle Database를 설정할 수도 있습니다.


### <a name="identity-tier"></a>아이덴티티 티어

ID 계층에는 EBS 어설서터 VM이 포함되어 있습니다. EBS 어설션을 사용하면 오라클 ID 클라우드 서비스(IDCS) 및 Azure AD에서 ID를 동기화할 수 있습니다. EBS는 SAML 2.0 또는 OpenID 연결과 같은 단일 사인온 프로토콜을 지원하지 않으므로 EBS 어설스터가 필요합니다. EBS 어설스터는 OpenID 연결 토큰(IDCS에서 생성됨)을 사용하고 유효성을 검사한 다음 EBS에서 사용자에 대한 세션을 만듭니다. 

이 아키텍처는 IDCS 통합을 보여 주지만 오라클 인터넷 디렉터리 또는 Oracle 통합 디렉터리기능을 사용하는 Oracle Access Manager를 통해 Azure AD 통합 액세스 및 단일 사인온을 사용할 수도 있습니다. 자세한 내용은 [IDCS 통합을 통해 오라클 EBS 배포](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) 또는 OAM 통합을 [통해 오라클 EBS 배포에](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)대한 백서를 참조하십시오.

고가용성을 위해 EBS Asserter의 중복 서버를 로드 밸러커 앞에 두고 여러 가용성 영역에 배포하는 것이 좋습니다.

인프라가 설정되면 오라클에서 제공하는 설치 가이드에 따라 E-비즈니스 스위트를 설치할 수 있습니다.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

오라클의 JD 에드워즈 엔터프라이즈원은 포괄적인 엔터프라이즈 리소스 계획 소프트웨어의 통합 애플리케이션 제품군입니다. Oracle 또는 SQL Server 데이터베이스 백 엔드로 설정할 수 있는 다중 계층 응용 프로그램입니다. 이 섹션에서는 OCI 또는 Azure에서 Oracle 데이터베이스 백 엔드를 사용하여 JD Edwards EnterpriseOne을 배포하는 방법에 대해 자세히 설명합니다.

다음 권장 아키텍처(그림 3)에서는 관리, 프레젠테이션 및 중간 계층이 Azure의 가상 네트워크에 배포됩니다. 데이터베이스는 OCI의 가상 클라우드 네트워크에 배포됩니다.

E-Business Suite와 마찬가지로 안전한 관리를 위해 선택적 요새 계층을 설정할 수 있습니다. 배스온 VM 호스트를 점프 서버로 사용하여 응용 프로그램 및 데이터베이스 인스턴스에 액세스합니다.

![JD 에드워즈 엔터프라이즈원 크로스 클라우드 아키텍처](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*그림 3: JD 에드워즈 엔터프라이즈원 크로스 클라우드 아키텍처*

이 아키텍처에서 Azure의 가상 네트워크는 교차 클라우드 상호 연결을 사용하여 OCI의 가상 클라우드 네트워크에 연결됩니다. 응용 프로그램 계층은 Azure에서 설정되지만 데이터베이스는 OCI에서 설정됩니다. 특정 포트의 특정 서브넷에서만 트래픽을 허용하도록 네트워크 보안 그룹이 있는 자체 서브넷에 각 구성 요소를 배포하는 것이 좋습니다.

또한 이 아키텍처는 한 지역의 두 가용성 영역에서 Oracle Data Guard를 사용하여 구성된 가용성이 높은 Oracle 데이터베이스를 사용하여 Azure에서 전적으로 배포할 수 있습니다. 다음 다이어그램(그림 4)은 이 아키텍처 패턴의 예입니다.

![JD 에드워즈 엔터프라이즈원 Azure 전용 아키텍처](media/oracle-oci-applications/jdedwards-arch-azure.png)

*그림 4: JD 에드워즈 엔터프라이즈원 Azure 전용 아키텍처*

다음 섹션에서는 상위 수준에서 다른 구성 요소를 설명합니다.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>관리 계층

이름에서 알 수 있듯이 이 계층은 관리 작업에 사용됩니다. 관리 계층에 대해 별도의 서브넷을 개척할 수 있습니다. 이 계층의 서비스와 서버는 주로 응용 프로그램의 설치 및 관리에 사용됩니다. 따라서 이러한 서버의 단일 인스턴스로 충분합니다. 응용 프로그램의 고가용성에는 중복 인스턴스가 필요하지 않습니다.

이 계층의 구성 요소는 다음과 같습니다.
    
 - **프로비저닝 서버** - 이 서버는 응용 프로그램의 다른 구성 요소의 종단 간 배포에 사용됩니다. 포트 22를 통해 데이터베이스 계층의 인스턴스를 포함하여 다른 계층의 인스턴스와 통신합니다. JD 에드워즈 엔터프라이즈원의 서버 관리자 콘솔을 호스팅합니다.
 - **배포 서버** - 이 서버는 주로 JD Edwards EnterpriseOne의 설치에 필요합니다. 설치 프로세스 중에 이 서버는 필요한 파일 및 설치 패키지의 중앙 리포지토리 역할을 합니다. 소프트웨어가 이 서버의 다른 서버 및 클라이언트에 배포또는 배포됩니다.
 - **개발 클라이언트** - 이 서버에는 네이티브 응용 프로그램뿐만 아니라 웹 브라우저에서 실행되는 구성 요소가 포함되어 있습니다.

### <a name="presentation-tier"></a>프레젠테이션 계층

이 계층에는 응용 프로그램 인터페이스 서비스(AIS), 응용 프로그램 개발 프레임워크(ADF) 및 JAVA 응용 프로그램 서버(JAS)와 같은 다양한 구성 요소가 포함되어 있습니다. 이 계층의 서버는 중간 계층의 서버와 통신합니다. 트래픽이 수신되는 포트 번호 및 URL을 기반으로 필요한 서버로 트래픽을 라우팅하는 로드 밸런서가 앞에 표시됩니다. 고가용성을 위해 각 서버 유형의 여러 인스턴스를 배포하는 것이 좋습니다.

다음은 이 계층의 구성 요소입니다.
    
- **응용 프로그램 인터페이스 서비스 (AIS)** - AIS 서버는 JD 에드워즈 엔터프라이즈원 모바일 엔터프라이즈 응용 프로그램과 JD 에드워즈 엔터프라이즈 원 사이의 통신 인터페이스를 제공합니다.
- **JAVA 응용 프로그램 서버(JAS)** - JAS는 로드 밸러버로부터 요청을 수신하고 중간 계층으로 전달하여 복잡한 작업을 실행합니다. JAS는 간단한 비즈니스 논리를 실행할 수 있습니다.
- **BI 게시자 서버(BIP)** - 이 서버는 JD Edwards EnterpriseOne 응용 프로그램에서 수집한 데이터를 기반으로 보고서를 제공합니다. 보고서에서 서로 다른 템플릿을 기반으로 데이터를 제공하는 방법을 디자인하고 제어할 수 있습니다.
- **비즈니스 서비스 서버(BSS)** - BSS를 통해 다른 오라클 애플리케이션과의 정보 교환 및 상호 운용성을 지원합니다.
- **실시간 이벤트 서버 (RTE)** - RTE 서버는 JDE EnterpriseOne 시스템에서 발생하는 트랜잭션에 대한 외부 시스템에 알림을 설정할 수 있습니다. 구독자 모델을 사용 하며 타사 시스템에서 이벤트를 구독할 수 있습니다. 두 RTE 서버에 대한 분산 요청을 로드하려면 서버가 클러스터에 있는지 확인합니다.
- **응용 프로그램 개발 프레임 워크 (ADF) 서버** - ADF 서버는 오라클 ADF와 함께 개발 된 JD 에드워즈 엔터프라이즈원 응용 프로그램을 실행하는 데 사용됩니다. 이 배포는 ADF 런타임이 있는 Oracle WebLogic 서버에 배포됩니다.

### <a name="middle-tier"></a>중간 계층

중간 계층에는 논리 서버와 일괄 처리 서버가 포함됩니다. 이 경우 두 서버가 동일한 가상 시스템에 설치됩니다. 그러나 프로덕션 시나리오의 경우 논리 서버와 일괄 처리 서버를 별도의 서버에 배포하는 것이 좋습니다. 가용성을 높이기 위해 두 개의 가용성 영역에 걸쳐 중간 계층에 여러 서버가 배포됩니다. Azure 로드 밸런서를 만들어야 하며 이러한 서버를 백 엔드 풀에 배치하여 두 서버가 모두 활성 상태이고 요청을 처리해야 합니다.

중간 계층의 서버는 프레젠테이션 계층의 서버와 공용 로드 밸런서에서만 요청을 받습니다. 네트워크 보안 그룹 규칙은 프레젠테이션 계층 서브넷 및 로드 밸러버 이외의 주소에서 트래픽을 거부하도록 설정해야 합니다. NSG 규칙을 설정하여 관리 목적으로 요새 호스트에서 포트 22의 트래픽을 허용할 수도 있습니다. 공용 로드 밸런서를 사용하여 중간 계층의 VM 간에 요청을 로드할 수 있습니다.

다음 두 구성 요소는 중간 계층에 있습니다.

- **논리 서버** - 비즈니스 논리 또는 비즈니스 기능을 포함합니다.
- **배치 서버** - 배치 처리에 사용

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

오라클의 피플소프트 애플리케이션 제품군에는 인적 자원 및 재무 관리를 위한 소프트웨어가 포함되어 있습니다. 애플리케이션 제품군은 다중 계층화이며 애플리케이션에는 HRMS(HRMS), 고객 관계 관리(CRM), 재무 및 공급망 관리(FSCM), 엔터프라이즈 성능 관리(EPM)가 포함됩니다.

소프트웨어 제품군의 각 계층을 자체 서브넷에 배포하는 것이 좋습니다. 오라클 데이터베이스 또는 Microsoft SQL Server는 응용 프로그램의 백 엔드 데이터베이스로 필요합니다. 이 섹션에서는 Oracle 데이터베이스 백 엔드를 사용하여 PeopleSoft 배포에 대한 자세한 내용을 설명합니다.

다음은 교차 클라우드 아키텍처에 PeopleSoft 응용 프로그램 제품군을 배포하기 위한 표준 아키텍처입니다(그림 5).

![피플소프트 크로스 클라우드 아키텍처](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*그림 5: 피플소프트 크로스 클라우드 아키텍처*

이 샘플 아키텍처에서 Azure의 가상 네트워크는 교차 클라우드 상호 연결을 사용하여 OCI의 가상 클라우드 네트워크에 연결됩니다. 응용 프로그램 계층은 Azure에서 설정되지만 데이터베이스는 OCI에서 설정됩니다. 특정 포트의 특정 서브넷에서만 트래픽을 허용하도록 네트워크 보안 그룹이 있는 자체 서브넷에 각 구성 요소를 배포하는 것이 좋습니다.

또한 이 아키텍처는 한 지역의 두 가용성 영역에서 Oracle Data Guard를 사용하여 구성된 가용성이 높은 Oracle 데이터베이스를 사용하여 Azure에서 전적으로 배포할 수 있습니다. 다음 다이어그램(그림 6)은 이 아키텍처 패턴의 예입니다.

![피플소프트 Azure 전용 아키텍처](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*그림 6: 피플소프트 Azure 전용 아키텍처*

다음 섹션에서는 상위 수준에서 다른 구성 요소를 설명합니다.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>애플리케이션 계층

응용 프로그램 계층에는 PeopleSoft 응용 프로그램 서버, PeopleSoft 웹 서버, 탄력적 검색 및 피플소프트 프로세스 스케줄러의 인스턴스가 포함되어 있습니다. Azure 로드 밸런서는 응용 프로그램 계층의 해당 서버로 라우팅되는 사용자의 요청을 수락하도록 설정됩니다.

고가용성을 위해 다양한 가용성 영역에 걸쳐 응용 프로그램 계층의 각 서버의 중복 인스턴스를 설정하는 것이 좋습니다. Azure 로드 밸런서를 여러 백 엔드 풀로 설정하여 각 요청을 올바른 서버로 전달할 수 있습니다.

### <a name="peopletools-client"></a>피플툴 클라이언트

PeopleTools 클라이언트는 개발, 마이그레이션 및 업그레이드와 같은 관리 작업을 수행하는 데 사용됩니다. PeopleTools 클라이언트는 응용 프로그램의 고가용성을 달성하기 위해 필요하지 않으므로 PeopleTools 클라이언트의 중복 서버가 필요하지 않습니다.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>다음 단계

[테라폼 스크립트를](https://github.com/microsoft/azure-oracle) 사용하여 Azure에서 Oracle 앱을 설정하고 OCI와 교차 클라우드 연결을 설정합니다.

OCI에 대한 자세한 정보 및 백서는 [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) 설명서를 참조하십시오.
