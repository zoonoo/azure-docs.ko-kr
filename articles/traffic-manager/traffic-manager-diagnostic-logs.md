---
title: Azure Traffic Manager에서 진단 로깅 사용
description: Traffic Manager 프로필에 대해 진단 로깅을 사용하도록 설정하고, 결과로 만들어진 로그 파일에 액세스하는 방법을 알아봅니다.
services: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.openlocfilehash: b2ebeb41e69b7edfd43c38cc3b828069a1b3401a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071238"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Azure Traffic Manager에서 진단 로깅 사용

이 문서에서는 Traffic Manager 프로필에 대한 진단 로깅 및 액세스 로그 데이터를 사용하도록 설정하는 방법에 대해 설명합니다.

Azure Traffic Manager 진단 로그는 Traffic Manager 프로필 리소스의 동작에 대한 인사이트를 제공합니다. 예를 들어 프로필의 로그 데이터를 사용하여 개별 프로브에서 엔드포인트에 대해 시간이 초과된 이유를 확인할 수 있습니다.

## <a name="enable-diagnostic-logging"></a>진단 로깅 사용

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell) 뒤에 오는 명령을 실행하거나 또는 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 무료 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 컴퓨터에서 PowerShell을 실행하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 설치된 버전을 확인할 수 있습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`도 실행하여 Azure에 로그인해야 합니다.

1. **Traffic Manager 프로필 검색:**

    진단 로깅을 사용하도록 설정하려면 Traffic Manager 프로필의 ID가 필요합니다. [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)을 사용하여 진단 로깅을 사용하도록 설정하려는 Traffic Manager 프로필을 검색합니다. 출력에 Traffic Manager 프로필의 ID 정보가 포함되어 있습니다.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Traffic Manager 프로필에 대한 진단 로깅 사용:**

    이전 단계에서 [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest)을 통해 가져온 ID를 사용하여 Traffic Manager 프로필에 대한 진단 로깅을 사용하도록 설정합니다. 다음 명령은 Traffic Manager 프로필에 대한 자세한 정보 로그를 지정된 Azure Storage 계정에 저장합니다. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **진단 설정 확인:**

      [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest)을 사용하여 Traffic Manager 프로필에 대한 진단 설정을 확인합니다. 다음 명령은 리소스에 대해 기록되는 범주를 표시합니다.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Traffic Manager 프로필 리소스와 연결된 모든 로그 범주가 '사용'으로 표시되는지 확인합니다. 또한 스토리지 계정이 올바르게 설정되어 있는지도 확인합니다.

## <a name="access-log-files"></a>로그 파일 액세스
1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. 포털에서 Azure Storage 계정으로 이동합니다.
2. Azure 스토리지 계정의 **개요** 페이지에 있는 **서비스** 아래에서 **Blob**을 선택합니다.
3. **컨테이너**에 대해 **insights-logs-probehealthstatusevents**를 선택하고, PT1H.json 파일까지 아래로 이동한 다음, **다운로드**를 클릭하여 이 로그 파일의 복사본을 다운로드하고 저장합니다.

    ![Blob 스토리지에서 Traffic Manager 프로필의 로그 파일에 액세스](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Traffic Manager 로그 스키마

Azure Monitor를 통해 사용할 수 있는 모든 진단 로그는 일반적인 최상위 수준 스키마를 공유하며, 각 서비스가 자체 이벤트에 대한 고유한 속성을 유연성 있게 내보낼 수 있습니다. 최상위 수준 진단 로그 스키마는 [Azure 진단 로그에 지원되는 서비스, 스키마 및 범주](../azure-monitor/platform/tutorial-dashboards.md)를 참조하세요.

다음 표에는 Azure Traffic Manager 프로필 리소스와 관련된 로그 스키마가 나와 있습니다.

|||||
|----|----|---|---|
|**필드 이름**|**필드 형식**|**정의**|**예제**|
|EndpointName|String|상태가 기록되고 있는 Traffic Manager 엔드포인트의 이름입니다.|*myPrimaryEndpoint*|
|상태|String|검색된 Traffic Manager 엔드포인트의 상태입니다. 상태는 **위로** 또는 **아래로**일 수 있습니다.|**위로**|
|||||

## <a name="next-steps"></a>다음 단계

* [Traffic Manager 모니터링](traffic-manager-monitoring.md)에 대해 자세히 알아봅니다.

