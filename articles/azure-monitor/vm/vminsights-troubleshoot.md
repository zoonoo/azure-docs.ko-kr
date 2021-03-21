---
title: VM 정보 문제 해결
description: VM insights 설치 문제를 해결 합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555657"
---
# <a name="troubleshoot-vm-insights"></a>VM 정보 문제 해결
이 문서에서는 VM insights를 사용 하거나 사용 하도록 설정 하는 데 문제가 있는 경우의 문제 해결 정보를 제공 합니다.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>컴퓨터에서 VM Insights를 사용 하도록 설정할 수 없음
Azure Portal에서 Azure 가상 머신을 온 보 딩 하면 다음 단계가 수행 됩니다.

- 해당 옵션이 선택 된 경우 기본 Log Analytics 작업 영역이 생성 됩니다.
- 에이전트가 이미 설치 되어 있는 경우 VM 확장을 사용 하 여 Log Analytics 에이전트가 Azure 가상 컴퓨터에 설치 됩니다.
- 종속성 에이전트는 필요한 경우 확장을 사용 하 여 Azure 가상 컴퓨터에 설치 됩니다.
  
온 보 딩 프로세스 중에는 각 단계를 확인 하 고 알림 상태를 포털에 표시 합니다. 작업 영역 및 에이전트 설치의 구성에는 일반적으로 5~10분 정도가 걸립니다. 포털에서 데이터를 볼 수 있을 때까지 5 ~ 10 분이 소요 됩니다.

온 보 딩 프로세스를 수행한 후 가상 컴퓨터를 등록 해야 한다는 메시지가 표시 되는 경우 프로세스가 완료 될 때까지 최대 30 분이 걸릴 수 있습니다. 문제가 지속 되 면 다음 섹션에서 가능한 원인을 확인 하십시오.

### <a name="is-the-virtual-machine-running"></a>가상 머신이 실행 되 고 있나요?
 가상 컴퓨터가 잠시 꺼져 있거나, 현재 꺼져 있거나, 최근에 설정 된 경우에만 데이터가 도착할 때까지 데이터를 표시할 수 없습니다.

### <a name="is-the-operating-system-supported"></a>운영 체제가 지원 되나요?
운영 체제가 지원 되는 [운영 체제](vminsights-enable-overview.md#supported-operating-systems) 목록에 없는 경우 확장을 설치 하지 못하고 데이터가 도착할 때까지 대기 하 고 있음을이 메시지가 표시 됩니다.

### <a name="did-the-extension-install-properly"></a>확장이 제대로 설치 되었습니까?
가상 머신을 등록 해야 한다는 메시지가 표시 되 면 확장 중 하나 또는 둘 모두를 올바르게 설치 하지 못했음을 의미할 수 있습니다. Azure Portal에서 가상 머신에 대 한 **확장** 페이지를 확인 하 여 다음 확장이 나열 되어 있는지 확인 합니다.

| 운영 체제 | 에이전트 | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

설치 된 확장 목록에 운영 체제에 대 한 두 확장이 모두 표시 되지 않는 경우 설치 해야 합니다. 확장이 나열 되지만 해당 상태가 *프로 비전 성공* 으로 표시 되지 않으면 확장을 제거 하 고 다시 설치 해야 합니다.

### <a name="do-you-have-connectivity-issues"></a>연결 문제가 있나요?
Windows 컴퓨터의 경우  *Testcloudconnectivity* 도구를 사용 하 여 연결 문제를 식별할 수 있습니다. 이 도구는 기본적으로 *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent* 폴더의 에이전트와 함께 설치 됩니다. 관리자 권한 명령 프롬프트에서 도구를 실행 합니다. 결과를 반환 하 고 테스트가 실패 한 위치를 강조 표시 합니다. 

![TestCloudConnectivity 도구](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>추가 에이전트 문제 해결

Log Analytics 에이전트의 문제 해결에 대 한 다음 문서를 참조 하세요.

- [Windows용 Log Analytics 에이전트의 문제를 해결하는 방법](../agents/agent-windows-troubleshoot.md)
- [Linux용 Log Analytics 에이전트의 문제를 해결하는 방법](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>성능 보기에 데이터가 없음
에이전트가 올바르게 설치 되어 있지만 성능 보기에 데이터가 표시 되지 않으면 다음 섹션에서 가능한 원인을 확인 하십시오.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Log Analytics 작업 영역이 데이터 제한에 도달 했습니까?
데이터 수집 [에 대 한 용량 예약 및 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 확인 하세요.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>가상 머신이 로그 및 성능 데이터를 Azure Monitor 로그에 보내고 있나요?

Azure Portal의 Azure Monitor 메뉴에 있는 **로그** 에서 Log Analytics를 엽니다. 컴퓨터에 대해 다음 쿼리를 실행 합니다.

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

데이터가 표시 되지 않으면 에이전트에 문제가 있을 수 있습니다. 에이전트 문제 해결 정보는 위의 섹션을 참조 하세요.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>지도 보기에 가상 컴퓨터가 표시 되지 않음

### <a name="is-the-dependency-agent-installed"></a>종속성 에이전트가 설치 되어 있습니까?
 위의 섹션에 나와 있는 정보를 사용 하 여 종속성 에이전트가 설치 되어 있고 제대로 작동 하는지 확인 합니다.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Log Analytics 무료 계층에 있나요?
[Log Analytics 무료 계층](https://azure.microsoft.com/pricing/details/monitor/) 은 최대 5 개의 고유한 서비스 맵 컴퓨터를 허용 하는 레거시 가격 책정 계획입니다. 따라서 이전의 5개 서비스 맵 컴퓨터에서 데이터를 더 이상 보내지 않더라도 이후의 모든 컴퓨터는 서비스 맵에 표시되지 않습니다.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>가상 머신이 로그 및 성능 데이터를 Azure Monitor 로그에 보내고 있나요?
[성능 보기의 데이터 없음](#performance-view-has-no-data) 섹션에서 로그 쿼리를 사용 하 여 가상 컴퓨터에 대 한 데이터가 수집 되 고 있는지 확인 합니다. 데이터를 수집 하지 않는 경우 위에 설명 된 TestCloudConnectivity 도구를 사용 하 여 연결 문제가 있는지 확인 합니다.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>가상 컴퓨터가 지도 보기에 나타나지만 데이터가 누락 되었습니다.
가상 컴퓨터가 지도 보기에 있는 경우 종속성 에이전트가 설치 되어 실행 중이지만 커널 드라이버가 로드 되지 않았습니다. 다음 위치에서 로그 파일을 확인 합니다.

| 운영 체제 | 로그 | 
|:---|:---|
| Windows | C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

파일의 마지막 줄에 커널이 로드되지 않은 이유가 표시되어야 합니다. 예를 들어, 커널을 업데이트 한 경우 Linux에서 커널이 지원되지 않을 수 있습니다.
## <a name="next-steps"></a>다음 단계

- VM insights 에이전트 온 보 딩에 대 한 자세한 내용은 [vm Insights 사용 개요 사용](vminsights-enable-overview.md)을 참조 하세요.
