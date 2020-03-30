---
title: Azure 모니터 통합 문서 및 Azure 리소스 관리자 템플릿
description: Azure 리소스 관리자 템플릿을 통해 배포된 미리 빌드되고 사용자 지정 매개 변수화된 Azure 모니터 통합 문서로 복잡한 보고 간소화
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658407"
---
# <a name="programmatically-manage-workbooks"></a>프로그래밍 방식으로 통합 문서 관리

리소스 소유자는 리소스 관리자 템플릿을 통해 프로그래밍 방식으로 통합 문서를 만들고 관리할 수 있습니다. 

이 기능은 다음과 같은 시나리오에서 유용할 수 있습니다.
* 리소스 배포와 함께 조직 또는 도메인별 분석 보고서를 배포합니다. 예를 들어 새 앱 또는 가상 컴퓨터에 조직별 성능 및 실패 통합 문서를 배포할 수 있습니다.
* 기존 리소스에 대한 통합 문서를 사용하여 표준 보고서 또는 대시보드 배포

통합 문서원하는 하위/리소스 그룹에서 리소스 관리자 템플릿에 지정된 콘텐츠와 함께 만들어집니다.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>통합 문서 배포를 위한 Azure 리소스 관리자 템플릿
1. 프로그래밍 방식으로 배포하려는 통합 문서를 엽니다.
2. 도구 모음 항목 _편집을_ 클릭하여 통합 문서를 편집 모드로 전환합니다.
3. 도구 모음의 단추를 _</>_ 사용하여 고급 _편집기열기._
4. 편집기에서 _템플릿 유형을_ _리소스 관리자 템플릿으로_전환합니다.
5. 만들기위한 리소스 관리자 템플릿은 편집기에 표시됩니다. 콘텐츠를 복사하여 있는 것처럼 사용하거나 대상 리소스를 배포하는 더 큰 템플릿과 병합합니다.

    ![통합 문서 UI 내에서 리소스 관리자 템플릿을 받는 방법을 보여 주면 이미지](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>샘플 Azure 리소스 관리자 템플릿
이 템플릿은 'Hello World!'를 표시하는 간단한 통합 문서를 배포하는 방법을 보여 주며
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
| `workbookDisplayName` | 갤러리 또는 저장된 목록에 사용되는 통합 문서의 친숙한 이름입니다. 리소스 그룹 및 소스 의 범위에서 고유해야 합니다. |
| `workbookType` | 통합 문서 아래에 표시될 갤러리입니다. 지원되는 값에는 `tsg`통합 문서, Azure 모니터 등이 포함됩니다. |
| `workbookSourceId` | 통합 문서를 연결할 리소스 인스턴스의 ID입니다. 새 통합 문서는 이 리소스 인스턴스와 관련된 것으로 표시됩니다(예: 통합 _문서_아래의 리소스 테이블) 통합 문서를 Azure Monitor의 통합 문서 갤러리에 표시하려면 리소스 ID 대신 _Azure Monitor_ 문자열을 사용합니다. |
| `workbookId` | 이 통합 문서 인스턴스에 대한 고유한 guid입니다. _[newGuid()]]를_ 사용하여 새 guid를 자동으로 만듭니다. |
| `kind` | 생성된 통합 문서를 공유하거나 비공개로 지정하는 데 사용됩니다. 공유 통합 문서및 개인 통합 문서의 _사용자에_ 대해 _공유된_ 값을 사용합니다. |
| `location` | 통합 문서를 만들 Azure 위치입니다. _[resourceGroup().location]을_ 사용하여 리소스 그룹과 동일한 위치에 만들 수 있습니다. |
| `serializedData` | 통합 문서에서 사용할 콘텐츠 또는 페이로드를 포함합니다. 통합 문서 UI의 리소스 관리자 템플릿을 사용하여 값을 가져옵니다. |

### <a name="workbook-types"></a>통합 문서 유형
통합 문서 유형은 새 통합 문서 인스턴스가 아래에 표시할 통합 문서 갤러리 유형을 지정합니다. 옵션은 다음과 같습니다.

| Type | 갤러리 위치 |
| :------------- |:-------------|
| `workbook` | 응용 프로그램 인사이트, Azure Monitor 등의 통합 문서 갤러리를 포함하여 대부분의 보고서에 사용되는 기본값입니다.  |
| `tsg` | 애플리케이션 인사이트 갤러리의 문제 해결 가이드 갤러리 |
| `usage` | 응용 프로그램 인사이트 _사용_ 에서 _더 많은_ 갤러리 |

### <a name="limitations"></a>제한 사항
기술적인 이유로 이 메커니즘은 응용 프로그램 인사이트 통합 _문서_ 갤러리에서 통합 문서 인스턴스를 만드는 데 사용할 수 없습니다. 우리는이 제한을 해결하기 위해 노력하고 있습니다. 한편 문제 해결 가이드 갤러리(통합 문서 유형: `tsg`)를 사용하여 응용 프로그램 인사이트 관련 통합 문서를 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

통합 문서를 사용하여 저장소 환경을 위한 새 [Azure 모니터에](../insights/storage-insights-overview.md)전원을 공급하는 방법을 살펴봅습니다.

