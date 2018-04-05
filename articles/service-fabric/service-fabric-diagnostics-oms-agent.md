---
title: Azure Service Fabric - OMS 에이전트로 모니터링 설정 | Microsoft Docs
description: Azure Service Fabric 클러스터의 컨테이너 및 성능 카운터를 모니터링하기 위해 OMS 에이전트를 설정하는 방법에 대해 알아보세요.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 4b0845cbb25d160b53b483641e242422c98029ee
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="add-the-oms-agent-to-a-cluster"></a>OMS 에이전트를 클러스터에 추가

이 문서에서는 OMS 에이전트를 가상 머신 확장 집합의 확장 기능으로 클러스터에 추가하고 이를 기존의 Azure Log Analytics 작업 영역에 연결하는 단계를 설명합니다. 이를 통해 컨테이너, 응용 프로그램 및 성능 모니터링에 대한 진단 데이터를 수집할 수 있습니다. Azure Resource Manager를 확장자로 추가하면 클러스터 확장 시에도 모든 노드에 설치됩니다.

> [!NOTE]
> 이 문서는 Azure Log Analytics 작업 영역이 이미 설정되었다고 가정합니다. 그렇지 않은 경우 [Azure Log Analytics 설정](service-fabric-diagnostics-oms-setup.md)을 참조하세요.

## <a name="add-the-agent-extension-via-azure-cli"></a>Azure CLI를 통해 에이전트 확장 추가

OMS 에이전트를 클러스터에 추가하는 가장 좋은 방법은 Azure CLI로 제공되는 가상 머신 확장 집합 API를 통하는 것입니다. 아직 Azure CLI가 설정되지 않은 경우 Azure Portal로 이동하여 [Cloud Shell](../cloud-shell/overview.md) 인스턴스를 열거나 [Azure CLI 2.0을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

1. Cloud Shell이 요청되면 리소스와 동일한 구독에서 작업 중인지 확인합니다. `az account show`로 이를 확인하여 "name" 값이 클러스터 구독의 값과 일치하는지 확인합니다.

2. 포털에서 Log Analytics 작업 영역이 있는 리소스 그룹으로 이동합니다. Log Analytics 리소스(Log Analytics가 될 리소스 종류)를 클릭하고 오른쪽 탐색 모음에서 아래로 스크롤하여 **속성**을 클릭합니다.

    ![Log Analytics 속성 페이지](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    `workspaceId`를 기록해 둡니다. 

3. 또한 에이전트를 배포할 `workspaceKey`도 필요합니다. 키를 가져오려면 왼쪽 탐색 모음의 *설정* 아래에 있는 **고급 설정**을 클릭합니다. Windows 클러스터를 준비하는 경우 **Windows 서버**를 클릭하고 Linux 클러스터를 만드는 경우 **Linux 서버**를 클릭합니다. `workspaceKey`로 에이전트를 배포하기 위해 표시되는 *기본 키*가 필요합니다.

4. 명령을 실행하여 Cloud Shell에서 `vmss extension set` API를 사용하여 클러스터에 OMS 에이전트를 설치합니다.

    Windows 클러스터의 경우:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Linux 클러스터의 경우:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    다음은 Windows 클러스터에 OMS 에이전트를 추가하는 예입니다.

    ![OMS 에이전트 cli 명령](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. 이미 존재하는 VM 인스턴스에 이 구성을 적용하는 명령을 실행합니다.  

    ```sh
    az vmss update-instances
    ```

    에이전트를 노드에 성공적으로 추가하기 위해서는 15분 미만이 소요됩니다. `az vmss extension list` API를 사용하여 에이전트가 추가되었는지 확인할 수 있습니다.

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Resource Manager 템플릿을 통해 에이전트 추가

Azure Log Analytics 작업 영역을 배포하고 각 노드에 에이전트를 추가하는 샘플 Resource Manager 템플릿은 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 또는 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)에서 사용할 수 있습니다.

이 템플릿을 다운로드하고 수정하여 필요에 가장 적합한 클러스터를 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 관련 [성능 카운터](service-fabric-diagnostics-event-generation-perf.md)를 수집합니다. OMS 에이전트를 구성하여 특정 성능 카운터를 수집하려면 [데이터 원본 구성](../log-analytics/log-analytics-data-sources.md#configuring-data-sources)을 검토합니다.
* 검색 및 진단에 도움이 되는 [자동 경고](../log-analytics/log-analytics-alerts.md)를 설정하도록 Log Analytics를 구성합니다.
