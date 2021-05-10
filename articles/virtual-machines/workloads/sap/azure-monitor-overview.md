---
title: SAP 솔루션을 위한 Azure Monitor 개요 및 아키텍처 | Microsoft Docs
description: 본 문서에서는 SAP 솔루션을 위한 Azure Monitor에 대해 자주 묻는 질문에 대한 답변을 제공합니다.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: a88ad3930e114bdf9f3c3c340f92f164215d59c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671995"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>SAP 솔루션을 위한 Azure Monitor(미리 보기)

## <a name="overview"></a>개요

SAP 솔루션을 위한 Azure Monitor는 Azure의 SAP 환경을 실행하는 고객을 위한 Azure 네이티브 모니터링 제품입니다. 본 제품은 [Azure 가상 머신의 SAP](./hana-get-started.md)와 [Azure의 SAP(대규모 인스턴스)](./hana-overview-architecture.md) 모두에서 작동합니다.
고객들은 SAP 솔루션을 위한 Azure Monitor를 사용하여 단일 중앙 위치에서 Azure 인프라 및 데이터베이스의 원격 분석 데이터를 수집하고, 빠른 문제 해결을 위해 원격 분석 데이터의 상관 관계를 시각적으로 지정할 수 있습니다.

SAP 솔루션을 위한 Azure Monitor는 Azure Marketplace을 통해 제공됩니다. 간단하면서도 직관적인 설정 환경을 제공하여 몇 번의 클릭 만으로 **SAP 모니터 리소스** 로 알려진 SAP 솔루션을 위한 Azure Monitor용 리소스를 배포합니다.

고객은 해당 구성 요소에 대해 관련 **공급자** 를 추가하여 Azure 가상 머신, 고가용성 클러스터, SAP HANA 데이터베이스 등의 SAP 환경을 이루는 다양한 구성 요소를 모니터링할 수 있습니다.

지원되는 인프라:

- Azure Virtual Machine
- Azure 대규모 인스턴스

지원되는 데이터베이스:
- SAP HANA 데이터베이스
- Microsoft SQL Server:

SAP 솔루션을 위한 Azure Monitor는 추가적인 모니터링 기능을 제공하기 위해 Log Analytics와 [통합 문서](../../../azure-monitor/visualize/workbooks-overview.md) 같은 기존 [Azure Monitor](../../../azure-monitor/overview.md) 기능을 활용합니다. 고객은 SAP 솔루션을 위한 Azure Monitor가 제공하는 기본 통합 문서를 편집하여 [사용자 지정 시각화](../../../azure-monitor/visualize/workbooks-overview.md#getting-started)를 만들 수 있고 Azure Log Analytics 작업 영역을 통해 [사용자 지정 쿼리](../../../azure-monitor/logs/log-analytics-tutorial.md)를 작성하고 [사용자 지정 경고](../../../azure-monitor/alerts/tutorial-response.md)를 생성할 수 있으며 [유연한 보존 기간](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)을 활용하고 모니터링 데이터를 티켓팅 시스템에 연결할 수 있습니다.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>SAP 솔루션을 위한 Azure Monitor에서 어떤 데이터를 수집하나요?

SAP 솔루션을 위한 Azure Monitor에서 수집하는 데이터는 고객이 구성한 공급자에 따라 달라집니다. 공개 미리 보기 중에 다음과 같은 데이터가 수집됩니다.

고가용성 Pacemaker 클러스터 원격 분석:
- 노드, 리소스 및 SBD 디바이스 상태
- Pacemaker 위치 제약 조건
- 쿼럼 투표 및 링 상태
- [기타](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA 원격 분석:
- CPU, 메모리, 디스크 및 네트워크 사용률
- HSR(HANA 시스템 복제)
- HANA 백업
- HANA 호스트 상태
- 인덱스 서버 및 이름 서버 역할

Microsoft SQL Server 원격 분석:
- CPU, 메모리, 디스크 사용률
- 호스트 이름, SQL 인스턴스 이름, SAP 시스템 ID
- 시간에 따른 일괄 처리 요청, 컴파일 및 페이지 예상 수명
- 시간에 따라 가장 많은 비용이 드는 상위 10개 SQL 문
- SAP 시스템에서 가장 큰 상위 12개 테이블
- SQL Server 오류 로그에 기록된 문제
- 시간에 따른 차단 프로세스와 SQL 대기 통계

운영 체제 원격 분석(Linux) 
- CPU 사용률, 포크의 수, 실행 중인 프로세스 및 차단된 프로세스 
- 사용되거나 캐시되거나 버퍼링한 메모리 사용률 및 배포 
- 스왑 사용률, 페이징 및 교환 비율 
- 파일 시스템 사용률, 블록 디바이스당 읽고 쓴 바이트 수 
- 블록 디바이스당 읽기/쓰기 대기 시간 
- 지속 I/O 수, 영구 메모리 읽기/쓰기 바이트 
- 네트워크 패킷 입출력, 네트워크 바이트 입출력 

## <a name="data-sharing-with-microsoft"></a>Microsoft와 데이터 공유

SAP 솔루션을 위한 Azure Monitor는 시스템 메타데이터를 수집하여 Azure의 SAP 고객에게 향상된 지원을 제공합니다. PII/EUII는 수집되지 않습니다.
고객은 드롭다운에서 *공유* 를 선택하여 SAP 솔루션을 위한 Azure Monitor 리소스를 만들 때 Microsoft와 데이터 공유를 사용하게 설정할 수 있습니다.
데이터 공유를 통해 Microsoft 지원 및 엔지니어링 팀이 고객 환경에 대한 추가 정보를 얻어 중요 업무용 Azure의 SAP 고객에게 향상된 지원을 제공할 수 있으므로 고객은 데이터 공유를 사용하는 것이 좋습니다.

## <a name="architecture-overview"></a>아키텍처 개요

다음 다이어그램은 SAP 솔루션을 위한 Azure Monitor가 SAP HANA 데이터베이스에서 원격 분석 데이터를 수집하는 방법을 개략적으로 보여 줍니다. 아키텍처는 SAP HANA를 Azure 가상 머신에 배포하는지 Azure 대규모 인스턴스에 배포하는지 여부와 무관합니다.

![SAP 솔루션을 위한 Azure Monitor 아키텍처](./media/azure-monitor-sap/azure-monitor-architecture.png)

해당 아키텍처의 중요 구성 요소는 다음과 같습니다.
- Azure Portal – 고객의 시작 지점입니다. 고객은 Azure Portal 내에서 Marketplace로 이동하여 SAP 솔루션을 위한 Azure Monitor를 검색할 수 있습니다.
- SAP 솔루션을 위한 Azure Monitor 리소스는 고객이 모니터링 원격 분석을 볼 수 있는 기본 위치입니다.
- 관리되는 리소스 그룹은 SAP 솔루션을 위한 Azure Monitor 리소스 배포의 일부로 자동으로 배포됩니다. 관리되는 리소스 그룹 내에 배포된 리소스는 원격 분석 데이터를 수집하는 데 도움이 됩니다. 배포된 주요 리소스와 그 용도는 다음과 같습니다.
   - Azure 가상 머신: *수집기 VM* 이라고도 합니다. Standard_B2ms VM입니다. 해당 VM의 주 용도는 *모니터링 페이로드* 를 호스트하는 것입니다. 페이로드 모니터링은 원본 시스템에서 원격 분석 데이터를 수집하고, 수집한 해당 데이터를 모니터링 프레임워크로 전송하는 논리를 나타냅니다. 위 다이어그램에서 모니터링 페이로드는 SQL 포트를 통해 SAP HANA 데이터베이스에 연결하기 위한 논리를 포함합니다.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): 이 리소스는 SAP HANA 데이터베이스 자격 증명을 안전히 보관하고 [공급자](./azure-monitor-providers.md)에 대한 정보를 저장하기 위해 배포됩니다.
   - Log Analytics 작업 영역: 원격 분석 데이터가 위치할 대상입니다.
      - 시각화는 [Azure 통합 문서](../../../azure-monitor/visualize/workbooks-overview.md)를 사용하여 Log Analytics에서 원격 분석을 기반으로 빌드됩니다. 고객은 시각화를 사용자 지정할 수 있습니다. 고객은 최소 세분성이 30분인 자동 새로 고침을 위해 통합 문서나 통합 문서 내의 특정 시각화를 Azure 대시보드에 고정할 수 있습니다.
      - 고객은 배포 시 해당 옵션을 선택하여 SAP 모니터 리소스와 동일한 구독 내에 위치한 기존의 작업 영역을 사용할 수 있습니다.
      - 고객은 KQL(Kusto 쿼리 언어)을 사용하여 Log Analytics 작업 영역 내의 원시 테이블에 대해 [쿼리](../../../azure-monitor/logs/log-query-overview.md)를 실행할 수 있습니다. *사용자 지정 로그* 확인

> [!Note]
> 고객은 관리되는 리소스 그룹 내에 배포된 VM의 패치와 유지를 담당합니다.

> [!Tip]
> 고객은 원격 분석 컬렉션을 위한 기존의 Log Analytics 작업 영역이 SAP 솔루션을 위한 Azure Monitor용 리소스로서 동일한 Azure 구독 내에 배포된 경우 이를 사용하도록 선택할 수 있습니다.

### <a name="architecture-highlights"></a>아키텍처 주요 내용

아키텍처의 주요 내용은 다음과 같습니다.
 - **다중 인스턴스** - 고객은 SAP 솔루션을 위한 Azure Monitor용 단일 리소스를 이용해 단일 VNET 내의 여러 SAP SIDs에 걸쳐 주어진 구성 요소 유형(예를 들면, HANA DB, HA 클러스터, Microsoft SQL Server)의 여러 인스턴스에 대한 모니터링을 만들 수 있습니다.
 - **다중 공급자** - 위의 아키텍처 다이어그램은 SAP HANA 공급자를 예로써 보여줍니다. 마찬가지로, 고객은 해당 구성 요소(예를 들면, HANA DB, HA 클러스터, Microsoft SQL Server)에 대해 추가적인 공급자를 구성하여 해당 구성 요소에서 데이터를 수집할 수 있습니다.
 - **오픈 소스** - SAP 솔루션을 위한 Azure Monitor의 소스 코드는 [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions)에서 이용할 수 있습니다. 고객은 공급자 코드를 참조하여 제품 및 기여네 대한 자세한 정보를 확인하고 피드백을 공유할 수 있습니다.
 - **확장 가능한 쿼리 프레임워크** - 원격 분석 데이터 수집을 위한 SQL 쿼리는 [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json)으로 작성됩니다. 원격 분석 데이터를 더 많이 수집하기 위해 SQL 쿼리를 쉽게 추가할 수 있습니다. 고객은 본 문서의 끝에 있는 링크를 통해 피드백을 남기거나 담당 계정 팀에 문의하여 SAP 솔루션을 위한 Azure Monitor에 추가할 특정 원격 분석 데이터를 요청할 수 있습니다.

## <a name="pricing"></a>가격 책정
SAP 솔루션을 위한 Azure Monitor는 무상 제품입니다(라이선스 요금이 없음). 고객은 관리되는 리소스 그룹 내의 기본 구성 요소에 대한 비용을 지불할 책임이 있습니다.

## <a name="next-steps"></a>다음 단계

공급자에 대해 알아보고 SAP 솔루션을 위한 첫 번째 Azure Monitor 리소스를 만듭니다.
 - [공급자](./azure-monitor-providers.md)에 대해 자세히 알아보세요.
 - [Azure PowerShell을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포](azure-monitor-sap-quickstart-powershell.md)
 - SAP 솔루션을 위한 Azure Monitor에 대해 질문이 있나요? [FAQ](./azure-monitor-faq.md) 섹션을 확인하세요.
