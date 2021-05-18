---
title: Azure Pipelines 및 Azure Application Insights를 사용한 DevOps 릴리스 파이프라인의 연속 모니터링 | Microsoft Docs
description: Application Insights를 사용하여 연속 모니터링을 빠르게 설정하기 위한 지침을 제공합니다.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: fd7cd6a107ed45adb60167a57661b60be5dc8212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86517130"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>릴리스 파이프라인에 연속 모니터링 추가

Azure Pipelines는 Azure Application Insights와 통합되어 소프트웨어 개발 수명 주기 내내 DevOps 릴리스 파이프라인을 지속적으로 모니터링할 수 있습니다. 

지속적으로 모니터링하여 릴리스 파이프라인이 Application Insights 및 기타 Azure 리소스의 모니터링 데이터를 통합할 수 있습니다. 릴리스 파이프라인이 Application Insights 경고를 감지하면 파이프라인은 경고가 해결될 때까지 배포를 제어하거나 롤백할 수 있습니다. 모든 검사에 통과하면 수동 개입 없이도 테스트에서 프로덕션까지 자동으로 진행할 수 있습니다. 

## <a name="configure-continuous-monitoring"></a>연속 모니터링 구성

1. [Azure DevOps](https://dev.azure.com)에서 조직 및 프로젝트를 선택합니다.
   
1. 프로젝트 페이지의 왼쪽 메뉴에서 **파이프라인** > **릴리스** 를 선택합니다. 
   
1. **새로 만들기** 옆에 있는 화살표를 드롭다운하고 **새 릴리스 파이프라인** 을 선택합니다. 또는 파이프라인이 아직 없는 경우 표시되는 페이지에서 **새 파이프라인** 을 선택합니다.
   
1. **템플릿 선택** 창에서 **연속 모니터링을 사용하여 Azure App Service 배포** 를 검색하고 선택한 후 **적용** 을 선택합니다. 

   ![새 Azure Pipelines 릴리스 파이프라인](media/continuous-monitoring/001.png)

1. **스테이지 1** 상자에서 **스테이지 작업 보기** 에 대한 하이퍼링크를 선택합니다.

   ![스테이지 작업 보기](media/continuous-monitoring/002.png)

1. **스테이지 1** 구성 창에서 다음 필드를 채워 넣습니다. 

    | 매개 변수        | 값 |
   | ------------- |:-----|
   | **스테이지 이름**      | 스테이지 이름을 제공하거나 그대로 **스테이지 1** 로 둡니다. |
   | **Azure 구독** | 드롭다운하여 사용하려는 연결된 Azure 구독을 선택합니다.|
   | **앱 유형** | 드롭다운하여 앱 유형을 선택합니다. |
   | **App Service 이름** | Azure App Service의 이름을 입력합니다. |
   | **Application Insights의 리소스 그룹 이름**    | 드롭다운하여 사용하려는 리소스 그룹을 선택합니다. |
   | **Application Insights 리소스 이름** | 드롭다운하여 선택한 리소스 그룹에 대한 Application Insights 리소스를 선택합니다.

1. 기본 경고 규칙 설정을 사용하여 파이프라인을 저장하려면 Azure DevOps 창의 오른쪽 위에 있는 **저장** 을 선택합니다. 설명을 입력하고 **확인** 을 선택합니다.

## <a name="modify-alert-rules&quot;></a>경고 규칙 수정

기본적으로 **연속 모니터링을 사용하여 Azure App Service 배포** 템플릿에는 **가용성**, **실패한 요청**, **서버 응답 시간** 및 **서버 예외** 의 네 가지 경고 규칙이 있습니다. 규칙을 추가하거나 서비스 수준 요구 사항에 맞게 규칙 설정을 변경할 수 있습니다. 

경고 규칙 설정을 수정하려면:

릴리스 파이프라인 페이지의 왼쪽 창에서 **Application Insights 경고 구성** 을 선택합니다.

인라인 스크립트를 통해 네 가지 기본 경고 규칙을 만듭니다.

```bash
$subscription = az account show --query &quot;id&quot;;$subscription.Trim(&quot;`&quot;");$resource="/subscriptions/$subscription/resourcegroups/"+"$(Parameters.AppInsightsResourceGroupName)"+"/providers/microsoft.insights/components/" + "$(Parameters.ApplicationInsightsResourceName)";
az monitor metrics alert create -n 'Availability_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg availabilityResults/availabilityPercentage < 99' --description "created from Azure DevOps";
az monitor metrics alert create -n 'FailedRequests_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count requests/failed > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerResponseTime_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg requests/duration > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerExceptions_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count exceptions/server > 5' --description "created from Azure DevOps";
```

스크립트를 수정하고 경고 규칙을 추가하거나, 경고 조건을 수정하거나, 배포 목적에 적합하지 않은 경고 규칙을 제거할 수 있습니다.

## <a name="add-deployment-conditions"></a>배포 조건 추가

릴리스 파이프라인에 배포 게이트를 추가하는 경우 설정된 임계값을 초과하는 경고는 원치 않는 릴리스 프로모션을 방지합니다. 경고를 해결하면 배포는 자동으로 진행됩니다.

배포 게이트를 추가하려면:

1. 기본 파이프라인 페이지의 **스테이지** 에서 연속 모니터링 게이트가 필요한 스테이지에 따라 **배포 전 조건** 또는 **배포 후 조건** 기호를 선택합니다.
   
   ![배포 전 조건](media/continuous-monitoring/004.png)
   
1. **배포 전 조건** 구성 창에서 **게이트** 를 **사용** 으로 설정합니다.
   
1. **배포 게이트** 옆의 **추가** 를 선택합니다.
   
1. 드롭다운 메뉴에서 **Azure Monitor 경고 쿼리** 를 선택합니다. 이 옵션을 사용하면 Azure Monitor 및 Application Insights 경고에 모두 액세스할 수 있습니다.
   
   ![Azure Monitor 경고 쿼리](media/continuous-monitoring/005.png)
   
1. **평가 옵션** 에서 **게이트 재평가 사이의 시간** 및 **게이트가 실패한 후의 시간 제한** 같은 설정에 대해 원하는 값을 입력합니다. 

## <a name="view-release-logs"></a>릴리스 로그 보기

릴리스 로그의 배포 게이트 동작 및 기타 릴리스 단계를 확인할 수 있습니다. 로그를 열려면:

1. 파이프라인 페이지의 왼쪽 메뉴에서 **릴리스** 를 선택합니다. 
   
1. 릴리스를 선택합니다. 
   
1. **스테이지** 에서 스테이지를 선택하여 릴리스 요약을 확인합니다. 
   
1. 로그를 보려면 릴리스 요약에서 **로그 보기** 를 선택하고, 스테이지에서 **성공** 또는 **실패** 하이퍼링크를 선택하거나 스테이지를 마우스로 가리키고 **로그** 를 선택합니다. 
   
   ![릴리스 로그 보기](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>다음 단계

Azure Pipelines에 대한 자세한 내용은 [Azure Pipelines 설명서](/azure/devops/pipelines)를 참조하세요.
