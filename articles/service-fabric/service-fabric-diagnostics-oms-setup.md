---
title: "Azure Service Fabric - OMS Log Analytics로 모니터링 설정 | Microsoft Docs"
description: "Azure Service Fabric 클러스터의 모니터링을 위해 OMS를 설정하여 이벤트를 시각화 및 분석하는 방법에 대해 알아봅니다."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 32c09f06cea97024437e7e339407d344194a14ae
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>클러스터에 대해 OMS Log Analytics 설정

Azure Resource Manager를 통해 또는 Azure Marketplace에서 OMS 작업 영역을 설정할 수 있습니다. 나중에 사용할 수 있도록 배포 템플릿을 유지 관리하려는 경우에는 전자를 사용합니다. 진단이 사용하도록 설정된 상태에서 클러스터를 이미 배포한 경우 Marketplace를 통한 배포가 더 쉽습니다.

> [!NOTE]
> 클러스터를 성공적으로 모니터링하도록 OMS를 설정할 수 있으려면 클러스터에서 클러스터/플랫폼 수준 이벤트를 볼 수 있도록 진단을 사용하도록 설정해야 합니다.

## <a name="deploying-oms-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 OMS 배포

Resource Manager 템플릿을 사용하여 클러스터를 배포할 때 템플릿은 새 OMS 작업 영역을 만들고 여기에 Service Fabric 솔루션을 추가하고 적절한 저장소 테이블에서 데이터를 읽도록 구성해야 합니다.

샘플 템플릿은 [여기](https://azure.microsoft.com/resources/templates/service-fabric-oms/)에 있으며 요구 사항에 따라 사용하고 수정할 수 있습니다. OMS 작업 영역 설정을 위한 다양한 옵션을 제공하는 더 많은 템플릿은 [Service Fabric 및 OMS 템플릿](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS)에서 확인할 수 있습니다.

주요 변경 내용은 다음과 같습니다.

1. 매개 변수에 `omsWorkspaceName` 및 `omsRegion`을 추가합니다. 즉, *template.json* 파일에 정의된 매개 변수에 다음 코드 조각을 추가합니다. 기본값을 원하는 대로 자유롭게 수정합니다. *parameters.json*에 새로운 두 개의 매개 변수를 추가하여 리소스 배포를 위한 해당 값을 정의해야 합니다.
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    `omsRegion` 값은 특정 값 집합을 준수해야 합니다. 클러스터 배포에 가장 가까운 것을 선택해야 합니다.

2. 응용 프로그램 로그를 OMS로 보내려면 `applicationDiagnosticsStorageAccountType` 및 `applicationDiagnosticsStorageAccountName`이 템플릿에 매개 변수로 포함되어 있는지 확인하십시오. 그렇지 않은 경우 변수 섹션에 추가하고 필요에 따라 값을 편집합니다. 위에서 사용된 형식을 원할 경우 매개 변수로 포함할 수도 있습니다.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]",
    ```

3. Service Fabric OMS 솔루션을 템플릿의 변수에 추가합니다.

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric",
    ```

4. Service Fabric 클러스터 리소스가 선언된 후 리소스 섹션의 끝에 다음을 추가합니다.

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```

5. 클러스터에 대한 Resource Manager 업그레이드로 템플릿을 배포합니다. 이 작업은 AzureRM PowerShell 모듈에서 `New-AzureRmResourceGroupDeployment` API를 사용하여 수행합니다. 예제 명령은 다음과 같습니다.

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager는 이것이 기존 리소스에 대한 업데이트임을 감지할 수 있게 됩니다. 기존 배포를 구동하는 템플릿과 제공된 새 템플릿 사이의 변경 사항만 처리합니다.

## <a name="deploying-oms-using-azure-marketplace"></a>Azure Marketplace를 사용하여 OMS 배포

클러스터를 배포한 후에 OMS 작업 영역을 추가하려면 Azure Marketplace(포털에서)로 이동하고 *"Service Fabric 분석"*을 검색합니다.

1. 왼쪽 탐색 메뉴에서 **새로 만들기**를 클릭합니다. 

2. *Service Fabric 분석*을 검색합니다. 표시되는 리소스를 클릭합니다.

3. **만들기**를 클릭합니다.

    ![Marketplace의 OMS SF 분석](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Service Fabric 분석 만들기 창에서 *OMS 작업 영역* 필드에 대해 **작업 영역 선택**, **새 작업 영역 만들기**를 차례로 클릭합니다. 필요한 항목을 입력합니다. 단, Service Fabric 클러스터와 OMS 작업 영역에 대한 구독이 동일해야 합니다. 입력 항목의 유효성이 검사되면 OMS 작업 영역이 배포되기 시작하며, 몇 분밖에 안 걸립니다.

5. 완료되면 Service Fabric 분석 만들기 창 맨 아래에서 **만들기**를 다시 클릭합니다. *OMS 작업 영역* 아래에 새 작업 영역이 표시되는지 확인합니다. 이렇게 하면 방금 만든 작업 영역에 이 솔루션이 추가됩니다.

6. 클러스터에서 가져온 진단 데이터에 해당 작업 영역을 연결해야 합니다. Service Fabric 분석 솔루션을 만든 리소스 그룹으로 이동합니다. *ServiceFabric(\<nameOfOMSWorkspace\>)*이 보입니다. 솔루션을 클릭하여 솔루션 설정, 작업 영역 설정을 변경할 수 있는 개요 페이지로 이동한 다음, OMS 포털로 이동합니다.

7. 왼쪽 탐색 메뉴에서 *작업 영역 데이터 원본*의 **저장소 계정 로그**를 클릭합니다.

8. *저장소 계정 로그* 페이지에서 맨 위의 **추가**를 클릭하여 작업 영역에 클러스터 로그를 추가합니다.

9. **저장소 계정**을 클릭하여 클러스터에서 만든 적절한 계정을 추가합니다. 기본 이름을 사용한 경우 저장소 계정의 이름은 *sfdg\<resourceGroupName\>*으로 지정됩니다. 또한, 클러스터를 배포하기 위해 사용한 Azure Resource Manager 템플릿을 확인하고, `applicationDiagnosticsStorageAccountName`에 사용한 값을 확인하여 확인할 수도 있습니다. 이름이 표시되지 않을 경우 아래로 스크롤하여 **추가 로드**를 클릭해야 할 수도 있습니다. 오른쪽 저장소 계정 이름을 클릭하여 선택합니다.

10. 다음으로, **Service Fabric 이벤트**로 설정될 *데이터 형식*을 지정해야 합니다.

11. *원본*은 *WADServiceFabric\*EventTable*로 자동 설정되어야 합니다.

12. **확인**을 클릭하여 작업 영역을 클러스터 로그에 연결합니다.

    ![OMS에 저장소 계정 로그 추가](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

이제 계정이 작업 영역의 데이터 원본에서 *저장소 계정 로그*의 일부로 표시됩니다.

이를 통해 이제 OMS Log Analytics 작업 영역에서 Service Fabric 분석 솔루션을 추가했으며, 이제는 클러스터의 플랫폼 및 응용 프로그램 로그 표에 제대로 연결됩니다. 같은 방식으로 작업 영역에 추가적인 원본을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 노드에 [OMS 에이전트를 배포](service-fabric-diagnostics-oms-agent.md)하여 성능 카운터를 수집하고 컨테이너에 대한 docker 통계 및 로그를 수집합니다.
* Log Analytics의 일부로 제공되는 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) 기능 알아보기
* [뷰 디자이너를 사용하여 Log Analytics에서 사용자 지정 보기 만들기](../log-analytics/log-analytics-view-designer.md)
