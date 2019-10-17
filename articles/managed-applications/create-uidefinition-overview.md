---
title: Azure 관리 되는 응용 프로그램의 만들기 환경에 대 한 CreateUiDefinition Microsoft Docs
description: Azure Portal에 대 한 사용자 인터페이스 정의를 만드는 방법을 설명 합니다. Azure Managed Applications를 정의할 때 사용 됩니다.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 7177b9513a1e51bc24672a69935a0e9430292537
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332697"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Azure 관리 되는 응용 프로그램의 만들기 환경을 위한 CreateUiDefinition

이 문서에서는 관리 되는 응용 프로그램을 만들 때 사용자 인터페이스를 정의 하는 데 사용 Azure Portal 하는 **Createuidefinition. json** 파일의 핵심 개념을 소개 합니다.

템플릿은 다음과 같습니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

CreateUiDefinition에는 항상 다음 세 가지 속성이 포함됩니다. 

* 처리기
* 버전
* parameters

처리기는 항상 0 @no__t 해야 하며, 지원 되는 최신 버전은-1 @no__t.

매개 변수 속성의 스키마는 지정된 처리기와 버전의 조합에 따라 다릅니다. 관리되는 애플리케이션의 경우 지원되는 속성은 `basics`, `steps` 및 `outputs`입니다. basics 및 steps 속성에는 Azure Portal에서 표시할 [요소](create-uidefinition-elements.md)(예: 텍스트 상자 및 드롭다운)가 포함됩니다. outputs 속성은 지정된 요소의 출력 값을 Azure Resource Manager 배포 템플릿의 매개 변수에 매핑하는 데 사용됩니다.

`$schema`를 포함하는 것이 좋지만 선택 사항입니다. 지정하는 경우 `version`의 값은 `$schema` URI 내의 버전과 일치해야 합니다.

JSON 편집기를 사용 하 여 createUiDefinition을 만든 다음 [Createuidefinition 샌드박스에서](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) 테스트 하 여 미리 볼 수 있습니다. 샌드박스에 대 한 자세한 내용은 [Azure Managed Applications에 대 한 포털 인터페이스 테스트](test-createuidefinition.md)를 참조 하세요.

## <a name="basics"></a>기본 사항

기본은 Azure Portal에서 파일을 구문 분석할 때 생성 되는 첫 번째 단계입니다. 포털에서는 `basics`에 지정된 요소를 표시할 뿐만 아니라 사용자가 배포에 대한 구독, 리소스 그룹 및 위치를 선택하기 위한 요소도 삽입합니다. 가능 하면 클러스터 또는 관리자 자격 증명의 이름과 같이 배포 차원 매개 변수를 쿼리 하는 요소는이 단계에서 수행 해야 합니다.

## <a name="steps"></a>단계

steps 속성에는 하나 이상의 요소가 포함된 각 basics 뒤에 표시하는 추가 steps가 0개 이상 포함될 수 있습니다. 배포할 애플리케이션의 역할별 또는 계층별로 단계를 추가하는 것이 좋습니다. 예를 들어 마스터 노드 입력에 대 한 단계와 클러스터의 작업자 노드에 대 한 단계를 추가 합니다.

## <a name="outputs"></a>outputs

Azure Portal에서는 `outputs` 속성을 사용하여 `basics` 및 `steps`의 요소를 Azure Resource Manager 배포 템플릿의 매개 변수에 매핑합니다. 이 사전의 키는 템플릿 매개 변수의 이름이며, 값은 참조되는 요소에 있는 출력 개체의 속성입니다.

관리되는 애플리케이션 리소스 이름을 설정하려면 출력 속성에 `applicationResourceName`이라는 값을 포함해야 합니다. 이 값을 설정 하지 않으면 응용 프로그램에서 이름에 GUID를 할당 합니다. 사용자 이름을 요청하는 사용자 인터페이스에 텍스트 상자를 포함할 수 있습니다.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>리소스 유형

배포할 리소스 유형을 지 원하는 위치로만 사용 가능한 위치를 필터링 하려면 리소스 형식의 배열을 제공 합니다. 둘 이상의 리소스 형식을 제공 하는 경우 모든 리소스 유형을 지 원하는 위치만 반환 됩니다. 이 속성은 선택 사항입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Functions

CreateUiDefinition은 요소의 입/출력 및 조건 등의 기능을 사용 하기 위한 [함수](create-uidefinition-functions.md) 를 제공 합니다. 이러한 함수는 템플릿 함수를 Azure Resource Manager 하는 구문과 기능에서 비슷합니다.

## <a name="next-steps"></a>다음 단계

createUiDefinition.json 파일 자체에는 간단한 스키마가 있습니다. 실제 수준의 모든 지원되는 요소와 기능을 통해 제공됩니다. 이러한 항목은 다음에 자세히 설명되어 있습니다.

- [요소](create-uidefinition-elements.md)
- [함수](create-uidefinition-functions.md)

createUiDefinition에 대한 현재 JSON 스키마는 https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json 에 사용할 수 있습니다.

예제 사용자 인터페이스 파일의 경우 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)을 참조하세요.
