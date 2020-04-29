---
title: Azure Migrate Server 평가에서 종속성 분석
description: Azure Migrate Server 평가를 사용 하 여 평가에 대 한 종속성 분석을 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: f0b956620895ae2264b53916015d440f5e586eb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024764"
---
# <a name="dependency-analysis"></a>종속성 분석

이 문서에서는 Azure Migrate: 서버 평가의 종속성 분석에 대해 설명 합니다.

## <a name="overview"></a>개요

종속성 분석을 사용 하면 평가 하 여 Azure로 마이그레이션할 온-프레미스 컴퓨터 간의 종속성을 식별할 수 있습니다. 

- Azure Migrate: 서버 평가에서 그룹으로 컴퓨터를 수집한 다음 그룹을 평가 합니다. 종속성 분석을 사용 하면 평가를 위한 신뢰도를 높이기 위해 컴퓨터를 보다 정확 하 게 그룹화 할 수 있습니다.
- 종속성 분석을 사용 하면 함께 마이그레이션해야 하는 컴퓨터를 식별할 수 있습니다. 컴퓨터가 사용 중인지 여부 또는 마이그레이션 대신 서비스를 해제할 수 있는지 여부를 식별할 수 있습니다.
- 종속성을 분석 하면 더 이상 남아 있지 않으며 마이그레이션 중에 예기치 않은 중단이 발생 하지 않습니다.
- 분석은 컴퓨터가 Azure로 마이그레이션하려는 앱 배포의 일부 인지 확실 하지 않은 경우에 특히 유용 합니다.
- 종속성 분석에 대 한 일반적인 질문을 [검토](common-questions-discovery-assessment.md#what-is-dependency-visualization) 합니다.

종속성 분석을 배포 하는 두 가지 옵션이 있습니다.

- **에이전트 기반**: 에이전트 기반 종속성 분석을 사용 하려면 분석 하려는 각 온-프레미스 컴퓨터에 에이전트를 설치 해야 합니다.
- **에이전트**없는 분석: 에이전트 없는 분석을 사용 하면 교차 확인 하려는 컴퓨터에 에이전트를 설치할 필요가 없습니다. 이 옵션은 현재 미리 보기 상태 이며 VMware Vm에 대해서만 사용할 수 있습니다.

> [!NOTE]
> 에이전트 기반 종속성 분석은 Azure Government에서 사용할 수 없습니다. 에이전트 없는 종속성 분석을 사용할 수 있습니다.

## <a name="agentless-analysis"></a>에이전트 없는 분석

에이전트 없는 종속성 분석은 사용 하도록 설정 된 컴퓨터에서 TCP 연결 데이터를 캡처하여 작동 합니다. 분석 하려는 컴퓨터에 에이전트가 설치 되어 있지 않습니다.

### <a name="collected-data"></a>수집된 데이터

종속성 검색이 시작 되 면 어플라이언스는 데이터를 수집 하기 위해 5 분 마다 컴퓨터에서 데이터를 폴링합니다. 이 데이터는 vSphere Api를 사용 하 여 vCenter Server를 통해 게스트 Vm에서 수집 됩니다. 수집 된 데이터는 Azure Migrate 어플라이언스에서 처리 되어 id 정보를 추론 하 고 6 시간 마다 Azure Migrate 전송 됩니다.

폴링은 컴퓨터에서이 데이터를 수집 합니다. 
- 활성 연결이 있는 프로세스의 이름입니다.
- 활성 연결이 있는 프로세스를 실행 하는 응용 프로그램의 이름입니다.
- 활성 연결의 대상 포트입니다.

## <a name="agent-based-analysis"></a>에이전트 기반 분석

에이전트 기반 분석의 경우 서버 평가에서는 Azure Monitor의 [서비스 맵 솔루션](../azure-monitor/insights/service-map.md) 을 사용 하 여 종속성 시각화 및 분석을 사용 하도록 설정 합니다. 분석 하려는 각 컴퓨터에 [Microsoft Monitoring Agent/Log Analytics 에이전트](../azure-monitor/platform/agents-overview.md#log-analytics-agent) 및 [종속성 에이전트가](../azure-monitor/platform/agents-overview.md#dependency-agent)설치 되어 있어야 합니다.

### <a name="collected-data"></a>수집된 데이터

에이전트 기반 분석의 경우 다음 데이터가 수집 됩니다.

- 원본 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.
- 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다.
- 연결 수, 대기 시간 및 데이터 전송 정보를 수집 하 고 Log Analytics 쿼리에 사용할 수 있습니다. 


## <a name="compare-agentless-and-agent-based"></a>에이전트 없는 에이전트 및 에이전트 기반 비교

에이전트 없는 시각화와 에이전트 기반 시각화 간의 차이점은 표에 요약 되어 있습니다.

**요구 사항** | **에이전트 없음** | **에이전트 기반**
--- | --- | ---
Support(지원) | 이 옵션은 현재 미리 보기 상태 이며 VMware Vm에 대해서만 사용할 수 있습니다. 지원 되는 운영 체제를 [검토](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) 합니다. | GA (일반 공급)를 사용 합니다.
에이전트 | 교차 확인 하려는 컴퓨터에 에이전트를 설치할 필요가 없습니다. | [MMA (Microsoft Monitoring agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)및 [종속성 에이전트](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)를 분석 하려는 각 온-프레미스 컴퓨터에 설치 되는 에이전트입니다. 
Log Analytics | 필요하지 않음. | Azure Migrate는 종속성 분석을 위해 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 의 [서비스 맵](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) 솔루션을 사용 합니다. 
작동 방식 | 종속성 시각화에 사용 되는 컴퓨터에서 TCP 연결 데이터를 캡처합니다. 검색 후 5 분 간격으로 데이터를 수집 합니다. | 컴퓨터에 설치 된 서비스 맵 에이전트는 각 프로세스에 대 한 TCP 프로세스 및 인바운드/아웃 바운드 연결에 대 한 데이터를 수집 합니다.
데이터 | 원본 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다. | 원본 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다.<br/><br/> 연결 수, 대기 시간 및 데이터 전송 정보를 수집 하 고 Log Analytics 쿼리에 사용할 수 있습니다. 
시각화 | 단일 서버에 대 한 종속성 맵은 1 시간에서 30 일 동안 볼 수 있습니다. | 단일 서버의 종속성 맵입니다.<br/><br/> 지도는 한 시간에 한 해 볼 수 있습니다.<br/><br/> 서버 그룹의 종속성 맵입니다.<br/><br/> 지도 보기에서 그룹의 서버를 추가 하 고 제거 합니다.
데이터 내보내기 | 현재 테이블 형식으로 다운로드할 수 없습니다. | Log Analytics를 사용 하 여 데이터를 쿼리할 수 있습니다.



## <a name="next-steps"></a>다음 단계
- [VMware vm](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements), [물리적 서버](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)및 [hyper-v vm](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)에 대 한 에이전트 기반 분석을 설정 하기 위한 요구 사항을 검토 합니다.
- VMware Vm의 에이전트 없는 분석에 대 한 요구 사항을 [검토](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) 합니다.
- 에이전트 기반 종속성 시각화 [설정](how-to-create-group-machine-dependencies.md)
- VMware Vm에 대 한 에이전트 없는 종속성 시각화를 [사용해 보세요](how-to-create-group-machine-dependencies-agentless.md) .
- 종속성 시각화에 대 한 [일반적인 질문](common-questions-discovery-assessment.md#what-is-dependency-visualization) 을 검토 합니다.


