---
title: SQL 인사이트에 대한 Resource Manager 템플릿 샘플
description: SQL 인사이트를 배포 및 구성하기 위한 Azure Resource Manager 템플릿 샘플입니다.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730199"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>SQL 인사이트에 대한 Resource Manager 템플릿 샘플
이 문서에는 Azure에서 실행 중인 SQL을 모니터링하기 위해 SQL 인사이트를 사용하도록 설정하는 샘플 [Azure Resource Manager 템플릿](../../azure-resource-manager/templates/template-syntax.md)이 포함되어 있습니다.  지원되는 SQL 제품 및 버전에 대한 자세한 내용은 [SQL 인사이트 설명서](sql-insights-overview.md)를 참조하세요. 각 샘플에는 템플릿 파일 및 템플릿에 제공할 샘플 값이 포함된 매개 변수 파일이 포함되어 있습니다.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>SQL 인사이트 모니터링 프로필 만들기
다음 샘플에서는 수집할 SQL 모니터링 데이터, 데이터 수집 빈도를 포함하며 데이터가 전송될 작업 영역을 지정하는 SQL 인사이트 모니터링 프로필을 만듭니다.


### <a name="template-file"></a>템플릿 파일

[GitHub의 템플릿 파일](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate)을 봅니다.

### <a name="parameter-file"></a>매개 변수 파일

[GitHub의 매개 변수 파일](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json)을 봅니다.


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>SQL 인사이트 모니터링 프로필에 모니터링 VM 추가
모니터링 프로필을 만든 후에는 해당 VM의 구성에 지정된 SQL 리소스에서 원격으로 데이터를 수집하도록 구성할 Azure 가상 머신을 할당해야 합니다.  자세한 내용은 SQL 인사이트 사용 설명서를 참조하세요.

다음은 지정된 SQL 리소스에서 데이터를 수집하도록 모니터링 VM을 구성하는 샘플입니다.


### <a name="template-file"></a>템플릿 파일

[GitHub의 템플릿 파일](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate)을 봅니다.

### <a name="parameter-file"></a>매개 변수 파일

[GitHub의 매개 변수 파일](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json)을 봅니다.


## <a name="create-an-alert-rule-for-sql-insights"></a>SQL 인사이트에 대한 경고 규칙 만들기
다음은 지정된 모니터링 프로필의 범위 내에 있는 SQL 리소스에 적용되는 경고 규칙을 만드는 샘플입니다.  이 경고 규칙은 경고 UI 컨텍스트 패널의 SQL 인사이트 UI에 표시됩니다.  

매개 변수 파일에는 SQL 인사이트에서 제공하는 경고 템플릿 중 하나의 값이 있으며, SQL에 대해 수집하는 다른 데이터에 대해 경고하도록 수정할 수 있습니다.  이 템플릿은 경고 규칙의 작업 그룹을 지정하지 않습니다.


#### <a name="template-file"></a>템플릿 파일

[GitHub의 템플릿 파일](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate)을 봅니다.

### <a name="parameter-file"></a>매개 변수 파일

[GitHub의 매개 변수 파일](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json)을 봅니다.





## <a name="next-steps"></a>다음 단계

* [Azure Monitor에 대한 다른 샘플 템플릿을 가져옵니다](../resource-manager-samples.md).
* [SQL 인사이트에 대해 자세히 알아봅니다](sql-insights-overview.md).
