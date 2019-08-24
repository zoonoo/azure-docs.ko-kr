---
title: Azure 오스트레일리아에서 송신 트래픽 제어
description: 보안 인터넷 게이트웨이의 오스트레일리아 정부 요구 사항을 충족 하기 위해 Azure에서 송신 트래픽을 제어 하는 주요 요소
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602091"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Azure 오스트레일리아에서 송신 트래픽 제어

ICT 시스템 보안의 기본 구성 요소는 네트워크 트래픽을 제어 합니다. 시스템이 작동 하는 데 필요한 트래픽만 통신을 제한 하면 손상 가능성이 줄어듭니다. 응용 프로그램 및 서비스와 통신 하는 외부 시스템에 대 한 가시성 및 제어를 통해 손상 된 시스템을 검색 하 고, 데이터를 시도 했거나 성공적으로 데이터를 필터링 할 수 있습니다. 이 문서에서는 Azure 내에서 아웃 바운드 (송신) 네트워크 트래픽이 작동 하는 방법에 대 한 정보를 제공 하 고, ACSC (오스트레일리아 사이버 Security Center)에 맞는 인터넷 연결 시스템의 네트워크 보안 제어를 구현 하기 위한 권장 사항을 제공 합니다. 소비자 지침과 ACSC의 사용자 ISM (Information Security Manual)의 의도

## <a name="requirements"></a>요구 사항

시스템에 대 한 전반적인 보안 요구 사항은 ISM에 정의 되어 있습니다. 네트워크 보안을 구현 하는 데 도움이 되는 acsc는 acsc Protect를 게시 _했습니다. 네트워크 구분 및 분리_구현 및 클라우드 환경에서 시스템 보안을 지원 하기 위해 acsc는 _테 넌 트에 대해 클라우드 컴퓨팅 보안_을 게시 했습니다.

ACSC 문서에서는 네트워크 보안을 구현 하 고 트래픽을 제어 하기 위한 컨텍스트를 간략히 설명 하 고 네트워크 디자인 및 구성에 대 한 실용적인 권장 사항을 제공 합니다.

Azure에서 송신 트래픽을 제어 하기 위한 다음과 같은 주요 요구 사항은 ACSC 문서에서 확인 되었습니다.

Description|Source
--------------- |------------------
**네트워크 구분 및 분리 구현**. 예를 들어, 호스트 기반 방화벽 및 네트워크 액세스 제어를 사용 하 여 n 계층 아키텍처를 사용 하면 인바운드 및 아웃 바운드 네트워크 연결을 필요한 포트 및 프로토콜로만 제한할 수 있습니다.| [테 넌 트에 대 한 클라우드 컴퓨팅](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
테 넌 트의 가용성 요구 사항을 충족 하기 위해 테 넌 트 (테 넌 트의 원격 사용자 포함)와 클라우드 서비스 간에 **적절 한 높은 대역폭, 짧은 대기 시간, 안정적인 네트워크 연결을 구현** 합니다.  | [ACSC 보호: 네트워크 분할 및 분리 구현](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**네트워크 계층 이상 에서만 기술을 적용**합니다. 각 호스트 및 네트워크는 가능 하면 실제로 관리할 수 있는 가장 낮은 수준에서 조각화 및 분리 되어야 합니다. 대부분의 경우이는 응용 프로그램 계층을 포함 하 여 데이터 링크 계층에서 적용 됩니다. 그러나 중요 한 환경에서는 물리적 격리가 적절할 수 있습니다. 호스트 기반 및 네트워크 차원의 측정값을 보완 방식으로 배포 하 고 중앙 집중식으로 모니터링 해야 합니다. 유일한 보안 조치로만 방화벽이 나 보안 어플라이언스를 구현할 수 있는 것은 아닙니다. |[ACSC 보호: 네트워크 분할 및 분리 구현](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**최소 권한의 원칙을 사용 하 고 ‐ ‐ 해야**합니다. 호스트, 서비스 또는 네트워크가 다른 호스트, 서비스 또는 네트워크와 통신할 필요가 없는 경우에는이를 허용 하지 않아야 합니다. 호스트, 서비스 또는 네트워크가 특정 포트나 프로토콜의 다른 호스트, 서비스 또는 네트워크와만 통신 해야 하는 경우 해당 포트 및 프로토콜로만 제한 되어야 합니다. 네트워크에서 이러한 원칙을 채택 하면 사용자 권한 minimisation을 보완 하 고 환경의 전반적인 보안 상태를 크게 증가 시킵니다. |[ACSC 보호: 네트워크 분할 및 분리 구현](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**비즈니스 운영에 대 한 민감도 또는 중요도에 따라 호스트와 네트워크를 분리**합니다. 여기에는 특정 호스트나 네트워크에 대 한 다양 한 보안 분류, 보안 도메인 또는 가용성/무결성 요구 사항에 따라 다른 하드웨어 또는 플랫폼을 사용 하는 것이 포함 될 수 있습니다. 특히, 관리 네트워크를 분리 하 고 중요 한 환경에 대해 대역 외 관리 네트워크를 물리적으로 격리 하는 것이 좋습니다. |[ACSC 보호: 네트워크 분할 및 분리 구현](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**모든 엔터티에서 다른 모든 엔터티에 대 한 ise 액세스를 식별, 인증 및 권한**부여 합니다. 모든 사용자, 호스트 및 서비스는 지정 된 의무 또는 기능을 수행 하는 데 필요한 것으로 제한 된 다른 모든 사용자, 호스트 및 서비스에 대 한 액세스 권한이 있어야 합니다. 식별, 인증 및 권한 부여 서비스의 강도를 우회 하거나 다운 그레이드 하는 모든 레거시 또는 로컬 서비스는 가능 하면 사용 하지 않도록 설정 하 고 사용을 면밀 하 게 모니터링 해야 합니다. |[ACSC 보호: 네트워크 분할 및 분리 구현](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**거부 목록 대신 네트워크 트래픽 목록 허용을 구현**합니다. 알려진 불량 네트워크 트래픽 (예: 특정 주소 또는 서비스 차단)에 대 한 액세스를 거부 하는 것이 아니라 알려진 양호한 네트워크 트래픽 (식별 된 트래픽, 인증 된 트래픽 및 공인)에 대 한 액세스만 허용 합니다. 허용 목록을 사용 하면 목록을 거부 하는 뛰어난 보안 정책이 생성 되며 잠재적 네트워크 침입을 감지 하 고 평가 하기 위해 용량을 크게 향상 시킬 수 있습니다. |[ACSC 보호: 네트워크 분할 및 분리 구현](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
허용 되는 **웹 사이트의 허용 목록을 정의 하 고 나열 되지 않은 모든 웹 사이트** 를 효과적으로 차단 하면 악의적 사용자가 사용 하는 가장 일반적인 데이터 배달 및 반출 기술 중 하나가 제거 됩니다. 사용자가 다양 한 웹 사이트 또는 빠르게 변경 된 웹 사이트에 액세스할 수 있는 합법적인 요구 사항이 있는 경우 이러한 구현의 비용을 고려해 야 합니다. 상대적으로 허용 되는 허용 목록은 거부 목록에 의존 하는 것 보다 더 나은 보안을 제공 하는 동시에 구현 비용을 줄이는 데에도 적용 됩니다. 허용 허용 목록의 예는 전체 오스트레일리아 하위 도메인 (' *. au ')을 허용 하거나 Alexa 사이트 순위 (동적 도메인 이름 시스템 (DDNS) 도메인 및 기타 부적절 한 도메인을 필터링 한 후)에서 상위 1000 사이트를 허용 하도록 허용할 수 있습니다.| [오스트레일리아 정부 정보 보안 설명서 (ISM)](https://www.cyber.gov.au/ism)
|

이 문서에서는 Azure 환경에서 나가는 네트워크 트래픽이 제어 되는 방법에 대 한 정보와 권장 사항을 제공 합니다. IaaS (Infrastructure as a Service) 및 PaaS (Platform as a service)를 사용 하 여 Azure에 배포 된 시스템을 다룹니다.

[게이트웨이 수신 트래픽](gateway-ingress-traffic.md) 문서는 Azure 환경에 들어가는 네트워크 트래픽 주소를 제공 하며 전체 네트워크 제어 범위에 대 한이 문서와 함께 제공 됩니다.

## <a name="architecture"></a>아키텍처

네트워크 보안을 설계 하 고 구현할 때 송신 트래픽을 적절 하 게 제어 하려면 먼저 IaaS 및 PaaS 모두에서 송신 네트워크 트래픽이 Azure 내에서 작동 하는 방식을 이해 해야 합니다. 이 섹션에서는 Azure에서 호스트 되는 리소스에 의해 생성 된 아웃 바운드 트래픽이 처리 되는 방식과 해당 트래픽을 제한 하 고 제어 하는 데 사용할 수 있는 보안 제어 방법에 대 한 개요를 제공 합니다.

### <a name="architecture-components"></a>아키텍처 구성 요소

여기에 표시 된 아키텍처 다이어그램은 가상 네트워크의 서브넷에 배포 된 시스템을 종료할 때 네트워크 트래픽이 수행할 수 있는 경로를 보여 줍니다. 가상 네트워크의 트래픽은 관리 및 서브넷 수준에서 제어 되며, 라우팅 및 보안 규칙은 내에 포함 된 리소스에 적용 됩니다. 송신 트래픽과 관련 된 구성 요소는 시스템, 유효 경로, 다음 홉 유형, 보안 제어 및 PaaS 송신으로 나뉩니다.

![아키텍처](media/egress-traffic.png)

### <a name="systems"></a>시스템

시스템은 가상 네트워크의 일부인 IP 서브넷 내에서 아웃 바운드 트래픽을 생성 하는 Azure 리소스 및 관련 구성 요소입니다.

| 구성 요소 | 설명 |
| --- | ---|
|Virtual Network(VNet) | VNet은 Azure 내에서 리소스를 배포 하 고 통신을 가능 하 게 하는 플랫폼 및 경계를 제공 하는 기본 리소스입니다. VNet은 Azure 지역 내에 있으며 Virtual Machines와 같은 VNet 통합 리소스에 대 한 IP 주소 공간 및 라우팅 경계를 정의 합니다.|
|Subnet | 서브넷은 VNet 내에 생성 되는 IP 주소 범위입니다. 네트워크 조각화를 위해 VNet 내에서 여러 서브넷을 만들 수 있습니다.|
|네트워크 인터페이스| 네트워크 인터페이스는 Azure에 존재 하는 리소스입니다. 가상 컴퓨터에 연결 되 고, 연결 된 서브넷에서 인터넷을 통해 라우팅할 수 있는 개인 IP 주소가 할당 됩니다. 이 IP 주소는 Azure Resource Manager을 통해 동적 또는 정적으로 할당 됩니다.|
|공용 IP| 공용 IP는 가상 네트워크 내에서 사용 하기 위해 지정 된 지역에서 Microsoft 소유의 공용 인터넷 라우팅 가능 IP 주소 중 하나를 예약 하는 리소스입니다. 이를 특정 네트워크 인터페이스 또는 PaaS 리소스와 연결 하 여 리소스에서 인터넷, Express 경로 및 다른 PaaS 시스템과 통신할 수 있습니다.|
|

### <a name="routes"></a>경로

송신 트래픽이 사용 하는 경로는 해당 리소스에 대 한 유효 경로에 따라 달라 집니다 .이 경로는 가능한 모든 원본에서 학습 하는 경로와 Azure 라우팅 논리의 조합에 의해 결정 되는 경로의 결과 집합입니다.

| 구성 요소 | 설명 |
|--- | ---|
|시스템 경로| Azure는 시스템 경로를 자동으로 만들고 가상 네트워크의 각 서브넷에 경로를 할당합니다. 시스템 경로를 만들거나 제거할 수 없지만 일부는 사용자 지정 경로를 사용 하 여 재정의할 수 있습니다. Azure는 각 서브넷에 대 한 기본 시스템 경로를 만들고 특정 Azure 기능이 utilised 될 때 특정 서브넷 또는 모든 서브넷에 선택적 기본 경로를 추가 합니다.|
|서비스 엔드포인트| 서비스 끝점은 서브넷에서 특정 PaaS 기능으로의 직접 개인 송신 연결을 제공 합니다. PaaS 기능 하위 집합에만 사용할 수 있는 서비스 끝점은 PaaS에 액세스 하는 VNet의 리소스에 대 한 향상 된 성능 및 보안을 제공 합니다.|
|경로 테이블| 경로 테이블은 Border Gateway Protocol를 통해 학습를 통해 시스템 경로 또는 경로를 보완 하거나 재정의할 수 있는 UDRs (사용자 정의 경로)를 지정 하기 위해 만들 수 있는 Azure의 리소스입니다. 각 UDR은 네트워크, 네트워크 마스크 및 다음 홉을 지정 합니다. 경로 테이블은 서브넷에 연결 될 수 있으며 동일한 경로 테이블은 여러 서브넷에 연결 될 수 있지만 서브넷에는 0 개 또는 하나의 경로 테이블만 있을 수 있습니다.|
|BGP(Border gateway protocol)| BGP는 자치 시스템 라우팅 프로토콜입니다. 자치 시스템은 일반적인 관리 및 일반적인 라우팅 정책에 따라 네트워크 또는 네트워크 그룹입니다. BGP는 자치 시스템 간에 라우팅 정보를 교환 하는 데 사용 됩니다. BGP는 가상 네트워크 게이트웨이를 통해 가상 네트워크에 통합할 수 있습니다.|
|

### <a name="next-hop-types-defined"></a>정의 된 다음 홉 유형

Azure 내의 각 경로에는 트래픽 처리 방법을 결정 하는 네트워크 범위와 연결 된 서브넷 마스크 및 다음 홉이 포함 됩니다.

다음 홉 유형 | Description
---- | ----
**Virtual Network** | 가상 네트워크의 주소 공간 내에서 주소 범위 간의 트래픽을 라우팅합니다. Azure는 가상 네트워크의 주소 공간 내에 정의된 각 주소 범위에 해당하는 주소 접두사가 포함된 경로를 만듭니다. 가상 네트워크 주소 공간에 정의 된 여러 주소 범위가 있는 경우 Azure는 각 주소 범위에 대 한 개별 경로를 만듭니다. Azure는 각 주소 범위에 대해 생성 된 경로를 사용 하 여 VNet 내의 서브넷 간에 트래픽을 자동으로 라우팅합니다.
**VNet 피어링** | 두 가상 네트워크 간에 가상 네트워크 피어 링을 만들면 각 가상 네트워크의 각 주소 범위에 대 한 경로가 피어 링 가상 네트워크에 추가 됩니다. 트래픽은 가상 네트워크 내의 서브넷과 동일한 방식으로 피어 링 가상 네트워크 간에 라우팅됩니다.
**가상 네트워크 게이트웨이** | 가상 네트워크 게이트웨이가 가상 네트워크에 추가 될 때 다음 홉 유형으로 나열 되는 가상 네트워크 게이트웨이를 사용 하는 하나 이상의 경로가 추가 됩니다. 포함 된 경로는 로컬 네트워크 게이트웨이 리소스 내에서 구성 된 경로와 BGP를 통해 학습 된 경로입니다.
**가상 어플라이언스** | 가상 어플라이언스는 일반적으로 방화벽과 같은 네트워크 응용 프로그램을 실행 합니다. 가상 어플라이언스를 사용 하면 필터링, 검사 또는 주소 변환과 같은 트래픽 처리가 추가로 처리 됩니다. 가상 어플라이언스 홉 유형이 포함 된 각 경로는 다음 홉 IP 주소도 지정 해야 합니다.
**VirtualNetworkServiceEndpoint** | 서비스 끝점을 사용 하도록 설정한 경우 특정 서비스에 대 한 공용 IP 주소는 다음 홉이 VirtualNetworkServiceEndpoint 인 서브넷에 경로로 추가 됩니다. 서비스 끝점은 가상 네트워크 내의 개별 서브넷에 있는 개별 서비스에 대해 사용 하도록 설정 됩니다. Azure 서비스의 공용 IP 주소는 주기적으로 변경됩니다. 주소가 변경되면 Azure는 자동으로 경로 테이블의 주소를 관리합니다.
**인터넷** | 인터넷의 다음 홉이 있는 트래픽은 가상 네트워크를 종료 하 고 연결 된 Azure 지역에서 사용할 수 있는 동적 풀에서 또는 해당 리소스에 대해 구성 된 공용 IP 주소를 사용 하 여 공용 IP 주소로 자동으로 변환 됩니다. 대상 주소가 Azure의 서비스 중 하나인 경우 트래픽을 인터넷으로 라우팅하는 대신 Azure의 백본 네트워크를 통해 서비스로 직접 라우팅됩니다. Azure 서비스 간 트래픽은 가상 네트워크가 있는 Azure 지역 또는 Azure 서비스 인스턴스가 배포된 Azure 지역과 관계없이 인터넷을 거치지 않습니다.
**없음** | 다음 홉이 없음 인 트래픽은 삭제 됩니다. Azure는 다음 홉 유형으로 없음을 사용 하 여 예약 된 주소 접두사에 대 한 시스템 기본 경로를 만듭니다. 다음 홉이 없음 인 경로를 경로 테이블을 사용 하 여 추가 하 여 트래픽이 특정 네트워크로 라우팅되지 않도록 할 수도 있습니다.
|

### <a name="security-controls"></a>보안 컨트롤

컨트롤 | Description
----- | -----
**NSGs (네트워크 보안 그룹)** | NSGs는 Azure에서 가상 네트워크 리소스에 대 한 트래픽을 제어 합니다. NSGs는 Azure 내의 트래픽과 온-프레미스 또는 인터넷과 같은 외부 네트워크와 Azure 간의 트래픽을 포함 하 여 허용 되거나 거부 되는 트래픽 흐름에 대 한 규칙을 적용 합니다. NSGs는 가상 네트워크 내의 서브넷 또는 개별 네트워크 인터페이스에 적용 됩니다.
**Azure Firewall** | Azure 방화벽은 Azure virtual network 리소스를 보호 하는 관리 되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. IP 주소, 프로토콜 및 포트를 기반으로 하는 기존 네트워크 필터링 규칙을 사용 하 여 Azure 방화벽을 구성할 수 있지만 FQDN (정규화 된 도메인 이름), 서비스 태그 및 기본 제공 위협 인텔리전스에 기반한 필터링도 지원 됩니다.
**NVA (네트워크 가상 어플라이언스)** | 네트워크 가상 어플라이언스는 네트워킹, 보안 및 기타 기능을 Azure에 제공할 수 있는 가상 머신 미디어입니다. Nva는 가상 네트워크 및 배포에서 Vm 형식의 네트워크 기능 및 서비스를 지원 합니다. Nva를 사용 하 여 특정 요구 사항을 해결 하거나, 관리 및 운영 도구와 통합 하거나, 기존 제품과 일관성을 제공할 수 있습니다. Azure는 WAF(웹 애플리케이션 방화벽), 방화벽, 게이트웨이/라우터, ADC(애플리케이션 배달 컨트롤러) 및 WAN 최적화 도구를 비롯한 광범위한 타사 네트워크 가상 어플라이언스를 지원합니다.
**서비스 끝점 정책 (미리 보기)** | Virtual network 서비스 끝점 정책을 사용 하면 서비스 끝점을 통해 특정 Azure 서비스 리소스만 허용 하는 Azure 서비스에 대 한 가상 네트워크 트래픽을 필터링 할 수 있습니다. 엔드포인트 정책은 Azure 서비스에 대한 가상 네트워크 트래픽의 상세 액세스 제어를 제공합니다.
**Azure Policy** | Azure Policy는 정책을 만들고 할당 하 고 관리 하는 Azure의 서비스입니다. 이러한 정책은 규칙을 사용 하 여 배포할 수 있는 리소스 유형과 해당 리소스의 구성을 제어 합니다. 정책을 사용 하면 요구 사항을 충족 하지 않거나 모니터링에 호환성 상태를 보고 하는 데 사용할 수 있는 경우 리소스를 배포 하지 못하도록 하 여 준수를 강제 적용할 수 있습니다.
|

### <a name="paas-egress"></a>PaaS 송신

대부분의 PaaS 리소스는 송신 트래픽을 생성 하지 않지만 인바운드 요청 (예: Application Gateway, 저장소, SQL Database 등)에 응답 하거나 다른 리소스 (예: Service Bus 및 Azure Relay)의 데이터를 릴레이 합니다. 저장소 또는 SQL 데이터베이스와 App Services 같은 PaaS 리소스 간의 네트워크 통신 흐름은 Azure에 의해 제어 되 고 포함 되며, 네트워크 조각화 또는 분리이 아닌 id 및 기타 리소스 구성 제어를 통해 보안이 유지 됩니다.

가상 네트워크에 배포 된 PaaS 리소스는 전용 IP 주소를 수신 하며, 가상 네트워크의 다른 리소스와 동일한 방식으로 모든 라우팅 컨트롤과 NSGs가 적용 됩니다. 가상 네트워크 내에 존재 하지 않는 PaaS 리소스는 리소스의 모든 인스턴스에서 공유 되는 IP 주소 풀을 제품, Microsoft 문서를 통해 게시 하거나 Azure Resource Manager를 통해 확인할 수 있습니다.

## <a name="general-guidance"></a>일반 지침

Azure 내에서 보안 솔루션을 설계 하 고 빌드하려면 네트워크 트래픽을 이해 하 고 제어 하는 것이 중요 하며,이를 확인 하 고 공인 통신만 수행할 수 있습니다. 이 지침과 이후 섹션에서 특정 구성 요소 지침은 [acsc Protect에 설명 된 원칙을 적용 하기 위해 utilised 수 있는 도구와 서비스를 설명 하는 것입니다. Azure 워크 로드에서 네트워크](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) 조각화 및 분리 구현. 여기에는 온-프레미스 환경에서 사용할 수 있는 것과 동일한 기존 물리적 및 네트워크 컨트롤을 적용할 수 없는 경우 리소스를 보호 하기 위한 가상 아키텍처를 만드는 방법을 자세히 설명 합니다.

### <a name="guidance"></a>지침

* 가상 네트워크에 대 한 송신 지점의 수 제한
* 인터넷에 대 한 직접적인 아웃 바운드 통신이 필요 하지 않은 모든 서브넷에 대 한 시스템 기본 경로를 재정의 합니다.
* 모든 수신 및 송신 지점과 Azure 리소스를 식별 하 고 제어 하는 완전 한 네트워크 아키텍처를 설계 하 고 구현 합니다.
* Microsoft 가상 데이터 센터 (VDC) 설명서에 설명 된 대로 가상 네트워크에 대 한 허브 및 스포크 네트워크 디자인을 utilising 고려 합니다.
* 인터넷에 대 한 아웃 바운드 연결에 대 한 기본 제공 보안 기능으로 제품 제품 (예: Azure 방화벽, 네트워크 가상 어플라이언스 또는 웹 프록시)
* 역할 기반 액세스, 조건부 액세스 및 MFA (Multi-factor Authentication)와 같은 id 컨트롤을 사용 하 여 네트워크 구성 권한 제한
* 네트워크 구성의 주요 요소를 수정 하거나 삭제할 수 없도록 잠금을 구현 합니다.
* 분리 및 제어를 강화 하기 위해 VNet 통합 구성에서 PaaS 배포
* 온-프레미스 네트워크와 연결 하기 위한 Express 경로 구현
* 외부 네트워크와 통합할 Vpn 구현
* 제품은 지역 및 리소스를 시스템 기능에 필요한 것 으로만 제한 하는 Azure Policy 합니다.
* 제품 Azure Policy 하 여 리소스에 대 한 기준선 보안 구성을 적용 합니다.
* Azure 내에서 네트워크 트래픽의 로깅, 감사 및 표시를 위해 Network Watcher 및 Azure Monitor 활용

### <a name="resources"></a>리소스

항목 | 링크
-----------| ---------
_오스트레일리아 규정 및 정책 준수 문서 (소비자 지침 포함)_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Azure 가상 데이터 센터_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_ACSC 네트워크 조각화_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_테 넌 트 용 ACSC 클라우드 보안_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_ACSC Information Security 수동_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>구성 요소 지침

이 섹션에서는 Azure에 배포 된 시스템의 송신 트래픽과 관련 된 개별 구성 요소에 대 한 추가 지침을 제공 합니다. 각 섹션에서는 디자인 및 빌드 작업을 지 원하는 데 사용할 수 있는 설명서 및 구성 가이드에 대 한 링크가 있는 특정 구성 요소의 의도를 설명 합니다.

### <a name="systems-security"></a>시스템 보안

Azure 내의 리소스에 대 한 모든 통신은 연결 및 보안 기능을 제공 하는 Microsoft에서 유지 관리 하는 네트워크 인프라를 통과 합니다. Azure 플랫폼과 네트워크 인프라를 보호 하기 위해 Microsoft에서 자동으로 보호 범위를 설정 하 고, 추가 기능을 Azure 내에서 서비스로 사용 하 여 네트워크 트래픽을 제어 하 고 네트워크 조각화를 설정 하 고, 분리.

### <a name="virtual-network-vnet"></a>Virtual Network(VNet)

가상 네트워크는 Azure의 네트워킹에 대 한 기본 구성 요소 중 하나입니다. 가상 네트워크는 다양 한 시스템에서 사용할 IP 주소 공간 및 라우팅 경계를 정의 합니다. 가상 네트워크는 서브넷으로 나뉘어 Virtual Network 내의 모든 서브넷은 서로 직접 네트워크 경로를 가집니다. 가상 네트워크 게이트웨이 (Express 경로 또는 VPN)를 사용 하 여 가상 네트워크 내의 시스템은 온-프레미스 및 외부 환경과 통합할 수 있으며, Azure에서 제공 하는 NAT (네트워크 주소 변환) 및 공용 IP 주소 할당을 통해 시스템에서 인터넷 또는 다른 Azure 지역 및 서비스에 연결 합니다. 가상 네트워크 및 관련 구성 매개 변수 및 라우팅에 대 한 이해는 송신 네트워크 트래픽을 이해 하 고 제어 하는 데 매우 중요 합니다.

가상 네트워크는 Azure 내에서 기본 주소 공간 및 라우팅 경계를 형성 하므로 네트워크 구분 및 분리의 기본 구성 요소로 사용할 수 있습니다.

| 리소스 | 링크 |
| --- | --- |
| *가상 네트워크 개요* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *Virtual Networks 계획 방법 가이드*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *Virtual Network 빠른 시작 만들기* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

서브넷은 Azure 내에서 네트워크 조각화 및 분리을 위한 중요 한 구성 요소입니다. 서브넷은 시스템 간에 분리를 제공 하는 데 사용할 수 있습니다. 서브넷은 NSGs, 경로 테이블 및 서비스 끝점이 적용 되는 가상 네트워크 내의 리소스입니다. 서브넷은 방화벽 규칙과 액세스 제어 목록의 원본 및 대상 주소로 사용할 수 있습니다.

가상 네트워크 내의 서브넷은 워크 로드 및 시스템의 요구 사항을 충족 하도록 계획 해야 합니다. 서브넷의 디자인 또는 구현에 관여 하는 개인은 네트워크 구분에 대 한 ACSC 지침을 참조 하 여 서브넷 내에서 시스템을 함께 그룹화 하는 방법을 결정 해야 합니다.

|리소스|링크|
|---|---|
|*가상 네트워크 서브넷 추가, 변경 또는 삭제* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>네트워크 인터페이스

네트워크 인터페이스는 가상 머신에서 모든 송신 트래픽에 대 한 원본입니다. 네트워크 인터페이스를 사용 하면 IP 주소 지정을 구성할 수 있으며, NSGs를 적용 하거나 NVA를 통해 트래픽을 라우팅하는 데 사용할 수 있습니다. 가상 컴퓨터에 대 한 네트워크 인터페이스를 계획 하 고 적절 하 게 구성 하 여 전체 네트워크 조각화 및 분리 목표에 맞게 조정 해야 합니다.

|리소스|링크|
|---|---|
|*네트워크 인터페이스 만들기, 변경 또는 삭제* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*네트워크 인터페이스 IP 주소 지정*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>VNet 통합 PaaS

PaaS는 향상 된 기능 및 가용성을 제공 하 고 관리 오버 헤드를 줄일 수 있지만 적절 하 게 보호 해야 합니다. 제어를 강화 하거나, 네트워크 조각화를 적용 하거나, 응용 프로그램 및 서비스에 대 한 안전한 송신 지점을 제공 하기 위해 많은 PaaS 기능을 가상 네트워크와 통합할 수 있습니다.

시스템 또는 응용 프로그램 아키텍처의 통합 된 일부로 PaaS를 사용 하 여 Microsoft는 PaaS를 가상 네트워크에 배포 하는 여러 메커니즘을 제공 합니다. 배포 방법론은 내부 시스템 및 응용 프로그램과의 연결 및 통합을 제공 하는 동시에 액세스를 제한 하는 데 유용할 수 있습니다. 예를 들면 App Service 환경, SQL 관리 되는 인스턴스 등이 있습니다.

라우팅 및 NSG 컨트롤이 구현 된 가상 네트워크에 PaaS를 배포할 때 Microsoft 서비스의 관리 액세스 및 경로를 포함 하 여 리소스의 특정 통신 요구 사항을 이해 하는 것이 중요 합니다. 통신 트래픽은 이러한 서비스에서 들어오는 요청에 회신할 때 사용 됩니다.

| 리소스  | 링크  |
| --- | --- |
| *Azure 서비스에 대한 가상 네트워크 통합* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *Azure Virtual Network 방법 가이드와 앱 통합* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>공용 IP

공용 IP 주소는 가상 네트워크 외부에서 통신할 때 사용 됩니다. 여기에는 PaaS 리소스 및 인터넷의 다음 홉이 포함 된 경로가 포함 됩니다. 이러한 엔터티는 공용 IP 주소 할당을 신중 하 게 계획 하 고, 진짜 요구 사항이 있는 리소스에만 할당 해야 합니다. 일반적인 디자인 습관으로 공용 IP 주소는 Azure 방화벽, VPN Gateway 또는 네트워크 가상 어플라이언스와 같은 가상 네트워크의 제어 된 송신 점에 할당 되어야 합니다.

|리소스|링크|
|---|---|
|*공용 IP 주소 개요*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*공용 IP 주소 만들기, 변경 또는 삭제* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>유효 경로

유효 경로는 시스템 경로, 서비스 끝점, 경로 테이블, BGP 및 Azure 라우팅 논리 응용 프로그램의 조합에 의해 결정 되는 경로의 결과 집합입니다. 서브넷에서 아웃바운드 트래픽을 보내면 Azure에서 가장 긴 접두사 일치 알고리즘을 사용하여 대상 IP 주소에 기반한 경로를 선택합니다. 여러 경로에 동일한 주소 접두사가 있는 경우 Azure는 다음 우선 순위에 따라 경로 유형을 선택합니다.

1. 사용자 정의 경로
2. BGP 경로
3. 시스템 경로

BGP 경로가 더 구체적인 경우에도 가상 네트워크, 가상 네트워크 피어 링 또는 가상 네트워크 서비스 끝점과 관련 된 트래픽에 대 한 시스템 경로가 기본 경로 인지 확인 하는 것이 중요 합니다.

Azure에서 라우팅 토폴로지의 설계 나 구현에 참여 하는 개인은 Azure에서 트래픽을 라우팅하는 방법 및 필요한 보안 및 가시성으로 시스템의 필요한 기능을 분산 하는 아키텍처를 개발 하는 방법을 이해 해야 합니다. 이로 인해 복잡성이 증가 하 고 더 어렵고 시간이 많이 걸리는 문제를 발견 하는 문제를 해결할 수 있으므로 과도 한 조작을 및 라우팅 동작에 대 한 예외를 방지 하기 위해 환경을 적절 하 게 계획 해야 합니다.

| 리소스 | 링크  |
| --- | --- |
| *유효 경로 보기* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>시스템 경로

[시스템 경로의](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes)경우 가상 네트워크의 디자인 또는 구현에 관여 하는 개인은 기본 시스템 경로와 이러한 경로를 보완 하거나 재정의 하는 데 사용할 수 있는 옵션을 이해 해야 합니다.

### <a name="service-endpoints"></a>서비스 엔드포인트

서브넷에서 [서비스 끝점](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 을 사용 하도록 설정 하면 연결 된 PaaS 리소스에 대 한 직접 통신 경로를 제공 합니다. 이렇게 하면 사용 가능한 통신 경로를 해당 서비스로 제한 하 여 향상 된 성능 및 보안을 제공할 수 있습니다. 서비스 끝점을 사용 하면 응용 프로그램 또는 시스템에 필요한 특정 인스턴스가 아닌 PaaS 서비스의 모든 인스턴스에 대 한 액세스를 허용 하므로, 서비스 끝점을 사용 하 여 잠재적인 데이터 반출 경로를 도입할 수 있습니다.

인 단체 엔터티는 잘못 사용 되는 경로의 확률 및 결과를 포함 하 여 PaaS 리소스에 대 한 직접 액세스를 제공 하는 것과 관련 된 위험을 평가 해야 합니다.

서비스 끝점과 관련 된 잠재적 위험을 줄이기 위해 가능한 경우 서비스 끝점 정책을 구현 하거나 Azure 방화벽 또는 NVA 서브넷에서 서비스 끝점을 사용 하도록 설정 하 고 특정 서브넷에서 트래픽을 라우팅하는 것을 고려 합니다. 필터링, 모니터링 또는 검사를 적용할 수 있습니다.

|리소스|링크|
|---|---|
|*자습서: Azure Portal를 사용 하 여 가상 네트워크 서비스 끝점으로 PaaS 리소스에 대 한 네트워크 액세스 제한* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>경로 테이블

경로 테이블은 Azure 내에서 네트워크 트래픽을 제어 하기 위해 관리자가 구성한 메커니즘을 제공 합니다. 경로 테이블은 Azure 방화벽 또는 NVA를 통해 트래픽을 전달 하거나, 외부 리소스에 직접 연결 하거나, Azure 시스템 경로를 재정의 하는 데 utilised 수 있습니다. 또한 경로 테이블을 사용 하 여 가상 네트워크 게이트웨이를 통해 학습 된 네트워크를 가상 네트워크 게이트웨이 경로 전파를 사용 하지 않도록 설정 하 여 서브넷의 리소스에 사용할 수 없도록 할 수 있습니다.

|리소스|링크|
|---|---|
|*라우팅 개념-사용자 지정 경로* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*자습서: 네트워크 트래픽 라우팅* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>BGP(Border gateway protocol)

BGP는 가상 네트워크 게이트웨이에서 utilised 하 여 라우팅 정보를 온-프레미스 또는 다른 외부 네트워크와 동적으로 교환할 수 있습니다. BGP는 Express 경로 개인 피어 링을 통해 Express 경로 가상 네트워크 게이트웨이를 통해 구성 된 경우 및 Azure VPN Gateway에서 사용 하도록 설정 된 경우 가상 네트워크에 적용 됩니다.

Azure의 가상 네트워크 및 가상 네트워크 게이트웨이 디자인 또는 구현과 관련 된 개인은 Azure에서 BGP에 사용할 수 있는 동작 및 구성 옵션을 이해 하는 데 시간이 걸립니다.

|리소스|링크|
|---|---|
|*라우팅 개념: BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*Express 경로 라우팅 요구 사항* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*Azure VPN Gateway를 사용 하는 BGP 정보* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*자습서: Express 경로 Microsoft 피어 링을 통해 사이트 간 VPN 구성* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>다음 홉 유형

### <a name="virtual-network"></a>Virtual Network

Virtual Network의 다음 홉이 있는 경로는 시스템 경로로 자동 추가 되지만 사용자 정의 경로에 추가 하 여 시스템 경로가 재정의 된 인스턴스에서 가상 네트워크로 트래픽을 다시 보낼 수도 있습니다.

### <a name="vnet-peering"></a>VNet 피어링

VNet 피어 링은 서로 다른 두 가상 네트워크 간의 통신을 가능 하 게 합니다. VNet 피어 링 구성은 각 가상 네트워크에서 사용 하도록 설정 해야 하지만 가상 네트워크는 동일한 지역, 구독에 있거나 동일한 Azure Active Directory (Azure AD) 테 넌 트에 연결 되어 있지 않아도 됩니다.

VNet 피어 링을 구성 하는 경우에는 VNet 피어 링의 디자인 또는 구현에 관련 된 개인이 네 개의 연결 된 구성 매개 변수와 피어의 각 측면에 적용 되는 방식을 이해 하는 것이 중요 합니다.

1. **가상 네트워크 액세스 허용**: 두 가상 네트워크 간의 통신을 사용 하도록 설정 하려면 **사용** (기본값)을 선택 합니다. 가상 네트워크 간 통신을 사용하도록 설정하면 어느 쪽 가상 네트워크에든 연결된 리소스가 같은 가상 네트워크에 연결된 것처럼 같은 대역폭 및 대기 시간으로 서로 통신할 수 있습니다.
2. **전달된 트래픽 허용:** 이 확인란을 선택 하면 가상 네트워크에서 발생 하지 않은 네트워크 트래픽으로 *전달* 되는 트래픽이 피어 링을 통해이 가상 네트워크로 이동 하는 것을 허용 합니다. 이 설정은 허브 및 스포크 네트워크 토폴로지를 구현 하는 기본 사항입니다.
3. **게이트웨이 전송 허용:** 피어 링 가상 네트워크가이 가상 네트워크에 연결 된 가상 네트워크 게이트웨이를 제품 수 있도록 허용 하려면이 확인란을 선택 합니다. 가상 네트워크 게이트웨이 리소스를 사용 하 여 가상 네트워크에서 *게이트웨이 전송 허용* 을 사용 하도록 설정 하 고, 다른 가상 네트워크에서 *사용 하는 원격 게이트웨이* 를 사용 하는 경우에만 적용 됩니다.
4. **원격 게이트웨이 사용:** 이 확인란을 선택 하 여이 가상 네트워크의 트래픽이 피어 링 되는 가상 네트워크에 연결 된 가상 네트워크 게이트웨이를 통해 흐를 수 있도록 허용 합니다. 가상 네트워크 게이트웨이를 사용 하지 않고 가상 네트워크에서 *원격 게이트웨이 사용* 을 사용 하도록 설정 하 고 다른 가상 네트워크에서 *게이트웨이 전송 허용* 옵션을 사용 하는 경우에만 적용 됩니다.

|리소스|링크|
|---|---|
| 개념: 가상 네트워크 피어링                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| 가상 네트워크 피어링 만들기, 변경 또는 삭제 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>가상 네트워크 게이트웨이

가상 네트워크 게이트웨이는 가상 네트워크를 온-프레미스 환경, 파트너 환경 및 기타 클라우드 배포와 같은 외부 네트워크와 통합 하기 위한 메커니즘을 제공 합니다. 두 가지 유형의 가상 네트워크 게이트웨이는 Express 경로 및 VPN입니다.

#### <a name="expressroute-gateway"></a>Express 경로 게이트웨이

Express 경로 게이트웨이는 가상 네트워크에서 온-프레미스 환경으로의 송신 지점을 제공 하며 보안, 가용성, 재무 및 성능 요구 사항을 충족 하기 위해 배포 해야 합니다. Express 경로 게이트웨이는 정의 된 네트워크 대역폭을 제공 하 고 배포 후 사용 비용을 발생 시킵니다. 가상 네트워크는 Express 경로 게이트웨이를 하나만 포함할 수 있지만, 여러 Express 경로 회로에 연결 될 수 있으며 VNet 피어 링을 통해 여러 가상 네트워크에서 활용 하 여 대역폭 및 연결을 공유할 수 있습니다. Express 경로 게이트웨이를 사용 하 여 온-프레미스 환경과 가상 네트워크 간의 라우팅을 구성 하 여 알려진 제어 된 네트워크 송신 지점의 종단 간 연결을 보장 해야 합니다. Express 경로 개인 피어 링을 통해 Express 경로 게이트웨이를 사용 하는 개인 엔터티는 NVA (네트워크 가상 어플라이언스)를 배포 하 여 ACSC 소비자 지침을 준수 하기 위해 온-프레미스 환경에 대 한 VPN 연결을 설정 해야 합니다.

Express 경로 게이트웨이는 BGP를 통해 교환 되는 주소 범위, 커뮤니티 및 기타 특정 구성 항목에 대 한 제한이 있습니다.

| 리소스  | 링크  |
|---|---|
| Express 경로 게이트웨이 개요 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| ExpressRoute에 대한 가상 네트워크 게이트웨이 구성 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>VPN 게이트웨이

Azure VPN Gateway는 보안 사이트 간 연결을 위해 가상 네트워크에서 외부 네트워크로의 송신 네트워크 지점을 제공 합니다. VPN gateway는 정의 된 네트워크 대역폭을 제공 하 고 배포 후 사용 비용을 발생 시킵니다. Utilising VPN Gateway은 ACSC 소비자 지침에 따라 구성 되었는지 확인 해야 합니다. 가상 네트워크에는 VPN Gateway 하나만 있을 수 있지만이는 여러 터널로 구성할 수 있으며 VNet 피어 링을 통해 여러 가상 네트워크에서 활용 하 여 여러 가상 네트워크에서 대역폭과 연결을 공유할 수 있습니다. VPN 게이트웨이는 인터넷을 통해 설정 하거나 Microsoft 피어 링을 통해 Express 경로를 통해 설정할 수 있습니다.

| 리소스  | 링크 |
| --- | --- |
| VPN Gateway 개요| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| VPN Gateway 계획 및 설계 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| Azure 오스트레일리아의 azure VPN Gateway | [Azure 오스트레일리아의 azure VPN Gateway](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>가상 어플라이언스의 다음 홉

가상 어플라이언스의 다음 홉은 가상 네트워크에 적용 되는 Azure 네트워킹 및 라우팅 토폴로지 외부에서 네트워크 트래픽을 처리 하는 기능을 제공 합니다. 가상 어플라이언스는 보안 규칙을 적용 하 고, 주소를 변환 하 고, Vpn, 프록시 트래픽 또는 기타 다양 한 기능을 설정할 수 있습니다. 가상 어플라이언스의 다음 홉은 경로 테이블에서 UDRs를 통해 적용 되며 트래픽을 Azure 방화벽, 개별 NVA 또는 여러 Nva에 걸쳐 가용성을 제공 하는 Azure Load Balancer에 전달 하는 데 사용할 수 있습니다. 라우팅에 가상 어플라이언스를 사용 하려면 연결 된 네트워크 인터페이스에서 IP 전달을 사용 하도록 설정 해야 합니다.

| 리소스  | 링크 |
| --- | ---|
| 라우팅 개념: 사용자 지정 경로 | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| IP 전달 사용 또는 사용 안 함 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>VirtualNetworkServiceEndpoint의 다음 홉

다음 홉 유형이 VirtualNetworkServiceEndpoint 인 경로는 서브넷에 서비스 끝점이 구성 되어 있고 경로 테이블을 통해 수동으로 구성할 수 없는 경우에만 추가 됩니다.

### <a name="next-hop-of-internet"></a>인터넷의 다음 홉

다음 홉 인터넷은 인터넷 뿐만 아니라 Azure 지역의 PaaS 및 Azure 서비스를 포함 하는 공용 IP 주소를 사용 하는 리소스에 연결 하는 데 사용 됩니다. 다음 홉 인터넷은 모든 네트워크를 포괄 하는 기본 경로 (0.0.0.0/0)가 필요 하지 않지만 특정 공용 서비스에 대 한 라우팅 경로를 사용 하도록 설정 하는 데 사용할 수 있습니다. 인터넷의 다음 홉을 사용 하 여 공인 서비스에 경로를 추가 하 고 Microsoft 관리 주소와 같은 시스템 기능에 필요한 기능을 사용 해야 합니다.

인터넷의 다음 홉을 사용 하 여 추가할 수 있는 서비스의 예는 다음과 같습니다.

1. Windows 정품 인증에 대 한 키 관리 서비스
2. App Service Environment 관리

|리소스|링크|
|---|---|
| Azure에서 아웃바운드 연결 | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| Azure 사용자 지정 경로를 사용 하 여 KMS 정품 인증을 사용 하도록 설정 | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| App Service Environment 잠금  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>없음의 다음 홉

None의 다음 홉은 특정 네트워크에 대 한 통신을 방지 하는 데 사용할 수 있습니다. 사용 가능한 네트워크 경로 트래버스에서 트래픽이 허용 되거나 거부 되는지 여부를 제어 하는 NSG와 달리, 다음 홉 없음을 사용 하면 네트워크 경로가 완전히 제거 됩니다. 이는 의도 하지 않은 결과가 발생할 수 있으며 시스템 문제 해결을 복잡 하 고 시간이 많이 걸릴 수 있으므로, 다음 홉이 없음 인 경로를 만들 때 특히 0.0.0.0/0의 기본 경로에 적용 하는 경우 주의 해야 합니다.

## <a name="security"></a>보안

IaaS 및 PaaS 기능에서 네트워크 구분 및 분리 컨트롤을 구현 하는 것은 기능을 보호 하 고와 통신할 시스템에서 제어 된 통신 경로를 구현 하 여 수행 됩니다. 기능.

Azure에서 솔루션을 설계 하 고 구축 하는 과정은 전체 Azure에서 네트워크 리소스를 이해 하 고 제어 하 고 모니터링 하기 위한 논리적 아키텍처를 만드는 프로세스입니다. 이 논리적 아키텍처는 Azure 플랫폼 내에서 정의 되는 소프트웨어로, 기존 네트워크 환경에서 구현 되는 실제 네트워크 토폴로지의 자리를 차지 합니다.

생성 되는 논리적 아키텍처는 사용 편의성에 필요한 기능을 제공 해야 하지만 보안과 무결성에 필요한 가시성과 제어를 제공 해야 합니다.

이러한 결과를 달성 하려면 필요한 네트워크 조각화 및 분리 도구를 구현 하는 것이 아니라 네트워크 토폴로지와 이러한 도구의 구현을 보호 하 고 적용 해야 합니다.

### <a name="network-security-groups-nsgs"></a>NSG(네트워크 보안 그룹)

NSGs는 서브넷 또는 특정 네트워크 인터페이스에 허용 되는 인바운드 및 아웃 바운드 트래픽을 지정 하는 데 사용 됩니다. NSGs를 구성 하는 경우에는 특정 허용 문과 일치 하지 않는 모든 트래픽을 거부 하도록 기본 규칙을 구성 하는 데 필요한 트래픽을 허용 하도록 규칙을 구성 하는 허용 목록 접근 방식을 사용 해야 합니다. NSGs를 계획 하 고 구성할 때 필요한 모든 인바운드 및 아웃 바운드 트래픽이 적절 하 게 캡처 되도록 주의 해야 합니다. 여기에는 가상 네트워크 및 온-프레미스 환경 내에서 utilised 모든 개인 IP 주소 범위를 식별 하 고 이해 하는 것과 Azure Load Balancer 및 PaaS 관리 요구 사항 등의 특정 Microsoft 서비스가 포함 됩니다. NSGs의 디자인 및 구현에 참여 하는 개인은 세분화 된 서비스 및 응용 프로그램별 보안 규칙을 만들기 위해 서비스 태그 및 응용 프로그램 보안 그룹을 사용 하는 것도 이해 해야 합니다.

NSG에 대 한 기본 구성은 가상 네트워크 내의 모든 주소와 모든 공용 IP 주소에 대 한 아웃 바운드 트래픽을 허용 한다는 점에 유의 해야 합니다.

|리소스|링크|
|---|---|
|네트워크 보안 개요 | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|네트워크 보안 그룹을 만들기, 변경 또는 삭제 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure 방화벽을 utilised 하 여 허브 및 스포크 네트워크 토폴로지를 빌드하고 centralised 네트워크 보안 제어를 적용할 수 있습니다. Azure 방화벽을 사용 하 여 시스템 기능에 필요한 IP 주소, 프로토콜, 포트 및 Fqdn만 공인 하는 허용 목록 방법을 구현 하 여 송신 트래픽에 대 한 ISM의 필수 요구 사항을 충족할 수 있습니다. Azure 방화벽을 통해 제공 되는 보안 기능이 요구 사항에 충분 한지 여부를 확인 하는 데는 주의 해야 합니다. Azure 방화벽에서 제공 하는 것 이상의 추가 보안 기능이 필요한 시나리오의 경우에는 Nva 구현을 고려해 야 합니다.

|리소스|링크|
|---|---|
|*Azure 방화벽 설명서* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*자습서: Azure PowerShell를 사용 하 여 하이브리드 네트워크에서 Azure 방화벽 배포 및 구성* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>Nva (네트워크 가상 어플라이언스)

Nva는 허브 및 스포크 네트워크 토폴로지를 구축 하는 데 사용 하거나, 향상 된 네트워크 기능을 제공 하거나, 온-프레미스 네트워크 서비스를 사용 하 여 친숙 하 고 일관 된 지원 및 관리를 위해 Azure 네트워크 메커니즘 대신 사용할 수 있습니다. Nva은와 같은 특정 보안 요구 사항을 충족 하기 위해 배포할 수 있습니다. 네트워크 트래픽, HTTPS 암호 해독, 콘텐츠 검사, 필터링 또는 기타 보안 기능과 관련 된 id 인식을 요구 하는 시나리오입니다. Nva는 항상 사용 가능한 구성으로 배포 해야 하 고 Nva의 디자인 또는 구현에 참여 하는 개인은 Azure에서 배포에 대 한 지침을 제공 하는 적절 한 공급 업체 설명서를 참조 해야 합니다.

|리소스|링크|
|---|---|
|*항상 사용 가능한 네트워크 가상 어플라이언스 배포* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>서비스 끝점 정책 (미리 보기)

서비스의 가용성 및 데이터 exfiltration의 가능성 및 영향에 대 한 보안 위험 평가를 기반으로 서비스 끝점 정책을 구성 합니다. 서비스 끝점 정책은 관련 된 위험 프로필을 기반으로 하는 다른 서비스에 대 한 대/소문자를 사용 하 여 Azure Storage 하 고 관리 해야 합니다.

| 리소스 | 링크  |
| --- | --- |
| *서비스 끝점 정책 개요* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *Azure Portal를 사용 하 여 서비스 끝점 정책을 만들거나, 변경 하거나, 삭제 합니다.* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy는 Azure 환경의 논리적 아키텍처에 대 한 무결성을 적용 하 고 유지 관리 하기 위한 핵심 구성 요소입니다. Azure 서비스를 통해 다양 한 서비스와 송신 네트워크 트래픽 경로를 사용할 수 있습니다. 해당 환경 내에 존재 하는 리소스와 사용 가능한 네트워크 송신 지점의 리소스를 인식 하는 것이 중요 합니다. 무단 네트워크 송신 지점이 Azure 환경에서 생성 되지 않도록 하기 위해, 해당 리소스의 구성 및 배포할 수 있는 리소스 종류를 제어 하는 데 Azure Policy를 사용 해야 합니다. 실제 예에는 리소스를 공인로 제한 하 고 사용 하도록 승인 하 고 서브넷에 NSGs를 추가 해야 하는 작업이 포함 됩니다.

|리소스 | 링크|
|---|---|
|*Azure Policy 개요* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*허용 되는 리소스 종류 샘플 정책* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*서브넷 샘플 정책에 대 한 NSG 강제 적용*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>PaaS 송신 기능

PaaS 기능은 향상 된 기능 및 간소화 된 관리에 대 한 기회를 제공 하지만 네트워크 구분 및 분리 요구 사항에 대 한 복잡성을 소개 합니다. PaaS 기능은 일반적으로 공용 IP 주소를 사용 하 여 구성 되며 인터넷에서 액세스할 수 있습니다.  시스템 및 솔루션 내에서 PaaS 기능을 사용 하는 경우에는 구성 요소 간의 통신 흐름을 식별 하 고 네트워크 보안 규칙을 만들어 해당 통신만 허용 하도록 주의 해야 합니다. 보안에 대 한 심층적인 심층 방어 방식을 방식의 일부로, 암호화, 인증 및 적절 한 액세스 제어 및 권한으로 PaaS 기능을 구성 해야 합니다.  

### <a name="public-ip-for-paas"></a>PaaS에 대 한 공용 IP

PaaS 기능에 대 한 공용 IP 주소는 서비스가 호스트 되거나 배포 되는 지역에 따라 할당 됩니다. Azure virtual networks, PaaS 및 Express 경로를 포함 하는 네트워크 구분 및 분리 적절 한 네트워크 보안 규칙 및 라우팅 토폴로지를 빌드하려면 공용 IP 주소 할당 및 지역을 이해 해야 합니다. 인터넷 연결. Azure는 각 Azure 지역에 할당 된 풀에서 IP 주소를 할당 합니다. Microsoft는 각 지역에서 사용 되는 주소를 다운로드할 수 있도록 하며, 이러한 주소는 정기적이 고 제어 된 방식으로 업데이트 됩니다. 또한 각 지역에서 사용할 수 있는 서비스는 새 서비스를 출시 하거나 서비스를 더욱 광범위 하 게 배포할 때 자주 변경 됩니다. 이 자료 엔터티는 이러한 자료를 정기적으로 검토 하 고 자동화를 사용 하 여 필요에 따라 시스템을 유지 관리할 수 있습니다. Microsoft 지원에 문의 하 여 각 지역에서 호스트 되는 일부 서비스에 대 한 특정 IP 주소를 얻을 수 있습니다.

| 리소스 | 링크 |
| --- | --- |
| *Microsoft Azure 데이터 센터 IP 범위*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *지역별 Azure 서비스*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, 오스트레일리아-중부, 오스트레일리아-중부-2, 오스트레일리아-동부, 오스트레일리아-동남 & products = 모두](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Azure App Service의 인바운드 및 아웃 바운드 IP 주소* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>다음 단계

전반적인 아키텍처와 디자인을 [IaaS 및 PaaS 웹 응용 프로그램에 대해 게시 된 보호 된 청사진](https://aka.ms/au-protected)과 비교해 보세요.
