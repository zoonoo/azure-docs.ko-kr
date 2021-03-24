---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Azure 스프링 클라우드 참조 아키텍처
ms.author: akaleshian
ms.service: spring-cloud
description: 이 참조 아키텍처는 Azure 스프링 클라우드를 사용 하기 위한 일반적인 엔터프라이즈 허브 및 스포크 설계를 사용 하는 기반입니다.
ms.openlocfilehash: 74183ca2decf8487e5c41cf36d5784538021077f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878396"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Azure 스프링 클라우드 참조 아키텍처

이 참조 아키텍처는 Azure 스프링 클라우드를 사용 하기 위한 일반적인 엔터프라이즈 허브 및 스포크 설계를 사용 하는 기반입니다. 디자인에서 Azure 스프링 클라우드는 허브에서 호스트 되는 공유 서비스에 종속 된 단일 스포크에 배포 됩니다. 아키텍처는 [Microsoft Azure Well-Architected 프레임 워크][16]에서 개념을 얻기 위해 구성 요소를 사용 하 여 빌드됩니다.

이 아키텍처의 구현에 대 한 자세한 내용은 GitHub의 [Azure 스프링 클라우드 참조 아키텍처][10] 리포지토리를 참조 하세요.

이 아키텍처에 대 한 배포 옵션에는 ARM (Azure Resource Manager), Terraform 및 Azure CLI이 있습니다. 이 리포지토리의 아티팩트는 환경에 맞게 사용자 지정할 수 있는 토대를 제공 합니다. Azure 방화벽 또는 Application Gateway 같은 리소스를 다른 리소스 그룹 또는 구독으로 그룹화 할 수 있습니다. 이 그룹화를 사용 하면 IT 인프라, 보안, 비즈니스 응용 프로그램 팀 등 다양 한 기능을 별도로 유지할 수 있습니다.

## <a name="planning-the-address-space"></a>주소 공간 계획

Azure 스프링 클라우드에는 다음과 같은 두 가지 전용 서브넷이 필요 합니다.

* 서비스 런타임
* 스프링 부팅 응용 프로그램

이러한 각 서브넷에는 전용 클러스터가 필요 합니다. 여러 클러스터가 동일한 서브넷을 공유할 수 없습니다. 각 서브넷의 최소 크기는/28입니다. Azure 스프링 클라우드가 지원할 수 있는 응용 프로그램 인스턴스의 수는 서브넷 크기에 따라 달라 집니다. [가상 네트워크에서 Azure 스프링 클라우드 배포][17]의 [가상 네트워크 요구 사항][11] 섹션에서 VNET (자세한 Virtual Network) 요구 사항을 찾을 수 있습니다.

> [!WARNING]
> 선택한 서브넷 크기는 기존 VNET 주소 공간과 겹칠 수 없으며 피어 링 또는 온-프레미스 서브넷 주소 범위와 겹칠 수 없습니다.

## <a name="use-cases"></a>사용 사례

이 아키텍처의 일반적인 용도는 다음과 같습니다.

* 하이브리드 클라우드 환경에 배포 된 내부 응용 프로그램
* 외부 연결 응용 프로그램

이러한 사용 사례는 보안과 네트워크 트래픽 규칙을 제외 하 고 유사 합니다. 이 아키텍처는 각각의 미묘한 차이을 지원 하도록 설계 되었습니다.

## <a name="private-applications"></a>개인 응용 프로그램

다음 목록에서는 개인 응용 프로그램의 인프라 요구 사항에 대해 설명 합니다. 이러한 요구 사항은 매우 규제 된 환경에서 일반적입니다.

* 제어 평면 트래픽을 제외 하 고 공용 인터넷에 직접 송신 하지 않습니다.
* 송신 트래픽은 NVA (중앙 네트워크 가상 어플라이언스) (예: Azure 방화벽)를 통해 이동 해야 합니다.
* 미사용 데이터를 암호화 해야 합니다.
* 전송 중인 데이터를 암호화 해야 합니다.
* DevOps 배포 파이프라인을 사용할 수 있습니다 (예: Azure DevOps). Azure 스프링 클라우드에 네트워크를 연결 해야 합니다.
* Microsoft의 제로 신뢰 보안 방법에는 보안 자격 증명 모음에 저장 되는 암호, 인증서 및 자격 증명이 필요 합니다. 권장 되는 서비스 Azure Key Vault입니다.
* 응용 프로그램 호스트 DNS (Domain Name Service) 레코드는 Azure 사설 DNS에 저장 되어야 합니다.
* 온-프레미스 및 클라우드의 호스트 이름 확인은 양방향 이어야 합니다.
* 하나 이상의 보안 벤치 마크에 대 한 준수를 적용 해야 합니다.
* Azure 서비스 종속성은 서비스 끝점이 나 개인 링크를 통해 통신 해야 합니다.
* Azure 스프링 클라우드 배포에서 관리 하는 리소스 그룹은 수정 하면 안 됩니다.
* Azure 스프링 클라우드 배포에서 관리 되는 서브넷은 수정 하면 안 됩니다.
* 서브넷은 Azure 스프링 클라우드의 인스턴스를 하나만 포함 해야 합니다.
* [Azure 스프링 클라우드 구성 서버][8] 를 사용 하 여 리포지토리에서 구성 속성을 로드 하는 경우에는 리포지토리가 개인 이어야 합니다.

다음 목록에서는 디자인을 구성 하는 구성 요소를 보여 줍니다.

* 온-프레미스 네트워크
  * DNS (Domain Name Service)
  * 게이트웨이
* 허브 구독
  * Azure 방화벽 서브넷
  * Application Gateway 서브넷
  * 공유 서비스 서브넷
* 연결 된 구독
  * Virtual Network 피어
  * Azure 방호 서브넷

다음 목록에서는이 참조 아키텍처의 Azure 서비스에 대해 설명 합니다.

* [Azure 스프링 클라우드][1]: Java 기반 스프링 부팅 응용 프로그램 및 용으로 특별히 설계 되 고 최적화 된 관리 되는 서비스입니다. NET 기반 [Steeltoe][9] 응용 프로그램.

* [Azure Key Vault][2]: Microsoft id 서비스 및 계산 리소스와 긴밀 하 게 통합 된 하드웨어 지원 자격 증명 관리 서비스입니다.

* [Azure Monitor][3]: Azure 및 온-프레미스에 모두 배포 하는 응용 프로그램에 대 한 모니터링 서비스를 모두 포괄 하는 제품군입니다.

* [Azure Security Center][4]: 온-프레미스, 여러 클라우드 및 Azure에서 워크 로드에 대 한 통합 보안 관리 및 위협 방지 시스템입니다.

* [Azure Pipelines][5]: 업데이트 된 스프링 부팅 앱을 Azure 스프링 클라우드에 자동으로 배포할 수 있는 완전 한 기능을 갖춘 CI/CD (연속 통합/지속적인 개발) 서비스입니다.

다음 다이어그램은 위의 요구 사항을 해결 하는 잘 설계 된 허브 및 스포크 설계를 나타냅니다.

![전용 응용 프로그램에 대 한 참조 아키텍처 다이어그램](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>공용 응용 프로그램

다음 목록에서는 공용 응용 프로그램의 인프라 요구 사항에 대해 설명 합니다. 이러한 요구 사항은 매우 규제 된 환경에서 일반적입니다.

* 수신 트래픽은 최소한 Application Gateway 또는 Azure Front 도어를 통해 관리 되어야 합니다.
* Azure DDoS Protection standard를 사용 하도록 설정 해야 합니다.
* 제어 평면 트래픽을 제외 하 고 공용 인터넷에 직접 송신 하지 않습니다.
* 송신 트래픽은 NVA (중앙 네트워크 가상 어플라이언스) (예: Azure 방화벽)를 통과 해야 합니다.
* 미사용 데이터를 암호화 해야 합니다.
* 전송 중인 데이터를 암호화 해야 합니다.
* DevOps 배포 파이프라인을 사용할 수 있습니다 (예: Azure DevOps). Azure 스프링 클라우드에 네트워크를 연결 해야 합니다.
* Microsoft의 제로 신뢰 보안 방법에는 보안 자격 증명 모음에 저장 되는 암호, 인증서 및 자격 증명이 필요 합니다. 권장 되는 서비스 Azure Key Vault입니다.
* 응용 프로그램 호스트 DNS 레코드는 Azure 사설 DNS에 저장 되어야 합니다.
* 인터넷 라우팅 가능 주소는 Azure 공용 DNS에 저장 되어야 합니다.
* 온-프레미스 및 클라우드의 호스트 이름 확인은 양방향 이어야 합니다.
* 하나 이상의 보안 벤치 마크에 대 한 준수를 적용 해야 합니다.
* Azure 서비스 종속성은 서비스 끝점이 나 개인 링크를 통해 통신 해야 합니다.
* Azure 스프링 클라우드 배포에서 관리 하는 리소스 그룹은 수정 하면 안 됩니다.
* Azure 스프링 클라우드 배포에서 관리 되는 서브넷은 수정 하면 안 됩니다.
* 서브넷은 Azure 스프링 클라우드의 인스턴스를 하나만 포함 해야 합니다.

다음 목록에서는 디자인을 구성 하는 구성 요소를 보여 줍니다.

* 온-프레미스 네트워크
  * DNS (Domain Name Service)
  * 게이트웨이
* 허브 구독
  * Azure 방화벽 서브넷
  * Application Gateway 서브넷
  * 공유 서비스 서브넷
* 연결 된 구독
  * Virtual Network 피어
  * Azure 방호 서브넷

다음 목록에서는이 참조 아키텍처의 Azure 서비스에 대해 설명 합니다.

* [Azure 스프링 클라우드][1]: Java 기반 스프링 부팅 응용 프로그램 및 용으로 특별히 설계 되 고 최적화 된 관리 되는 서비스입니다. NET 기반 [Steeltoe][9] 응용 프로그램.

* [Azure Key Vault][2]: Microsoft id 서비스 및 계산 리소스와 긴밀 하 게 통합 된 하드웨어 지원 자격 증명 관리 서비스입니다.

* [Azure Monitor][3]: Azure 및 온-프레미스에 모두 배포 하는 응용 프로그램에 대 한 모니터링 서비스를 모두 포괄 하는 제품군입니다.

* [Azure Security Center][4]: 온-프레미스, 여러 클라우드 및 Azure에서 워크 로드에 대 한 통합 보안 관리 및 위협 방지 시스템입니다.

* [Azure Pipelines][5]: 업데이트 된 스프링 부팅 앱을 Azure 스프링 클라우드에 자동으로 배포할 수 있는 완전 한 기능을 갖춘 CI/CD (연속 통합/지속적인 개발) 서비스입니다.

* [Azure 애플리케이션 Gateway][6]: 계층 7에서 작동 하는 TLS (전송 계층 보안) 오프 로드를 사용 하는 응용 프로그램 트래픽을 담당 하는 부하 분산 장치입니다.

* [Azure 애플리케이션 방화벽][7]: 일반적인 악용 및 취약성 으로부터 응용 프로그램의 중앙 집중식 보호를 제공 하는 Azure 애플리케이션 게이트웨이의 기능입니다.

다음 다이어그램은 위의 요구 사항을 해결 하는 잘 설계 된 허브 및 스포크 설계를 나타냅니다.

![공용 응용 프로그램에 대 한 참조 아키텍처 다이어그램](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Azure 스프링 클라우드 온-프레미스 연결

Azure 스프링 클라우드에서 실행 되는 응용 프로그램은 다양 한 Azure, 온-프레미스 및 외부 리소스와 통신할 수 있습니다. 허브 및 스포크 디자인을 사용 하 여 응용 프로그램은 Express 경로 또는 사이트 간 VPN (가상 사설망)을 사용 하 여 외부 또는 온-프레미스 네트워크로 트래픽을 라우팅할 수 있습니다.

## <a name="azure-well-architected-framework-considerations"></a>Azure Well-Architected 프레임 워크 고려 사항

[Azure Well-Architected Framework][16] 는 강력한 인프라 기반을 설정 하는 데 따르는 지침 개념 집합입니다. 프레임 워크에는 비용 최적화, 운영 효율성, 성능 효율성, 안정성 및 보안 범주가 포함 되어 있습니다.

### <a name="cost-optimization"></a>비용 최적화

분산 시스템 디자인의 특성상 인프라 확산는 현실입니다. 이로 인해 예기치 않은 제어할 수 없는 비용이 발생 합니다. Azure 스프링 클라우드는 수요를 충족 하 고 비용을 최적화할 수 있도록 크기를 조정 하는 구성 요소를 사용 하 여 빌드됩니다. 이 아키텍처의 핵심은 Azure Kubernetes 서비스 (AKS)입니다. 서비스는 클러스터의 운영 비용 효율성을 포함 하는 Kubernetes 관리의 복잡성 및 운영 오버 헤드를 줄이도록 설계 되었습니다.

Azure 스프링 클라우드의 단일 인스턴스에 여러 응용 프로그램 및 응용 프로그램 유형을 배포할 수 있습니다. 서비스는 사용률과 비용 효율성을 향상 시킬 수 있는 메트릭 또는 일정에 의해 트리거되는 응용 프로그램의 자동 크기 조정을 지원 합니다.

Application Insights 및 Azure Monitor를 사용 하 여 운영 비용을 낮출 수도 있습니다. 포괄적인 로깅 솔루션에서 제공 하는 표시 유형을 사용 하 여 시스템의 구성 요소를 실시간으로 확장 하는 자동화를 구현할 수 있습니다. 또한 로그 데이터를 분석 하 여 시스템의 전반적인 비용과 성능을 향상 시키기 위해 해결할 수 있는 응용 프로그램 코드의 비효율성을 표시할 수 있습니다.

### <a name="operational-excellence"></a>운영 우수성

Azure 스프링 클라우드는 운영에 대 한 다양 한 측면을 다룹니다. 이러한 측면을 결합 하 여 다음 목록에 설명 된 대로 프로덕션 환경에서 서비스가 효율적으로 실행 되도록 할 수 있습니다.

* Azure Pipelines를 사용 하 여 배포의 안정성과 일관성을 유지 하는 동시에 사용자의 오류를 방지할 수 있습니다.
* Azure Monitor 및 Application Insights를 사용 하 여 로그 및 원격 분석 데이터를 저장할 수 있습니다.
  수집 된 로그 및 메트릭 데이터를 평가 하 여 응용 프로그램의 상태 및 성능을 확인할 수 있습니다. APM (응용 프로그램 성능 모니터링)은 Java 에이전트를 통해 서비스에 완벽 하 게 통합 됩니다. 이 에이전트는 추가 코드를 요구 하지 않고 배포 된 모든 응용 프로그램 및 종속성에 대 한 가시성을 제공 합니다. 자세한 내용은 블로그 게시물 [Azure 스프링 클라우드의 응용 프로그램 및 종속성][15]을 간편 하 게 모니터링을 참조 하세요.
* Azure Security Center를 사용 하 여 제공 된 데이터를 분석 하 고 평가 하는 플랫폼을 제공 하 여 응용 프로그램이 보안을 유지 하도록 할 수 있습니다.
* 서비스는 다양 한 배포 패턴을 지원 합니다. 자세한 내용은 [Azure Spring Cloud에서 스테이징 환경 설정][14]을 참조하세요.

### <a name="reliability"></a>안정성

Azure 스프링 클라우드는 기본 구성 요소로 AKS를 사용 하 여 설계 되었습니다. AKS는 클러스터링을 통해 복원 력을 제공 하는 반면,이 참조 아키텍처는 구성 요소가 실패할 경우 응용 프로그램의 가용성을 높이기 위해 서비스 및 아키텍처 고려 사항을 통합 합니다.

잘 정의 된 허브 및 스포크 디자인을 기반으로 구축 하 여이 아키텍처를 사용 하면 여러 지역에 배포할 수 있습니다. 전용 응용 프로그램 사용 사례의 경우 아키텍처는 Azure 사설 DNS를 사용 하 여 지리적으로 장애 발생 시 지속적으로 가용성을 보장 합니다. 공용 응용 프로그램 사용 사례의 경우 Azure Front 도어 및 Azure 애플리케이션 게이트웨이는 가용성을 보장 합니다.

### <a name="security"></a>보안

이 아키텍처의 보안은 업계에서 정의한 컨트롤 및 벤치 마크를 준수 하 여 해결 됩니다. 이 아키텍처의 컨트롤은 CSA ( [Cloud Security 동맹][18] ) [Microsoft Azure][20] 및 ci (MAFB) [에 대 한 센터][21] 에서 ci (인터넷 보안)에 이르기까지 CCM ( [cloud Control Matrix][19] )을 기반으로 합니다. 적용 된 컨트롤에서 주요 보안 설계 원리는 거 버 넌 스, 네트워킹 및 응용 프로그램 보안에 중점을 두고 있습니다. 대상 인프라와 관련 된 Id, 액세스 관리 및 저장소의 설계 원리를 처리 하는 것은 사용자의 책임입니다.

#### <a name="governance"></a>거버넌스

이 아키텍처에서 해결 하는 거 버 넌 스의 주요 측면은 네트워크 리소스의 격리를 통해 분리. CCM에서 DCS-08은 데이터 센터에 대 한 수신 및 송신 제어를 권장 합니다. 이 컨트롤을 충족 하기 위해 아키텍처는 NSGs (네트워크 보안 그룹)를 사용 하는 허브 및 스포크 디자인을 사용 하 여 리소스 간에 동-서 트래픽을 필터링 합니다. 또한이 아키텍처는 허브의 중앙 서비스와 스포크 리소스 간의 트래픽을 필터링 합니다. 아키텍처는 Azure 방화벽의 인스턴스를 사용 하 여 아키텍처 내의 리소스와 인터넷 간의 트래픽을 관리 합니다.

다음 목록에서는이 참조의 데이터 센터 보안을 처리 하는 컨트롤을 보여 줍니다.

| CSA CCM 컨트롤 ID | CSA CCM 제어 도메인 |
| :----------------- | :----------------------|
| DC-08 | 데이터 센터 보안 권한 없는 사람 항목 |

#### <a name="network"></a>네트워크

이 아키텍처를 지 원하는 네트워크 설계는 기존 허브 및 스포크 모델에서 파생 됩니다. 이 결정은 네트워크 격리가 기본 구문 인지 확인 합니다. CCM control IV-06은 신뢰할 수 있고 신뢰할 수 없는 환경 간에 네트워크와 가상 컴퓨터 간의 트래픽을 제한 하 고 모니터링 하는 것을 권장 합니다. 이 아키텍처는 동-서 트래픽에 대 한 NSGs의 구현에 의해 제어를 적용 하 고 북쪽 남부 트래픽에 대 한 Azure 방화벽을 구현 합니다. CCM control IPY가는 인프라에서 서비스 간 데이터 교환의 보안 네트워크 프로토콜을 사용 하도록 권장 합니다. 이 아키텍처를 지 원하는 Azure 서비스는 모두 HTTP 및 SQL에 대 한 TLS와 같은 표준 보안 프로토콜을 사용 합니다.

다음 목록에서는이 참조의 네트워크 보안을 다루는 CCM 컨트롤을 보여 줍니다.

| CSA CCM 컨트롤 ID | CSA CCM 제어 도메인 |
| :----------------- | :----------------------|
| IPY가-04             | 네트워크 프로토콜      |
| IV-06             | 네트워크 보안       |

네트워크 구현은 MAFB에서 컨트롤을 정의 하 여 더 안전 하 게 보호 됩니다. 컨트롤은 환경에 대 한 트래픽이 공용 인터넷에서 제한 되도록 합니다.

다음 목록에서는이 참조에서 네트워크 보안을 처리 하는 CIS 컨트롤을 보여 줍니다.

| CIS 컨트롤 ID | CIS 컨트롤 설명 |
| :------------- | :---------------------- |
| 6.2 | 인터넷에서 SSH 액세스를 제한 하는지 확인 합니다. |
| 6.3 | SQL 데이터베이스에서 수신 0.0.0.0/0 (모든 IP)을 허용 하지 않는지 확인 합니다. |
| 6.5 | Network Watcher이 ' 사용 ' 인지 확인 하십시오. |
| 6.6 | UDP를 사용 하는 수신이 인터넷에서 제한 되는지 확인 합니다. |

Azure 스프링 클라우드는 보안 환경에 배포 될 때 Azure에서 송신 하는 관리 트래픽이 필요 합니다. 이를 위해서는 [VNET에서 Azure 스프링 클라우드를 실행 하기 위해 고객 책임](./spring-cloud-vnet-customer-responsibilities.md)에 나열 된 네트워크 및 응용 프로그램 규칙을 허용 해야 합니다.

#### <a name="application-security"></a>애플리케이션 보안

이 디자인 주체는 id, 데이터 보호, 키 관리 및 응용 프로그램 구성의 기본 구성 요소를 다룹니다. 기본적으로 Azure 스프링 클라우드에 배포 된 응용 프로그램은 작동 하는 데 필요한 최소 권한으로 실행 됩니다. 권한 부여 컨트롤 집합은 서비스를 사용 하는 경우 데이터 보호와 직접적으로 관련이 있습니다. 키 관리는 계층화 된 응용 프로그램 보안 방법을 강화 합니다.

다음 목록에서는이 참조의 키 관리를 다루는 CCM 컨트롤을 보여 줍니다.

| CSA CCM 컨트롤 ID | CSA CCM 제어 도메인 |
| :----------------- | :--------------------- |
| EKM-01 | 암호화 및 키 관리 권한 |
| EKM-02 | 암호화 및 키 관리 키 생성 |
| EKM-03 | 암호화 및 키 관리 중요 한 데이터 보호 |
| EKM-04 | 암호화 및 키 관리 저장소 및 액세스 |

CCM, EKM-02 및 EKM-03에서는 키를 관리 하 고 암호화 프로토콜을 사용 하 여 중요 한 데이터를 보호 하는 정책 및 절차를 권장 합니다. EKM-01은 모든 암호화 키가 관리할 수 있도록 식별할 수 있는 소유자를 권장 합니다. EKM-04는 표준 알고리즘을 사용 하는 것을 권장 합니다.

다음 목록에서는이 참조의 키 관리를 처리 하는 CIS 컨트롤을 보여 줍니다.

| CIS 컨트롤 ID | CIS 컨트롤 설명 |
| :------------- | :---------------------- |
| 8.1 | 모든 키에 대해 만료 날짜가 설정 되었는지 확인 합니다. |
| 8.2 | 모든 암호에 대해 만료 날짜가 설정 되었는지 확인 합니다. |
| 8.4 | Key vault를 복구할 수 있는지 확인 합니다. |

CIS에서 8.1 및 8.2 컨트롤은 회전이 적용 되도록 자격 증명에 대 한 만료 날짜를 설정 하는 것을 권장 합니다. CIS 제어 8.4은 비즈니스 연속성을 유지 하기 위해 key vault의 콘텐츠를 복원할 수 있는지 확인 합니다.

응용 프로그램 보안의 측면은이 참조 아키텍처를 사용 하 여 Azure에서 스프링 워크 로드를 지원 하기 위한 기초를 설정 하는 것입니다.

## <a name="next-steps"></a>다음 단계

ARM, Terraform 및 [Azure 스프링 클라우드 참조 아키텍처][10] 리포지토리에서 사용할 수 있는 Azure CLI 배포를 통해이 참조 아키텍처를 살펴보세요.

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/