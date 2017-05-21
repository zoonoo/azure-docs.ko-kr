---
title: "Azure Managed Applications UI 정의 만들기에 대한 이해 | Microsoft Docs"
description: "Azure Managed Applications에 대한 UI 정의를 만드는 방법에 대해 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 176b891538f85c5638a2321561c3d8bd377d245b
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="getting-started-with-createuidefinition"></a>CreateUiDefinition 시작
이 문서에서는 관리되는 응용 프로그램을 만들기 위한 사용자 인터페이스를 생성하기 위해 Azure Portal에서 사용되는 CreateUiDefinition의 핵심 개념을 소개합니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition에는 항상 다음 세 가지 속성이 포함됩니다. 

* 처리기
* 버전
* 매개 변수

관리되는 응용 프로그램의 경우 처리기는 항상 `Microsoft.Compute.MultiVm`이어야 하며, 지원되는 최신 버전은 `0.1.2-preview`여야 합니다.

매개 변수 속성의 스키마는 지정된 처리기와 버전의 조합에 따라 다릅니다. 관리되는 응용 프로그램의 경우 지원되는 속성은 `basics`, `steps` 및 `outputs`입니다. basics 및 steps 속성에는 Azure Portal에서 표시할 _요소_(예: 텍스트 상자 및 드롭다운)가 포함됩니다. outputs 속성은 지정된 요소의 출력 값을 Azure Resource Manager 배포 템플릿의 매개 변수에 매핑하는 데 사용됩니다.

`$schema`를 포함하는 것이 좋지만 선택 사항입니다. 지정하는 경우 `version`의 값은 `$schema` URI 내의 버전과 일치해야 합니다.

## <a name="basics"></a>기본 사항
basics 단계는 항상 Azure Portal에서 CreateUiDefinition을 구문 분석할 때 생성되는 마법사의 첫 번째 단계입니다. 포털에서는 `basics`에 지정된 요소를 표시할 뿐만 아니라 사용자가 배포에 대한 구독, 리소스 그룹 및 위치를 선택하기 위한 요소도 삽입합니다. 일반적으로 클러스터 또는 관리자 자격 증명의 이름과 같은 배포 전체 매개 변수를 쿼리하는 요소는 이 단계에서 진행해야 합니다.

요소의 동작이 사용자의 구독, 리소스 그룹 또는 위치에 따라 다를 경우 해당 요소는 basics에 사용할 수 없습니다. 예를 들어 **Microsoft.Compute.SizeSelector**는 사용 가능한 크기의 목록을 결정하는 사용자의 구독 및 위치에 따라 다릅니다. 따라서 **Microsoft.Compute.SizeSelector**는 steps에만 사용할 수 있습니다. 일반적으로 **Microsoft.Common** 네임스페이스의 요소만 basics에 사용할 수 있습니다. 사용자의 컨텍스트에 종속되지 않는 다른 네임스페이스(예: **Microsoft.Compute.Credentials**)의 일부 요소는 여전히 허용됩니다.

## <a name="steps"></a>단계
steps 속성에는 하나 이상의 요소가 포함된 각 basics 뒤에 표시하는 추가 steps가 0개 이상 포함될 수 있습니다. 배포할 응용 프로그램의 역할별 또는 계층별로 steps를 추가하는 것이 좋습니다. 예를 들어 마스터 노드에 대한 입력 단계와 클러스터의 작업자 노드에 대한 단계를 추가합니다.

## <a name="outputs"></a>outputs
Azure Portal에서는 `outputs` 속성을 사용하여 `basics` 및 `steps`의 요소를 Azure Resource Manager 배포 템플릿의 매개 변수에 매핑합니다. 이 사전의 키는 템플릿 매개 변수의 이름이며, 값은 참조되는 요소에 있는 출력 개체의 속성입니다.

## <a name="functions"></a>함수
Azure Resource Manager의 [템플릿 함수](resource-group-template-functions.md)(구문과 기능 모두)와 마찬가지로, CreateUiDefinition은 요소의 입력 및 출력에서 사용하는 함수뿐만 아니라 조건부와 같은 기능도 제공합니다.

## <a name="next-steps"></a>다음 단계
CreateUiDefinition 자체에는 간단한 스키마가 있습니다. 이에 대한 심층적인 진정한 내용은 지원되는 모든 요소와 함수에서 비롯됩니다. 다음 문서에서는 놀라울 만큼 자세히 설명합니다.

- [요소](managed-application-createuidefinition-elements.md)
- [함수](managed-application-createuidefinition-functions.md)

CreateUiDefinition에 대한 현재 JSON 스키마는 https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json에서 사용할 수 있습니다. 

최신 버전도 동일한 위치에서 사용할 수 있습니다. URL의 `0.1.2-preview` 부분과 `version` 값을 사용하려는 버전 식별자로 바꿉니다. 현재 지원되는 버전 식별자는 `0.0.1-preview`, `0.1.0-preview`, `0.1.1-preview` 및 `0.1.2-preview`입니다.
