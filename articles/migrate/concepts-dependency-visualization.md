---
title: Azure Migrate 검색 및 평가에서 종속성 분석
description: Azure Migrate 검색 및 평가를 사용 하 여 평가에 대 한 종속성 분석을 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 184c8099c0e86d8f8744948137b344c732bbf7b8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778392"
---
# <a name="dependency-analysis"></a>종속성 분석

이 문서에서는 Azure Migrate: 검색 및 평가의 종속성 분석에 대해 설명 합니다.

종속성 분석은 검색 된 온-프레미스 서버 간의 종속성을 식별 합니다. 이는 다음과 같은 이점을 제공 합니다.

- 평가를 위해 서버를 그룹으로 더 정확 하 게 수집할 수 있습니다.
- 함께 마이그레이션해야 하는 서버를 식별할 수 있습니다. 이 기능은 Azure로 마이그레이션하려는 앱 배포의 일부인 서버를 확실 하 게 모르는 경우에 특히 유용 합니다.
- 서버가 사용 중인지 여부와 마이그레이션 대신 서비스 해제할 수 있는 서버를 확인할 수 있습니다.
- 종속성을 분석 하면 아무것도 유지 되지 않으므로 마이그레이션 후 예기치 않은 중단이 방지 됩니다.
- 종속성 분석에 대 한 일반적인 질문을 [검토](common-questions-discovery-assessment.md#what-is-dependency-visualization) 합니다.

## <a name="analysis-types"></a>분석 유형

종속성 분석을 배포 하는 두 가지 옵션이 있습니다.

**옵션** | **세부 정보** | **퍼블릭 클라우드** | **Azure Government**
----  |---- | ----
**에이전트 없음** | VSphere Api를 사용 하 여 VMware의 서버에서 데이터를 폴링합니다.<br/><br/> 서버에 에이전트를 설치할 필요가 없습니다.<br/><br/> 이 옵션은 현재 미리 보기로 제공 되며 VMware의 서버에 대해서만 사용할 수 있습니다. | 지원됨. | 지원됨.
**에이전트 기반 분석** | Azure Monitor에서 [서비스 맵 솔루션](../azure-monitor/vm/service-map.md) 을 사용 하 여 종속성 시각화 및 분석을 사용 하도록 설정 합니다.<br/><br/> 분석 하려는 각 온-프레미스 서버에 에이전트를 설치 해야 합니다. | 지원됨 | 지원되지 않습니다.

## <a name="agentless-analysis"></a>에이전트 없는 분석

에이전트 없는 종속성 분석은 사용 하도록 설정 된 서버에서 TCP 연결 데이터를 캡처하여 작동 합니다. 서버에 에이전트가 설치 되어 있지 않습니다. 동일한 원본 서버와 프로세스 및 대상 서버, 프로세스 및 포트를 사용 하는 연결은 논리적으로 종속성으로 그룹화 됩니다. 지도 뷰에서 캡처된 종속성 데이터를 시각화 하거나 CSV로 내보낼 수 있습니다. 분석 하려는 서버에 에이전트가 설치 되어 있지 않습니다.

### <a name="dependency-data"></a>종속성 데이터

종속성 데이터의 검색이 시작 된 후 폴링이 시작 됩니다.

- Azure Migrate 어플라이언스는 데이터를 수집 하기 위해 서버에서 5 분 마다 TCP 연결 데이터를 폴링합니다.
- 데이터는 vSphere Api를 사용 하 여 vCenter Server를 통해 게스트 서버에서 수집 됩니다.
- 폴링은 다음 데이터를 수집 합니다.

    - 활성 연결이 있는 프로세스의 이름입니다.
    - 활성 연결이 있는 프로세스를 실행 하는 응용 프로그램의 이름입니다.
    - 활성 연결의 대상 포트입니다.

- 수집 된 데이터는 Azure Migrate 어플라이언스에서 처리 되어 id 정보를 추론 하 고 6 시간 마다 Azure Migrate 전송 됩니다.


## <a name="agent-based-analysis"></a>에이전트 기반 분석

에이전트 기반 분석의 경우 Azure Migrate: 검색 및 평가는 Azure Monitor의 [서비스 맵](../azure-monitor/vm/service-map.md) 솔루션을 사용 합니다. 분석 하려는 각 서버에 [Microsoft Monitoring Agent/Log Analytics 에이전트](../azure-monitor/agents/agents-overview.md#log-analytics-agent) 와 [종속성 에이전트](../azure-monitor/agents/agents-overview.md#dependency-agent)를 설치 합니다.

### <a name="dependency-data"></a>종속성 데이터

에이전트 기반 분석은 다음 데이터를 제공 합니다.

- 원본 서버 이름, 프로세스, 응용 프로그램 이름입니다.
- 대상 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다.
- 연결 수, 대기 시간 및 데이터 전송 정보를 수집 하 고 Log Analytics 쿼리에 사용할 수 있습니다.

## <a name="compare-agentless-and-agent-based"></a>에이전트 없는 에이전트 및 에이전트 기반 비교

에이전트 없는 시각화와 에이전트 기반 시각화 간의 차이점은 표에 요약 되어 있습니다.

**요구 사항** | **에이전트 없음** | **에이전트 기반**
--- | --- | ---
**지원** | VMware의 서버에 대 한 미리 보기 상태입니다. 지원 되는 운영 체제를 [검토](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) 합니다. | GA (일반 공급)를 사용 합니다.
**에이전트** | 분석 하려는 서버에서 에이전트가 필요 하지 않습니다. | 분석 하려는 각 온-프레미스 서버에서 에이전트가 필요 합니다.
**Log Analytics** | 필수 아님. | Azure Migrate는 종속성 분석을 위해 [Azure Monitor 로그](../azure-monitor/logs/log-query-overview.md) 의 [서비스 맵](../azure-monitor/vm/service-map.md) 솔루션을 사용 합니다.<br/><br/> Log Analytics 작업 영역을 프로젝트와 연결 합니다. 작업 영역은 미국 동부, 동남 아시아 또는 서유럽 지역에 있어야 합니다. 작업 영역은 [서비스 맵 지원되는](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.
**처리** | TCP 연결 데이터를 캡처합니다. 검색 후 5 분 간격으로 데이터를 수집 합니다. | 서버에 설치 된 서비스 맵 에이전트는 TCP 프로세스에 대 한 데이터 및 각 프로세스에 대 한 인바운드/아웃 바운드 연결을 수집 합니다.
**Data** | 원본 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다. | 원본 서버 이름, 프로세스, 응용 프로그램 이름입니다.<br/><br/> 대상 서버 이름, 프로세스, 응용 프로그램 이름 및 포트입니다.<br/><br/> 연결 수, 대기 시간 및 데이터 전송 정보를 수집 하 고 Log Analytics 쿼리에 사용할 수 있습니다. 
**시각화** | 단일 서버에 대 한 종속성 맵은 1 시간에서 30 일 동안 볼 수 있습니다. | 단일 서버의 종속성 맵입니다.<br/><br/> 서버 그룹의 종속성 맵입니다.<br/><br/>  지도는 한 시간에 한 해 볼 수 있습니다.<br/><br/> 지도 보기에서 그룹의 서버를 추가 하 고 제거 합니다.
데이터 내보내기 | 지난 30 일간의 데이터는 CSV 형식으로 다운로드할 수 있습니다. | Log Analytics를 사용 하 여 데이터를 쿼리할 수 있습니다.



## <a name="next-steps"></a>다음 단계

- 에이전트 기반 종속성 시각화 [를 설정](how-to-create-group-machine-dependencies.md) 합니다.
- VMware에서 서버에 대 한 에이전트 없는 종속성 시각화를 [사용해 보세요](how-to-create-group-machine-dependencies-agentless.md) .
- 종속성 시각화에 대 한 [일반적인 질문](common-questions-discovery-assessment.md#what-is-dependency-visualization) 을 검토 합니다.
