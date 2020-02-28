---
title: Azure Monitor 통합 문서 및 Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 통해 배포 된 미리 작성 되 고 사용자 지정 매개 변수화 된 Azure Monitor 통합 문서를 통해 복잡 한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658407"
---
# <a name="programmatically-manage-workbooks"></a>프로그래밍 방식으로 통합 문서 관리

리소스 소유자는 리소스 관리자 템플릿을 통해 프로그래밍 방식으로 통합 문서를 만들고 관리 하는 옵션을 사용할 수 있습니다. 

이는 다음과 같은 시나리오에서 유용할 수 있습니다.
* 리소스 배포와 함께 조직 또는 도메인별 분석 보고서를 배포 합니다. 예를 들어 새 앱 또는 가상 머신에 대 한 조직 특정 성능 및 실패 통합 문서를 배포할 수 있습니다.
* 기존 리소스에 대 한 통합 문서를 사용 하 여 표준 보고서 또는 대시보드 배포

통합 문서는 원하는 하위/리소스 그룹 및 리소스 관리자 템플릿에 지정 된 콘텐츠와 함께 생성 됩니다.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>통합 문서 배포를 위한 Azure Resource Manager 템플릿
1. 프로그래밍 방식으로 배포 하려는 통합 문서를 엽니다.
2. _편집_ 도구 모음 항목을 클릭 하 여 통합 문서를 편집 모드로 전환 합니다.
3. 도구 모음에서 _</>_ 단추를 사용 하 여 _고급 편집기_ 를 엽니다.
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
| `workbookType` | 통합 문서가 표시 되는 갤러리입니다. 지원 되는 값에는 통합 문서, `tsg`, Azure Monitor 등이 있습니다. |
| `workbookSourceId` | 통합 문서를 연결할 리소스 인스턴스의 ID입니다. 새 통합 문서는이 리소스 인스턴스와 관련 되어 표시 됩니다. 예를 들어 _통합 문서_에 있는 리소스의 콘텐츠 테이블에 있습니다. 통합 문서가 Azure Monitor의 통합 문서 갤러리에 표시 되도록 하려면 리소스 ID 대신 문자열 _Azure Monitor_ 를 사용 합니다. |
| `workbookId` | 이 통합 문서 인스턴스의 고유 guid입니다. _[Newguid ()]_ 를 사용 하 여 새 guid를 자동으로 만듭니다. |
| `kind` | 만든 통합 문서가 공유 인지 아니면 개인 문서 인지를 지정 하는 데 사용 됩니다. 공유 통합 문서에 공유 되는 값과 비공개 _사용자_ 에 대해 _공유_ 되는 값을 사용 합니다. |
| `location` | 통합 문서가 생성 되는 Azure 위치입니다. _[ResourceGroup (). location]_ 을 사용 하 여 리소스 그룹과 같은 위치에 만듭니다. |
| `serializedData` | 통합 문서에 사용할 콘텐츠나 페이로드를 포함 합니다. 통합 문서 UI의 리소스 관리자 템플릿을 사용 하 여 값을 가져옵니다. |

### <a name="workbook-types"></a>통합 문서 유형
통합 문서 유형 새 통합 문서 인스턴스가 표시 될 통합 문서 갤러리 유형을 지정 합니다. 옵션은 다음과 같습니다.

| Type | 갤러리 위치 |
| :------------- |:-------------|
| `workbook` | Application Insights, Azure Monitor 등의 통합 문서 갤러리를 비롯 하 여 대부분의 보고서에 사용 되는 기본값입니다.  |
| `tsg` | Application Insights 문제 해결 가이드 갤러리 |
| `usage` | _사용_ 중인 Application Insights의 _더 많은_ 갤러리 |

### <a name="limitations"></a>제한 사항
기술적인 이유로이 메커니즘을 사용 하 여 _Application Insights 통합 문서 갤러리에서_ 통합 문서 인스턴스를 만들 수 없습니다. 이 제한 사항을 해결 하기 위해 노력 하 고 있습니다. 한편, 문제 해결 가이드 갤러리 (통합 문서 유형: `tsg`)를 사용 하 여 관련 통합 문서 Application Insights를 배포 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

통합 문서를 사용 하 여 [저장소 환경을 위한 새 Azure Monitor](../insights/storage-insights-overview.md)를 활용 하는 방법을 알아봅니다.

