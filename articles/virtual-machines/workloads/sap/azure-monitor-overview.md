---
title: SAP 솔루션 개요 및 아키텍처에 대 한 Azure Monitor | Microsoft Docs
description: 이 문서에서는 SAP 솔루션에 대 한 Azure monitor에 대 한 질문과 대답을 제공 합니다.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 8d97c713dfec574a8ef7f3f0dde4701ddea0e98b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89669007"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>SAP 솔루션에 대 한 Azure monitor (미리 보기)

## <a name="overview"></a>개요

SAP 솔루션에 대 한 Azure Monitor는 Azure에서 SAP 지형을 실행 하는 고객을 위한 Azure 기본 모니터링 제품입니다. 이 제품은 [azure Virtual Machines의 sap](./hana-get-started.md) 와 [Azure의 많은 인스턴스에서](./hana-overview-architecture.md)작동 합니다.
SAP 솔루션에 대 한 Azure Monitor를 통해 고객은 Azure 인프라 및 데이터베이스에서 원격 분석 데이터를 하나의 중앙 위치에 수집 하 고 신속 하 게 문제를 해결 하기 위해 원격 분석 데이터의 상관 관계를

SAP 솔루션에 대 한 Azure Monitor Azure Marketplace을 통해 제공 됩니다. 간단 하 고 직관적인 설정 환경을 제공 하며 SAP 솔루션에 대 한 Azure Monitor에 대 한 리소스를 배포 하기 위해 몇 번의 클릭만 하면 됩니다 ( **sap 모니터 리소스**라고 함).

고객은 해당 구성 요소에 해당 하는 **공급자** 를 추가 하 여 Azure Virtual Machines, 고가용성 클러스터, SAP HANA 데이터베이스 등의 SAP 환경에서 다양 한 구성 요소를 모니터링할 수 있습니다.

지원 되는 인프라:

- Azure Virtual Machine
- Azure Large Instance

지원 되는 데이터베이스:
- SAP HANA 데이터베이스
- Microsoft SQL server

SAP 솔루션에 대 한 Azure Monitor는 Log Analytics 및 [통합 문서](../../../azure-monitor/platform/workbooks-overview.md) 와 같은 기존 [Azure Monitor](../../../azure-monitor/overview.md) 기능의 기능을 활용 하 여 추가 모니터링 기능을 제공 합니다. 고객은 Azure Log Analytics 작업 영역을 사용 [하 여 Azure Monitor](../../../azure-monitor/learn/tutorial-response.md) 에서 제공 하는 기본 통합 문서를 편집 하 고, 사용자 지정 [쿼리](../../../azure-monitor/log-query/get-started-portal.md) 를 작성 하 고, [유연한 보존 기간](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 을 활용 하 고, 모니터링 데이터를 티켓 시스템과 연결 하 여 [사용자 지정 시각화](../../../azure-monitor/platform/workbooks-overview.md#getting-started) 를 만들 수 있습니다.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>SAP 솔루션에 어떤 데이터를 수집 Azure Monitor?

SAP 솔루션에 대 한 Azure Monitor의 데이터 수집은 고객에 의해 구성 된 공급자에 따라 달라 집니다. 공개 미리 보기 중에 다음 데이터가 수집 됩니다.

고가용성 Pacemaker 클러스터 원격 분석:
- 노드, 리소스 및 SBD 장치 상태
- Pacemaker location 제약 조건
- 쿼럼 투표 및 링 상태
- [Others](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA 원격 분석:
- CPU, 메모리, 디스크 및 네트워크 사용률
- HSR (HANA 시스템 복제)
- HANA 백업
- HANA 호스트 상태
- 인덱스 서버 및 이름 서버 역할

Microsoft SQL server 원격 분석:
- CPU, 메모리, 디스크 사용률
- 호스트 이름, SQL 인스턴스 이름, SAP 시스템 ID
- 시간에 따른 일괄 처리 요청, 컴파일 및 페이지 수명 예상
- 시간이 지남에 따라 상위 10 개의 가장 비싼 SQL 문
- SAP 시스템의 상위 12 개 가장 큰 테이블
- SQL Server 오류 로그에 기록 된 문제
- 시간에 따른 차단 프로세스 및 SQL 대기 통계

## <a name="data-sharing-with-microsoft"></a>Microsoft와 데이터 공유

SAP 솔루션에 대 한 Azure Monitor는 시스템 메타 데이터를 수집 하 여 Azure 고객의 SAP에 대해 향상 된 지원을 제공 합니다. PII/EUII가 수집 되지 않습니다.
고객은 드롭다운에서 *공유* 를 선택 하 여 SAP 솔루션 리소스에 대 한 Azure Monitor를 만들 때 Microsoft와의 데이터 공유를 사용 하도록 설정할 수 있습니다.
Microsoft 지원 및 엔지니어링 팀에서 고객 환경에 대 한 자세한 정보를 제공 하 고 Azure 고객의 중요 업무용 SAP에 대해 향상 된 지원을 제공 하므로, 고객은 데이터 공유를 사용 하는 것이 좋습니다.

## <a name="architecture-overview"></a>아키텍처 개요

개략적인 수준에서 다음 다이어그램은 SAP 솔루션에 대 한 Azure Monitor SAP HANA 데이터베이스에서 원격 분석을 수집 하는 방법을 설명 합니다. 아키텍처는 SAP HANA Azure Virtual Machines 또는 Azure Large Instances에 배포 되는지 여부와 무관 합니다.

![SAP 솔루션 아키텍처에 대 한 Azure Monitor](./media/azure-monitor-sap/azure-monitor-architecture.png)

아키텍처의 주요 구성 요소는 다음과 같습니다.
- Azure Portal – 고객의 시작 지점입니다. 고객은 Azure Portal 내 marketplace로 이동 하 여 SAP 솔루션에 대 한 Azure Monitor를 검색할 수 있습니다.
- SAP 솔루션 리소스에 대 한 Azure Monitor – 고객이 모니터링 원격 분석을 볼 수 있는 방문 장소
- 관리 되는 리소스 그룹 – SAP Solutions 리소스 배포를 위한 Azure Monitor의 일부로 자동으로 배포 됩니다. 관리 되는 리소스 그룹 내에 배포 된 리소스는 원격 분석 컬렉션에서 도움이 됩니다. 배포 된 주요 리소스 및 용도는 다음과 같습니다.
   - Azure 가상 머신: *수집기 VM*이 라고도 합니다. Standard_B2ms VM입니다. 이 VM의 주요 목적은 *모니터링 페이로드*를 호스트 하는 것입니다. 페이로드 모니터링은 원본 시스템에서 원격 분석을 수집 하 고 수집 된 데이터를 모니터링 프레임 워크로 전송 하는 논리를 나타냅니다. 위의 다이어그램에서 모니터링 페이로드는 SQL 포트를 통해 SAP HANA 데이터베이스에 연결 하는 논리를 포함 합니다.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md):이 리소스는 SAP HANA 데이터베이스 자격 증명을 안전 하 게 보관 하 고 [공급자](./azure-monitor-providers.md)에 대 한 정보를 저장 하기 위해 배포 됩니다.
   - Log Analytics 작업 영역: 원격 분석 데이터가 있는 대상입니다.
      - 시각화는 [Azure 통합 문서](../../../azure-monitor/platform/workbooks-overview.md)를 사용 하 여 Log Analytics에서 원격 분석을 기반으로 빌드됩니다. 고객은 시각화를 사용자 지정할 수 있습니다. 고객은 통합 문서 내에서 통합 문서 또는 특정 시각화를 Azure 대시보드에 고정 하 여 최소 세분성이 30 분인 autorefresh 기능을 사용할 수도 있습니다.
      - 고객은 배포 시이 옵션을 선택 하 여 SAP monitor 리소스와 동일한 구독 내에서 기존 작업 영역을 사용할 수 있습니다.
      - 고객은 KQL (Kusto query language)를 사용 하 여 Log Analytics 작업 영역 내의 원시 테이블에 대해 [쿼리](../../../azure-monitor/log-query/log-query-overview.md) 를 실행할 수 있습니다. *사용자 지정 로그*를 확인 합니다.

> [!Note]
> 고객은 관리 되는 리소스 그룹에 배포 된 VM의 패치 및 유지 관리를 담당 합니다.

> [!Tip]
> 고객은 원격 분석 컬렉션에 대해 기존 Log Analytics 작업 영역을 사용 하도록 선택할 수 있습니다 .이는 SAP 솔루션에 대 한 Azure Monitor 리소스와 동일한 Azure 구독 내에 배포 됩니다.

### <a name="architecture-highlights"></a>아키텍처 하이라이트

아키텍처의 주요 주요 사항은 다음과 같습니다.
 - **다중 인스턴스** -고객은 sap 솔루션에 대 한 단일 Azure Monitor 리소스를 사용 하 여 VNET 내의 여러 sap sid에서 지정 된 구성 요소 유형 (예: HANA DB, HA 클러스터, Microsoft SQL server)의 여러 인스턴스에 대 한 모니터를 만들 수 있습니다.
 - **다중 공급자** -위의 아키텍처 다이어그램은 SAP HANA 공급자를 예로 보여 줍니다. 마찬가지로 고객은 해당 구성 요소 (예: HANA DB, HA 클러스터, Microsoft SQL server)에 대 한 추가 공급자를 구성 하 여 해당 구성 요소에서 데이터를 수집할 수 있습니다.
 - **오픈 소스** -SAP 솔루션에 대 한 Azure Monitor의 소스 코드는 [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions)에서 사용할 수 있습니다. 고객은 공급자 코드를 참조 하 여 제품에 대 한 자세한 정보를 확인 하 고 의견을 올리거나 공유할 수 있습니다.
 - 원격 분석 데이터를 수집 하는 **확장 가능한 쿼리 프레임 워크** SQL 쿼리는 [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json)으로 작성 됩니다. 더 많은 원격 분석 데이터를 수집 하는 추가 SQL 쿼리를 쉽게 추가할 수 있습니다. 고객은이 문서의 끝에 있는 링크를 통해 피드백을 유지 하거나 계정 팀에 연락 하 여 SAP 솔루션에 대 한 Azure Monitor에 추가 될 특정 원격 분석 데이터를 요청할 수 있습니다.

## <a name="pricing"></a>가격 책정
SAP 솔루션에 대 한 Azure Monitor은 무료 제품 (라이선스 요금 없음)입니다. 고객은 관리 되는 리소스 그룹의 기본 구성 요소에 대 한 비용을 지불할 책임이 있습니다.

## <a name="next-steps"></a>다음 단계

공급자에 대해 알아보고 SAP Solutions 리소스에 대 한 첫 번째 Azure Monitor를 만듭니다.
 - [공급자](./azure-monitor-providers.md) 에 대해 자세히 알아보기
 - [Azure PowerShell을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포](azure-monitor-sap-quickstart-powershell.md)
 - SAP 솔루션의 Azure Monitor에 대 한 질문이 있나요? [FAQ](./azure-monitor-faq.md) 섹션 확인
