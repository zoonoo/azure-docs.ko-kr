---
title: Azure Migrate의 종속성 시각화
description: 에서 서버 평가 서비스의 평가 계산에 대 한 개요를 제공 Azure Migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 65a99e230262ae05d34dc8c04e87252c15133fda
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425683"
---
# <a name="dependency-visualization"></a>종속성 시각화

이 문서에서는 Azure Migrate: 서버 평가의 종속성 시각화 기능을 설명 합니다.

종속성 시각화를 사용 하면 평가 하 고 마이그레이션하려는 컴퓨터 간의 종속성을 이해할 수 있습니다. 신뢰 수준이 높은 컴퓨터를 평가 하려는 경우 일반적으로 종속성 매핑을 사용 합니다.

- Azure Migrate: 서버 평가에서 평가를 위해 컴퓨터를 그룹으로 모읍니다. 그룹은 일반적으로 함께 마이그레이션하려는 컴퓨터로 구성 되며 종속성 시각화를 사용 하면 컴퓨터를 정확 하 게 그룹화 할 수 있도록 컴퓨터 종속성을 교차 확인 하는 데 도움이 됩니다.
- 시각화를 사용 하 여 함께 마이그레이션해야 하는 상호 의존적인 시스템을 검색할 수 있습니다. 실행 중인 시스템이 아직 사용 중인지 여부 또는 시스템을 마이그레이션 대신 해제할 수 있는지 여부를 확인할 수 있습니다.
- 종속성을 시각화 하면 아무것도 유지 되지 않으며 마이그레이션 중에 예기치 않은 중단이 발생 하지 않습니다.
- 이 기능은 앱의 일부인 컴퓨터를 완전히 인식 하지 못하는 경우에 특히 유용 하므로 Azure로 마이그레이션해야 합니다.


> [!NOTE]
> Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.

## <a name="agent-based-and-agentless"></a>에이전트 기반 및 에이전트 없는

종속성 시각화를 배포 하기 위한 두 가지 옵션이 있습니다.

- **에이전트 없는 종속성 시각화**:이 옵션은 현재 미리 보기 상태 이며 VMware vm에 대해서만 사용할 수 있습니다. 컴퓨터에 에이전트를 설치 하지 않아도 됩니다. 
    - 사용 하도록 설정 된 컴퓨터에서 TCP 연결 데이터를 캡처하여 작동 합니다. [자세히 알아봅니다](how-to-create-group-machine-dependencies-agentless.md).
종속성 검색을 시작한 후에는 기기가 5 분의 폴링 간격으로 컴퓨터에서 데이터를 수집 합니다.
    - 수집 되는 데이터는 다음과 같습니다.
        - TCP 연결
        - 활성 연결이 있는 프로세스의 이름
        - 위의 프로세스를 실행 하는 설치 된 응용 프로그램의 이름
        - 아니요. 모든 폴링 간격에서 감지 된 연결
- **에이전트 기반 종속성 시각화**: 에이전트 기반 종속성 시각화를 사용 하려면 분석 하려는 각 온-프레미스 컴퓨터에 다음 에이전트를 다운로드 하 여 설치 해야 합니다.  
    - 각 머신에 [MMA(Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)를 설치해야 합니다. MMA 에이전트를 설치 하는 방법에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) .
    - 각 머신에 [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)를 설치해야 합니다. 종속성 에이전트를 설치 하는 방법에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) .
    - 또한 인터넷에 연결되지 않은 머신이 있으면 해당 머신에 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.

## <a name="agent-based-requirements"></a>에이전트 기반 요구 사항

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>종속성 시각화를 배포 하려면 어떻게 해야 하나요?

종속성 시각화를 배포 하기 전에 Azure Migrate: 서버 평가 도구를 프로젝트에 추가 하 여 Azure Migrate 프로젝트가 준비 되어 있어야 합니다. 온-프레미스 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다.

도구를 추가 하 고 [hyper-v](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)또는 물리적 서버용 어플라이언스를 배포 하는 방법에 [대해 자세히 알아보세요](how-to-assess.md) .


### <a name="how-does-it-work"></a>작동 원리

Azure Migrate는 종속성 시각화를 위해 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 의 [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) 솔루션을 사용 합니다.

- 종속성 시각화를 활용 하려면 Log Analytics 작업 영역 (신규 또는 기존)을 Azure Migrate 프로젝트와 연결 해야 합니다.
- 작업 영역은 Azure Migrate 프로젝트를 만들 때와 동일한 구독에 있어야 합니다.
- Azure Migrate는 미국 동부, 동남 아시아 및 유럽 서부 지역에 거주 하는 작업 영역을 지원 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다. 또한 작업 영역은 [서비스 맵 지원](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)되는 지역에 있어야 합니다.
- Azure Migrate 프로젝트에 대 한 작업 영역은 추가 된 후 수정할 수 없습니다.
- Log Analytics에서 Azure Migrate와 연결 된 작업 영역에는 마이그레이션 프로젝트 키와 프로젝트 이름이 지정 됩니다.

    ![Log Analytics 작업 영역 탐색](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>요금이 발생하나요?

종속성 시각화에는 서비스 맵 및 연결 된 Log Analytics 작업 영역이 필요 합니다. 

- 서비스 맵 솔루션에는 처음 180 일에 대 한 요금이 부과 되지 않습니다. 이는 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결한 날짜입니다.
- 180일 후에는 표준 Log Analytics 요금이 적용됩니다.
- 연결 된 Log Analytics 작업 영역에서 서비스 맵 이외의 다른 솔루션을 사용 하면 [표준 Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 요금이 발생 합니다.
- Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트를 삭제 한 후에는 서비스 맵 사용이 가능 하지 않으며 각 노드는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 청구 됩니다.

[여기](https://azure.microsoft.com/pricing/details/azure-migrate/)에서 Azure Migrate 가격 책정에 대해 자세히 알아보세요.

> [!NOTE]
> 2018 2 월 28 일에 일반 공급을 Azure Migrate 하기 전에 만든 프로젝트가 있는 경우 추가 서비스 맵 요금이 발생 했을 수 있습니다. 180 일 후에만 지불 되도록 하려면 일반 가용성 이전에 기존 작업 영역을 계속 청구 가능한 새 프로젝트를 만드는 것이 좋습니다.



### <a name="how-do-i-manage-the-workspace"></a>작업 영역은 어떻게 관리하나요?

- 작업 영역에 에이전트를 등록할 때 Azure Migrate 프로젝트에서 제공 하는 ID 및 키를 사용 합니다.
- Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.
- 연결 된 Azure Migrate 프로젝트를 삭제 하면 작업 영역이 자동으로 삭제 되지 않습니다. [수동으로 삭제](../azure-monitor/platform/manage-access.md)해야 합니다.
- Azure Migrate 프로젝트를 삭제 하지 않으면 Azure Migrate으로 만든 작업 영역을 삭제 하지 마세요. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계
- [컴퓨터 종속성을 사용하여 컴퓨터 그룹화](how-to-create-group-machine-dependencies.md)
- 종속성 시각화 관련 FAQ를 [자세히 확인](common-questions-discovery-assessment.md#what-is-dependency-visualization)해 보세요.


