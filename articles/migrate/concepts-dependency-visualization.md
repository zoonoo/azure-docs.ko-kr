---
title: Azure 마이그레이션 서버 평가의 종속성 분석
description: Azure 마이그레이션 서버 평가를 사용하여 평가에 종속성 분석을 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f96496b66d6bcfd397fb0a7303d3dbfb4fd6f6b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455641"
---
# <a name="dependency-analysis"></a>종속성 분석

이 문서에서는 Azure 마이그레이션:서버 평가에서 종속성 분석을 설명합니다.

## <a name="overview"></a>개요

종속성 분석을 사용하면 Azure를 평가하고 마이그레이션하려는 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 

- Azure 마이그레이션:서버 평가에서 그룹으로 컴퓨터를 수집 한 다음 그룹을 평가합니다. 종속성 분석을 사용하면 평가에 대한 신뢰도가 높은 컴퓨터를 보다 정확하게 그룹화할 수 있습니다.
- 종속성 분석을 사용하면 함께 마이그레이션해야 하는 컴퓨터를 식별할 수 있습니다. 사용 중인지 또는 마이그레이션하는 대신 폐기할 수 있는지 여부를 식별할 수 있습니다.
- 종속성을 분석하면 남은 것이 없도록 하고 마이그레이션 중에 급격한 중단을 방지할 수 있습니다.
- 분석은 컴퓨터가 Azure로 마이그레이션하려는 앱 배포의 일부인지 확실하지 않은 경우에 특히 유용합니다.
- 종속성 분석에 대한 일반적인 질문을 [검토합니다.](common-questions-discovery-assessment.md#what-is-dependency-visualization)

종속성 분석을 배포하기 위한 두 가지 옵션이 있습니다.

- **에이전트 기반**: 에이전트 기반 종속성 분석을 수행하려면 분석하려는 각 온-프레미스 컴퓨터에 에이전트를 설치해야 합니다.
- **에이전트리스**: 에이전트없는 분석을 사용하면 교차 검사하려는 컴퓨터에 에이전트를 설치할 필요가 없습니다. 이 옵션은 현재 미리 보기 중이며 VMware VM에서만 사용할 수 있습니다.

> [!NOTE]
> Azure 정부에서는 종속성 분석을 사용할 수 없습니다.

## <a name="agentless-analysis"></a>에이전트 없는 분석

에이전트 없는 종속성 분석은 활성화된 컴퓨터에서 TCP 연결 데이터를 캡처하여 작동합니다. 분석하려는 컴퓨터에 에이전트가 설치되지 않습니다.

### <a name="collected-data"></a>수집된 데이터

종속성 검색이 시작된 후 어플라이언스는 5분마다 컴퓨터에서 데이터를 폴링을 사용하여 데이터를 수집합니다. 이 데이터는 vSphere API를 사용하여 vCenter 서버를 통해 게스트 VM에서 수집됩니다. 수집된 데이터는 Azure 마이그레이션 어플라이언스에서 처리되어 ID 정보를 추론하고 6시간마다 Azure 마이그레이션으로 전송됩니다.

폴링은 컴퓨터에서 이 데이터를 수집합니다. 
- 활성 연결이 있는 프로세스의 이름입니다.
- 활성 연결이 있는 프로세스를 실행하는 응용 프로그램의 이름입니다.
- 활성 연결의 대상 포트입니다.

## <a name="agent-based-analysis"></a>에이전트 기반 분석

에이전트 기반 분석의 경우 서버 평가는 Azure Monitor의 [서비스 맵 솔루션을](../azure-monitor/insights/service-map.md) 사용하여 종속성 시각화 및 분석을 활성화합니다. [Microsoft 모니터링 에이전트/로그 분석 에이전트](../azure-monitor/platform/agents-overview.md#log-analytics-agent) 및 [종속성 에이전트는](../azure-monitor/platform/agents-overview.md#dependency-agent)분석하려는 각 컴퓨터에 설치해야 합니다.

### <a name="collected-data"></a>수집된 데이터

에이전트 기반 시각화의 경우 다음 데이터가 수집됩니다.

- 소스 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.
- 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다.
- 연결 수, 대기 시간 및 데이터 전송 정보가 수집되어 Log Analytics 쿼리에 사용할 수 있습니다. 


## <a name="compare-agentless-and-agent-based"></a>에이전트 없는 에이전트 및 에이전트 기반 비교

에이전트 없는 시각화와 에이전트 기반 시각화 간의 차이점이 표에 요약되어 있습니다.

**요구 사항** | **에이전트 없음** | **에이전트 기반**
--- | --- | ---
고객 지원팀 | 이 옵션은 현재 미리 보기 중이며 VMware VM에서만 사용할 수 있습니다. 지원되는 운영 체제를 [검토합니다.](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) | 일반 공급 (GA).
에이전트 | 교차 확인하려는 컴퓨터에 에이전트를 설치할 필요가 없습니다. | 분석하려는 각 온-프레미스 컴퓨터에 설치할 에이전트: [MMA(Microsoft 모니터링 에이전트)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)및 [종속성 에이전트](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Log Analytics | 필요하지 않습니다. | Azure 마이그레이션은 종속성 분석을 위해 [Azure Monitor 로그의](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) [서비스 맵](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) 솔루션을 사용합니다. 
작동 방법 | 종속성 시각화에 사용하도록 설정된 컴퓨터에서 TCP 연결 데이터를 캡처합니다. 검색 후 5분 간격으로 데이터를 수집합니다. | 컴퓨터에 설치된 서비스 맵 에이전트는 각 프로세스에 대한 TCP 프로세스 및 인바운드/아웃바운드 연결에 대한 데이터를 수집합니다.
데이터 | 소스 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다. | 소스 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다.<br/><br/> 연결 수, 대기 시간 및 데이터 전송 정보가 수집되어 Log Analytics 쿼리에 사용할 수 있습니다. 
시각화 | 단일 서버의 종속성 맵은 1시간에서 30일 동안 볼 수 있습니다. | 단일 서버의 종속성 맵입니다.<br/><br/> 지도는 한 시간 동안만 볼 수 있습니다.<br/><br/> 서버 그룹의 종속성 맵입니다.<br/><br/> 맵 보기에서 그룹의 서버를 추가 및 제거합니다.
데이터 내보내기 | 현재 테이블 형식으로 다운로드할 수 없습니다. | 로그 분석을 통해 데이터를 쿼리할 수 있습니다.



## <a name="next-steps"></a>다음 단계
- [VMware VM,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)물리적 서버 및 [하이퍼 VM에](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)대한 에이전트 기반 분석을 설정하기 위한 요구 사항을 [검토합니다.](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
- VMware VM의 에이전트 없는 분석에 대한 요구 사항을 [검토합니다.](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)
- 에이전트 기반 종속성 시각화 [설정](how-to-create-group-machine-dependencies.md)
- VMware VM에 대한 에이전트 없는 종속성 시각화를 [사용해 보십시오.](how-to-create-group-machine-dependencies-agentless.md)
- 종속성 시각화에 대한 [일반적인 질문을](common-questions-discovery-assessment.md#what-is-dependency-visualization) 검토합니다.


