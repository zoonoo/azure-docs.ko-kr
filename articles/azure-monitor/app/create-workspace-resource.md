---
title: 작업 영역 기반 리소스 Application Insights 새 Azure Monitor 만들기 | Microsoft Docs
description: 작업 영역 기반 리소스 Application Insights 새 Azure Monitor을 사용 하도록 설정 하는 데 필요한 단계에 대해 알아봅니다.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 65c2220bd3e9ea93e562b256e84796aed9046d00
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211409"
---
# <a name="workspace-based-application-insights-resources-preview"></a>작업 영역 기반 Application Insights 리소스 (미리 보기)

작업 영역 기반 리소스는 Application Insights와 Log Analytics 간에 완전 한 통합을 지원 합니다. 이제 Application Insights 원격 분석을 공통 Log Analytics 작업 영역으로 보낼 수 있습니다 .이를 통해 응용 프로그램, 인프라 및 플랫폼 로그를 통합 된 단일 위치에 유지 하면서 Log Analytics의 모든 기능에 액세스할 수 있습니다.

이를 통해 리소스에서 일반적인 RBAC (역할 기반 Access Control)를 사용할 수 있으며 앱 간/작업 영역 쿼리가 필요 하지 않습니다.

> [!NOTE]
> 작업 영역 기반 Application Insights 리소스에 대 한 데이터 수집 및 보존은 데이터가 있는 Log Analytics 작업 영역을 통해 청구 됩니다. 작업 영역 기반 Application Insights 리소스에 대 한 요금 청구에 대해 [자세히 알아보세요]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights) .

새 환경을 테스트 하려면 [Azure Portal](https://portal.azure.com)에 로그인 하 고 Application Insights 리소스를 만듭니다.

![작업 영역 기반 Application Insights 리소스](./media/create-workspace-resource/create-workspace-based.png)

기존 Log Analytics 작업 영역이 아직 없는 경우 [Log Analytics 작업 영역 만들기 설명서를 참조](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)하세요.

공개 미리 보기 **작업 영역 기반 리소스는 현재 미국 서 부 2, 미국 동부 및 미국 서 부로 제한 됩니다.**

리소스가 만들어지면 **개요** 창에 해당 작업 영역 정보가 표시 됩니다.

![작업 영역 이름](./media/create-workspace-resource/workspace-name.png)

파란색 링크 텍스트를 클릭 하면 새 통합 작업 영역 쿼리 환경을 활용할 수 있는 관련 Log Analytics 작업 영역으로 이동 합니다.

> [!NOTE]
> Application Insights 환경에서 Application Insights 클래식 리소스 쿼리, 통합 문서 및 로그 기반 경고에 대 한 완전 한 이전 버전과의 호환성을 계속 제공 합니다. [새 작업 영역 기반 테이블 구조/스키마](apm-tables.md) 를 쿼리/보려면 먼저 Log Analytics 작업 영역으로 이동 해야 합니다. 미리 보기 중에 Application Insights 창 내에서 **로그** 를 선택 하면 클래식 Application Insights 쿼리 환경에 액세스할 수 있습니다.

## <a name="copy-the-connection-string"></a>연결 문자열 복사

[연결 문자열]() 은 원격 분석 데이터를 연결 하려는 리소스를 식별 합니다. 또한 리소스가 원격 분석의 대상으로 사용 하는 끝점을 수정할 수 있습니다. 연결 문자열을 복사 하 여 응용 프로그램의 코드 또는 환경 변수에 추가 해야 합니다.

![계측 키를 클릭 하 고 복사 합니다.](./media/create-new-resource/instrumentation-key.png)

## <a name="monitoring-configuration"></a>모니터링 구성

작업 영역 기반 Application Insights 리소스를 만든 후에는 모니터링을 비교적 간단 하 게 구성할 수 있습니다.

### <a name="code-based-application-monitoring"></a>코드 기반 응용 프로그램 모니터링

코드 기반 응용 프로그램 모니터링의 경우 적절 한 Application Insights SDK를 설치 하 고, 새로 만든 리소스에 대 한 계측 키 또는 연결 문자열을 가리키도록 합니다.  

코드 기반 모니터링을 위해 Application Insights SDK를 설정 하는 방법에 대 한 자세한 설명서는 언어/프레임 워크 관련 설명서를 참조 하세요.

- [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
- [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
- [최신 콘솔 응용 프로그램 & 백그라운드 작업 (.NET/.NET Core)](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)
- [클래식 콘솔 응용 프로그램 (.NET)](https://docs.microsoft.com/azure/azure-monitor/app/console) 
- [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started?tabs=maven)
- [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
- [Node.JS](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)
- [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>코드 없는 모니터링 및 Visual Studio 리소스 만들기

Azure Functions 및 Azure 앱 Services와 같은 서비스의 코드 없는 모니터링을 위해 먼저 작업 영역 기반 Application Insights 리소스를 만든 다음 모니터링 구성 단계에서 해당 리소스를 가리켜야 합니다.

이러한 서비스는 자체 리소스 생성 프로세스 내에 새 Application Insights 리소스를 만드는 옵션을 제공 하지만 이러한 UI 옵션을 통해 만든 리소스는 현재 클래식 Application Insights 환경으로 제한 됩니다.

ASP.NET 및 ASP.NET Core에 대 한 Visual Studio의 Application Insights 리소스 생성 환경에도 동일 하 게 적용 됩니다. Visual Studio 모니터링 사용 UI를 사용 하 여에서 기존 작업 영역 기반 리소스를 선택 해야 합니다. Visual Studio 내에서 새 리소스 만들기를 선택 하면 클래식 Application Insights 리소스를 만드는 것이 제한 됩니다.

## <a name="creating-a-resource-automatically"></a>자동으로 리소스 만들기

### <a name="azure-cli"></a>Azure CLI

미리 보기 Application Insights Azure CLI 명령에 액세스 하려면 먼저 다음을 실행 해야 합니다.

```azurecli
 az extension add -n application-insights
```

명령을 실행 하지 않으면 `az extension add` 다음 오류 메시지가 표시 됩니다.`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

이제 다음을 실행 하 여 Application Insights 리소스를 만들 수 있습니다.

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>예제

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

이 명령에 대 한 전체 Azure CLI 설명서는 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)를 참조 하십시오.

### <a name="azure-powershell"></a>Azure PowerShell

`New-AzApplicationInsights`PowerShell 명령은 현재 작업 영역 기반 Application Insights 리소스 만들기를 지원 하지 않습니다. PowerShell을 사용 하 여 작업 영역 기반 리소스를 만들려면 아래 Azure Resource Manager 템플릿을 사용 하 고 PowerShell을 사용 하 여 배포할 수 있습니다.

### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

#### <a name="template-file"></a>템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>매개 변수 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>연결 된 작업 영역 수정

작업 영역 기반 Application Insights 리소스를 만든 후에는 연결 된 Log Analytics 작업 영역을 수정할 수 있습니다.

Application Insights 리소스 창에서 **속성**  >  **작업 영역**  >  **Log Analytics** 작업 영역 변경을 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색](../../azure-monitor/platform/metrics-charts.md)
* [분석 쿼리 작성](../../azure-monitor/app/analytics.md)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
