---
title: Azure Deployment Manager를 상태 통합 출시 소개
description: Azure 배포 관리자를 사용하여 여러 지역에 서비스를 배포하는 방법을 설명합니다. 모든 지역에 롤아웃하기 전에 배포의 안정성을 확인하는 안전한 배포 사례를 보여 줍니다.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/19
ms.author: jgao
ms.openlocfilehash: 1006cc902cf4b6f763d86165a039a1fec5dc97a1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467080"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Azure Deployment Manager (공개 미리 보기)에 상태 통합 출시 소개

[Azure Deployment Manager](./deployment-manager-overview.md) Azure Resource Manager 리소스를 준비 된 배포를 수행할 수 있습니다. 리소스는 지역에서 지역 순서가 지정 된 방식으로 배포 됩니다. Azure Deployment Manager의 통합 된 상태 검사를 문제를 해결 하 고 영향의 규모를 줄일 수 있도록, 롤아웃 및 자동으로 중지 문제가 출시를 모니터링할 수 있습니다. 이 기능은 업데이트에 대 한 회귀를 야기 하는 서비스 사용 불가 줄일 수 있습니다.

## <a name="health-monitoring-providers"></a>상태 모니터링 공급자

상태 통합을 최대한 쉽게 하려면 상위 서비스 상태 모니터링 배포와 상태 검사를 통합 하는 간단한 복사/붙여넣기 솔루션을 제공 하는 회사의 일부를 사용 하 여 Microsoft 왔습니다. 상태 모니터를 사용 하 고 있지, 이러한 경우 훌륭한 솔루션 시작 하기:

| ![azure 배포 관리자 상태 모니터 공급자 datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure 배포 관리자 상태 모니터 공급자 site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure 배포 관리자 상태 모니터 공급자 wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, 선행 모니터링 및 최신 클라우드 환경에 대 한 분석 플랫폼입니다. 참조 [Datadog Azure Deployment Manager를 통합 하는 방법을](https://www.datadoghq.com/azure-deployment-manager/)합니다.|Site24x7를 하나로 모든 개인 및 공용 클라우드 서비스 모니터링 솔루션입니다. 참조 [Site24x7 Azure Deployment Manager를 통합 하는 방법을](https://www.site24x7.com/azure/adm.html)합니다.| Wavefront, 다중 클라우드 응용 프로그램 환경에 대 한 모니터링 및 분석 플랫폼입니다. 참조 [Wavefront Azure Deployment Manager를 통합 하는 방법을](https://go.wavefront.com/wavefront-adm/)합니다.|

## <a name="how-service-health-is-determined"></a>서비스 상태를 확인 하는 방법

[상태 모니터링 공급자](#health-monitoring-providers) 모니터링 서비스 및 서비스 상태 문제를 경고에 대 한 몇 가지 메커니즘을 제공 합니다. [Azure Monitor](/services/monitor/) 은 하나의 제품은의 예입니다. Azure Monitor 특정 임계값을 초과 하는 경우 경고를 만드는 데 사용할 수 있습니다. 예를 들어, 메모리 및 CPU 사용률에 서비스에 새 업데이트를 배포할 때 필요한 수준 외에도 급증 합니다. 알림을 받은 경우에 정정 작업을 수행할 수 있습니다.

이러한 상태 공급자는 일반적으로 서비스의 모니터의 상태를 프로그래밍 방식으로 검사할 수 있도록 REST Api를 제공 합니다. 하거나 REST Api (HTTP 응답 코드에 의해 결정 됨), 간단한 정상/비정상 신호를 사용 하 여 및/또는 수신 신호에 대 한 자세한 정보를 사용 하 여 다시 가져올 수 있습니다.

새 *healthCheck* 단계 Azure Deployment Manager를 사용 하면 정상 서비스를 나타내는 HTTP 코드를 선언할 수 있습니다 하거나, 더 복잡 한 나머지 결과 지정할 수 있습니다도 일치 하는 경우 올바른 표시 하는 정규식 응답입니다.

Azure Deployment Manager 상태 검사를 사용 하 여 설치를 시작 하는 흐름:

1. 원하는 상태 서비스 공급자를 통해 상태 모니터를 만듭니다.
1. Azure Deployment Manager 출시의 일부로 하나 이상의 healthCheck 단계를 만듭니다. 다음 정보를 사용 하 여 healthCheck 단계를 입력 합니다.

    1. 상태 모니터 (정의 됨 상태 서비스 공급자)에 대 한 REST API에 대 한 URI입니다.
    1. 인증 정보입니다. 현재 API 키 스타일 인증만 지원 됩니다.
    1. [HTTP 상태 코드](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) 또는 정상 응답을 정의 하는 정규식입니다. 정규식을 해야 하는 모든 정상 상태로 간주 되기 위해 응답에는 일치는 정상 상태로 간주에 대 한 응답에 대 한 일치 해야 하는 식을 제공할 수 있습니다를 제공할 수 있습니다 note 합니다. 두 방법 모두 지원 됩니다.

    다음 Json 예제입니다.

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Azure Deployment Manager 롤아웃에 적절 한 시간 healthCheck 단계를 호출 합니다. 다음 예제에서 상태 확인 단계에서 호출 됩니다 **postDeploymentSteps** 의 **stepGroup2**합니다.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

예제를 단계별로 참조 [자습서: Azure Deployment Manager에서 상태 검사를 사용 하 여](./deployment-manager-health-check.md)입니다.

## <a name="phases-of-a-health-check"></a>상태 검사 단계

이 시점에서 Azure Deployment Manager에는 새로운 고객의 롤아웃에 이렇게 하려면 단계 및 서비스의 상태를 쿼리 하는 방법을 알고 있습니다. 그러나 Azure Deployment Manager는 또한 이러한 검사 타이밍 심층 구성에 대 한 허용합니다. HealthCheck 단계는 모두 구성 가능한 기간 3 개의 순차적 단계로 실행 됩니다. 

1. 대기

    1. 배포 작업이 완료 된 후 Vm을 다시 부팅 될 수 있습니다, 그리고 다시 구성 또는 기준으로 새 데이터를 처음 시작 하기도 합니다. 또한 서비스 상태 모니터링에 유용한 공급자에 따라 집계 상태 신호를 내보내기에 대 한 시간이 걸립니다. Tumultuous이 과정에서 안정적인 상태를 아직 도달 하지 않은 업데이트 하므로 서비스 상태를 확인 하는 것이 경우가 없습니다 것. 실제로 서비스 리소스를 납부 하는 대로 정상 및 비정상 상태 간에 진동 수 있습니다. 
    1. 대기 단계에서는 서비스 상태 모니터링 되지 않습니다. 배포 된 리소스를 허용 하는이 상태 확인 프로세스를 시작 하기 전에 적용 하는 시간입니다. 
1. Elastic

    1. 리소스 해지기 전에 안정적이 고 탄력적 단계를 통해 요청 간에 유동 시간 동안 리소스의 잠재적으로 안정적이 지는 경우 및 정상 안정적인 유지 관리 하는 데 필요한 경우 이러한 통합 걸립니다 기간 모든 경우에 알 수 없으므로 상태입니다.
    1. 탄력적 단계에서 시작 되 면 Azure Deployment Manager는 서비스 상태에 대 한 제공 된 REST 끝점을 주기적으로 폴링 시작 합니다. 폴링 간격은 구성할 수 있습니다. 
    1. 상태 모니터는 신호를 사용 하 여 반환 되는 서비스가 정상이 아님을 나타내는 이러한 신호는 무시 탄력적 단계는 다음 작업을 계속 해 서을 고 폴링 계속 됩니다. 
    1. 상태 모니터를 서비스가 정상 임을 나타내는 신호를 사용 하 여 복구 하는 즉시 탄력적 단계 종료 되 고 HealthyState 단계를 시작 합니다. 
    1. 따라서 탄력적 단계에 대 한 지정 된 기간은 필수 정상 응답으로 간주 되기 전에 서비스 상태에 대 한 폴링 소요 될 수 있는 최대 기간입니다. 
1. HealthyState

    1. HealthyState 단계에서는 서비스 상태는 지속적으로 탄력적 단계로 동일한 간격으로 폴링됩니다. 
    1. 서비스는 지정된 된 전체 기간에 대 한 상태 모니터링 공급자에서 정상 상태 신호를 유지 관리 해야 합니다. 
    1. 언제 든 지 응답을 비정상 감지 되 면 Azure Deployment Manager는 전체 롤아웃을 중지 되며 비정상 서비스 신호를 운반 REST 응답을 반환 합니다.
    1. HealthyState 기간이 종료 된 후의 healthCheck 완료 되며 배포 다음 단계를 계속 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 상태 모니터링에 Azure Deployment Manager를 통합 하는 방법에 대해 알아보았습니다. 다음 문서에서 계속해서 배포 관리자를 통한 배포 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: Azure 배포 관리자에서 상태 검사를 통합 합니다.](./deployment-manager-tutorial-health-check.md)