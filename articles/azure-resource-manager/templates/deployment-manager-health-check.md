---
title: 상태 통합 롤아웃 - Azure 배포 관리자
description: Azure 배포 관리자를 사용하여 여러 지역에 서비스를 배포하는 방법을 설명합니다. 모든 지역에 롤아웃하기 전에 배포의 안정성을 확인하는 안전한 배포 사례를 보여 줍니다.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273801"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Azure 배포 관리자에 상태 통합 롤아웃 소개(공개 미리 보기)

[Azure 배포 관리자를](./deployment-manager-overview.md) 사용하면 Azure 리소스 관리자 리소스의 단계적 롤아웃을 수행할 수 있습니다. 리소스는 지역별로 정렬된 방식으로 배포됩니다. Azure 배포 관리자의 통합 상태 확인은 롤아웃을 모니터링하고 문제가 있는 롤아웃을 자동으로 중지하여 문제를 해결하고 영향의 규모를 줄일 수 있습니다. 이 기능은 업데이트의 회귀로 인한 서비스 가용성을 줄일 수 있습니다.

## <a name="health-monitoring-providers"></a>건강 모니터링 제공자

상태를 최대한 쉽게 통합할 수 있도록, Microsoft에서는 업계 최고 수준의 여러 서비스 상태 모니터링 회사와 협력하여 고객의 시스템에 상태 확인을 통합할 수 있는 간단한 복사/붙여넣기 솔루션을 제공해 왔습니다. 아직 상태 모니터를 사용하지 않는 경우 다음과 같은 것부터 시작할 수 있는 훌륭한 솔루션입니다.

| ![azure 배포 관리자 상태 모니터 공급자 데이터개](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure 배포 관리자 상태 모니터 공급자 site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure 배포 관리자 상태 모니터 공급자 웨이브 프론트](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|최신 클라우드 환경을 위한 선도적인 모니터링 및 분석 플랫폼인 Datadog. [Datadog가 Azure 배포 관리자와 통합하는 방법을](https://www.datadoghq.com/azure-deployment-manager/)알아보십시오.|Site24x7, 올인원 프라이빗 및 퍼블릭 클라우드 서비스 모니터링 솔루션. [Site24x7이 Azure 배포 관리자와 통합되는 방법을](https://www.site24x7.com/azure/adm.html)알아보십시오.| 멀티 클라우드 애플리케이션 환경을 위한 모니터링 및 분석 플랫폼인 Wavefront. [Wavefront가 Azure 배포 관리자와 통합되는 방법을](https://go.wavefront.com/wavefront-adm/)알아보십시오.|

## <a name="how-service-health-is-determined"></a>서비스 상태 결정 방법

[상태 모니터링 공급자는](#health-monitoring-providers) 서비스를 모니터링하고 서비스 상태 문제를 경고하기 위한 몇 가지 메커니즘을 제공합니다. [Azure 모니터는](../../azure-monitor/overview.md) 이러한 오퍼링 중 하나의 예입니다. Azure Monitor는 특정 임계값을 초과할 때 경고를 만드는 데 사용할 수 있습니다. 예를 들어 서비스에 새 업데이트를 배포할 때 메모리 및 CPU 사용률이 예상 수준을 초과하여 급증합니다. 알림을 받으면 수정 작업을 수행할 수 있습니다.

이러한 의료 공급자는 일반적으로 REST API를 제공하므로 서비스 모니터의 상태를 프로그래밍 방식으로 검사할 수 있습니다. REST API는 간단한 정상/비정상 신호(HTTP 응답 코드에 의해 결정됨) 및/또는 수신하는 신호에 대한 자세한 정보로 돌아올 수 있습니다.

Azure 배포 관리자의 새 *healthCheck* 단계를 사용하면 정상 서비스를 나타내는 HTTP 코드를 선언하거나 보다 복잡한 REST 결과의 경우 일치하는 경우 정상 응답을 나타내는 정규식을 지정할 수도 있습니다.

Azure 배포 관리자 상태 확인을 사용하여 설치를 가져오는 흐름:

1. 선택한 의료 서비스 공급자를 통해 상태 모니터를 만듭니다.
1. 하나 이상의 상태 만들기 Azure 배포 관리자 롤아웃의 일부로 단계를 확인합니다. 다음 정보로 상태 확인 단계를 작성합니다.

    1. 상태 모니터에 대한 REST API에 대한 URI(상태 서비스 공급자가 정의한 대로).
    1. 인증 정보입니다. 현재 API 키 스타일 인증만 지원됩니다.
    1. [정상](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) 응답을 정의하는 HTTP 상태 코드 또는 정규식입니다. 정규 식을 제공할 수 있습니다., ALL 일치 해야 하는 응답에 대 한 정상으로 간주 될 수 있습니다., 또는 ANY 는 정상으로 간주 될 응답에 대 한 일치 해야 하는 식을 제공할 수 있습니다. 두 메서드가 모두 지원됩니다.

    다음은 다음과 같은 예입니다.

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

1. Azure 배포 관리자 롤아웃에서 적절한 시간에 상태 확인 단계를 호출합니다. 다음 예제에서는 **단계Group2의** **배포 후 단계에서** 상태 확인 단계가 호출됩니다.

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

예제를 살펴보려면 [자습서: Azure 배포 관리자에서 상태 확인](./deployment-manager-health-check.md)을 참조하십시오.

## <a name="phases-of-a-health-check"></a>상태 확인 단계

이 시점에서 Azure 배포 관리자는 서비스의 상태와 롤아웃에서 쿼리할 단계를 쿼리하는 방법을 알고 있습니다. 그러나 Azure 배포 관리자는 이러한 검사의 타이밍을 심층으로 구성할 수도 있습니다. healthCheck 단계는 구성 가능한 지속 시간이 있는 3개의 순차적 단계로 실행됩니다. 

1. Wait

    1. 배포 작업이 완료되면 VM이 재부팅되거나 새 데이터를 기반으로 다시 구성되거나 처음으로 시작될 수도 있습니다. 또한 서비스가 상태 모니터링 공급자가 유용한 것으로 집계하기 위해 상태 신호를 내보내는 데 에도 시간이 걸립니다. 이 격동의 프로세스 동안 업데이트가 아직 정상 상태에 도달하지 않았기 때문에 서비스 상태를 확인하는 것이 의미가 없을 수 있습니다. 실제로 리소스가 정착함에 따라 서비스가 정상 상태와 건강에 해로운 상태 사이에서 진동할 수 있습니다. 
    1. 대기 단계 중에는 서비스 상태를 모니터링하지 않습니다. 이는 배포된 리소스가 상태 확인 프로세스를 시작하기 전에 베이크할 시간을 허용하는 데 사용됩니다. 
1. Elastic

    1. 모든 경우에 자원이 안정되기 전에 구우기까지 걸리는 시간을 알 수 없기 때문에 Elastic 단계는 리소스가 잠재적으로 불안정할 수 있는 시기와 건강한 안정을 유지해야 하는 시점 사이의 유연한 기간을 허용합니다. 상태.
    1. 탄력적 단계가 시작되면 Azure 배포 관리자는 제공된 REST 끝점을 주기적으로 서비스 상태를 폴링하기 시작합니다. 폴링 간격을 구성할 수 있습니다. 
    1. 상태 모니터에 서비스가 비정상임을 나타내는 신호가 나타나면 이러한 신호가 무시되고 Elastic 단계가 계속되고 폴링이 계속됩니다. 
    1. 상태 모니터가 서비스가 정상임을 나타내는 신호와 함께 돌아오자마자 탄력성 위상이 종료되고 HealthyState 단계가 시작됩니다. 
    1. 따라서 탄력적 단계에 대해 지정된 기간은 정상 응답이 필수로 간주되기 전에 서비스 상태를 폴링하는 데 사용할 수 있는 최대 시간입니다. 
1. HealthyState

    1. HealthyState 단계 동안 서비스 상태는 탄력적 단계와 동일한 간격으로 계속 폴링됩니다. 
    1. 이 서비스는 지정된 전체 기간 동안 상태 모니터링 공급자의 정상 신호를 유지할 것으로 예상됩니다. 
    1. 언제든지 비정상 응답이 감지되면 Azure 배포 관리자는 전체 롤아웃을 중지하고 비정상 서비스 신호를 전달하는 REST 응답을 반환합니다.
    1. HealthyState 기간이 끝나면 healthCheck가 완료되고 배포가 다음 단계로 계속됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 배포 관리자에서 상태 모니터링을 통합하는 방법에 대해 배웠습니다. 다음 문서에서 계속해서 배포 관리자를 통한 배포 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: Azure 배포 관리자에서 상태 확인 통합](./deployment-manager-tutorial-health-check.md)