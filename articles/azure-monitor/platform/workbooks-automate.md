---
title: Azure Monitor 통합 문서 및 Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 통해 배포 된 미리 작성 되 고 사용자 지정 매개 변수화 된 Azure Monitor 통합 문서를 통해 복잡 한 보고 간소화
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 77190b85da08d09cf05a02dcc5787f0c24229948
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929730"
---
# <a name="programmatically-manage-workbooks"></a>프로그래밍 방식으로 통합 문서 관리

리소스 소유자는 리소스 관리자 템플릿을 통해 프로그래밍 방식으로 통합 문서를 만들고 관리 하는 옵션을 사용할 수 있습니다.

이는 다음과 같은 시나리오에서 유용할 수 있습니다.
* 리소스 배포와 함께 조직 또는 도메인별 분석 보고서를 배포 합니다. 예를 들어 새 앱 또는 가상 머신에 대 한 조직 특정 성능 및 실패 통합 문서를 배포할 수 있습니다.
* 기존 리소스에 대 한 통합 문서를 사용 하 여 표준 보고서 또는 대시보드 배포

통합 문서는 원하는 하위/리소스 그룹 및 리소스 관리자 템플릿에 지정 된 콘텐츠와 함께 생성 됩니다.

프로그래밍 방식으로 관리할 수 있는 두 가지 유형의 통합 문서 리소스는 다음과 같습니다.
* [통합 문서 템플릿](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [통합 문서 인스턴스](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>통합 문서 템플릿 배포를 위한 Azure Resource Manager 템플릿

1. 프로그래밍 방식으로 배포 하려는 통합 문서를 엽니다.
2. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
3. 도구 모음의 단추를 사용 하 여 _고급 편집기_ 를 엽니다 _</>_ .
4. _갤러리 템플릿_ 탭에 있는지 확인 합니다.

    ![갤러리 템플릿 탭](./media/workbooks-automate/gallery-template.png)
1. 갤러리 템플릿의 JSON을 클립보드에 복사 합니다.
2. 다음은 통합 문서 템플릿을 Azure Monitor 통합 문서 갤러리에 배포 하는 샘플 Azure Resource Manager 템플릿입니다. 대신 복사한 JSON을 붙여넣습니다 `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` . 통합 문서 템플릿을 만드는 참조 Azure Resource Manager 템플릿은 [여기](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)에서 찾을 수 있습니다.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. 개체에서 `galleries` `name` 및 `category` 키를 값으로 채웁니다. 다음 섹션에서 [매개 변수에](#parameters) 대해 자세히 알아보세요.
2. [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), [명령줄 인터페이스](../../azure-resource-manager/templates/deploy-cli.md), [PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)등을 사용 하 여이 Azure Resource Manager 템플릿을 배포 합니다.
3. Azure Portal를 열고 Azure Resource Manager 템플릿에서 선택한 통합 문서 갤러리로 이동 합니다. 예제 템플릿에서 Azure Monitor 통합 문서 갤러리로 이동 합니다.
    1. Azure Portal를 열고로 이동 Azure Monitor
    2. `Workbooks`목차에서 열기
    3. 갤러리의 범주 아래에서 템플릿을 찾습니다 `Deployed Templates` (자주색 항목 중 하나가 됨).

### <a name="parameters"></a>매개 변수

|매개 변수                |설명                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Azure Resource Manager에 있는 통합 문서 템플릿 리소스의 이름입니다.                                  |
|`type`                    | 항상 microsoft insights/통합 문서의 템플릿                                                            |
| `location`               | 통합 문서가 생성 될 Azure 위치입니다.                                               |
| `apiVersion`             | 2019-10-17 미리 보기                                                                                     |
| `type`                   | 항상 microsoft insights/통합 문서의 템플릿                                                            |
| `galleries`              | 이 통합 문서 템플릿을 표시할 갤러리 집합입니다.                                                |
| `gallery.name`           | 갤러리의 통합 문서 템플릿 이름입니다.                                             |
| `gallery.category`       | 템플릿을 저장할 갤러리의 그룹입니다.                                                     |
| `gallery.order`          | 갤러리의 범주 내에서 템플릿을 표시 하는 순서를 결정 하는 번호입니다. 낮은 순서는 더 높은 우선 순위를 나타냅니다. |
| `gallery.resourceType`   | 갤러리에 해당 하는 리소스 종류입니다. 일반적으로 리소스에 해당 하는 리소스 형식 문자열입니다 (예: operationalinsights/workspace). |
|`gallery.type`            | 통합 문서 유형 이라고 하는이는 리소스 유형 내에서 갤러리를 차별화 하는 고유 키입니다. 예를 들어 Application Insights에는 형식과 `workbook` `tsg` 다른 통합 문서 갤러리에 해당 하는이 있습니다. |

### <a name="galleries"></a>갤러리

| 갤러리                                        | 리소스 유형                                      | 통합 문서 유형 |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Azure Monitor의 통합 문서                     | `Azure Monitor`                                    | `workbook`    |
| Azure Monitor의 VM 정보                   | `Azure Monitor`                                    | `vm-insights` |
| Log analytics 작업 영역의 통합 문서           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Application Insights의 통합 문서              | `microsoft.insights/component`                     | `workbook`    |
| Application Insights 문제 해결 가이드 | `microsoft.insights/component`                     | `tsg`         |
| Application Insights에서 사용                  | `microsoft.insights/component`                     | `usage`       |
| Kubernetes service의 통합 문서                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| 리소스 그룹의 통합 문서                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Azure Active Directory의 통합 문서            | `microsoft.aadiam/tenant`                          | `workbook`    |
| 가상 컴퓨터의 VM 정보                | `microsoft.compute/virtualmachines`                | `insights`    |
| Virtual machine scale sets의 VM 정보      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>통합 문서 인스턴스를 배포 하기 위한 Azure Resource Manager 템플릿

1. 프로그래밍 방식으로 배포 하려는 통합 문서를 엽니다.
2. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
3. 도구 모음의 단추를 사용 하 여 _고급 편집기_ 를 엽니다 _</>_ .
4. 편집기에서 _템플릿 형식을_ _리소스 관리자 템플릿으로_전환 합니다.
5. 을 만들기 위한 리소스 관리자 템플릿은 편집기에 표시 됩니다. 콘텐츠를 복사 하 고 그대로 사용 하거나 대상 리소스를 배포 하는 더 큰 템플릿과 병합 합니다.

    ![통합 문서 UI 내에서 리소스 관리자 템플릿을 가져오는 방법을 보여 주는 이미지](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>샘플 Azure Resource Manager 템플릿
이 템플릿에서는 ' Hello World! '를 표시 하는 간단한 통합 문서를 배포 하는 방법을 보여 줍니다.
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>템플릿 매개 변수

| 매개 변수 | 설명 |
| :------------- |:-------------|
| `workbookDisplayName` | 갤러리 또는 저장 된 목록에 사용 되는 통합 문서의 이름입니다. 리소스 그룹 및 원본 범위에서 고유 해야 합니다. |
| `workbookType` | 통합 문서가 표시 되는 갤러리입니다. 지원 되는 값에는 통합 문서, `tsg` , Azure Monitor 등이 있습니다. |
| `workbookSourceId` | 통합 문서를 연결할 리소스 인스턴스의 ID입니다. 새 통합 문서는이 리소스 인스턴스와 관련 되어 표시 됩니다. 예를 들어 _통합 문서_에 있는 리소스의 콘텐츠 테이블에 있습니다. 통합 문서가 Azure Monitor의 통합 문서 갤러리에 표시 되도록 하려면 리소스 ID 대신 문자열 _Azure Monitor_ 를 사용 합니다. |
| `workbookId` | 이 통합 문서 인스턴스의 고유 guid입니다. _[Newguid ()]_ 를 사용 하 여 새 guid를 자동으로 만듭니다. |
| `kind` | 만든 통합 문서가 공유 인지 아니면 개인 문서 인지를 지정 하는 데 사용 됩니다. 공유 통합 문서에 공유 되는 값과 비공개 _사용자_ 에 대해 _공유_ 되는 값을 사용 합니다. |
| `location` | 통합 문서가 생성 되는 Azure 위치입니다. _[ResourceGroup (). location]_ 을 사용 하 여 리소스 그룹과 같은 위치에 만듭니다. |
| `serializedData` | 통합 문서에 사용할 콘텐츠나 페이로드를 포함 합니다. 통합 문서 UI의 리소스 관리자 템플릿을 사용 하 여 값을 가져옵니다. |

### <a name="workbook-types"></a>통합 문서 유형
통합 문서 유형 새 통합 문서 인스턴스가 표시 될 통합 문서 갤러리 유형을 지정 합니다. 다음 옵션을 사용할 수 있습니다.

| 유형 | 갤러리 위치 |
| :------------- |:-------------|
| `workbook` | Application Insights, Azure Monitor 등의 통합 문서 갤러리를 비롯 하 여 대부분의 보고서에 사용 되는 기본값입니다.  |
| `tsg` | Application Insights 문제 해결 가이드 갤러리 |
| `usage` | _사용_ 중인 Application Insights의 _더 많은_ 갤러리 |

### <a name="limitations"></a>제한 사항
기술적인 이유로이 메커니즘을 사용 하 여 _Application Insights 통합 문서 갤러리에서_ 통합 문서 인스턴스를 만들 수 없습니다. 이 제한 사항을 해결 하기 위해 노력 하 고 있습니다. 한편 문제 해결 가이드 갤러리 (통합 문서 유형: `tsg` )를 사용 하 여 Application Insights 관련 통합 문서를 배포 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

통합 문서를 사용 하 여 [저장소 환경을 위한 새 Azure Monitor](../insights/storage-insights-overview.md)를 활용 하는 방법을 알아봅니다.
