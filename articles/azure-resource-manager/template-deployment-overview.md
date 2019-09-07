---
title: Azure 리소스 관리자 템플릿
description: 리소스 배포를 위해 Azure Resource Manager 템플릿을 사용 하는 방법을 설명 합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4f273a04322246a2b4112c0b5b8a062732bab555
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390748"
---
# <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

클라우드로 이동 하면 많은 팀에서 agile 개발 방법을 채택 했습니다. 이러한 팀은 빠르게 반복 됩니다. 해당 솔루션을 반복적으로 클라우드로 안정적으로 배포 해야 합니다. 팀은 단일 프로세스에서 인프라와 소스 코드를 관리 해야 하므로 작업 및 개발 간의 분할이 사라졌습니다.

이러한 문제에 대 한 한 가지 해결책은 배포를 자동화 하 고 인프라의 사례를 코드로 사용 하는 것입니다. 코드를 사용 하 여 배포 해야 하는 항목을 정의 하 고 소스 코드와 동일한 프로세스를 통해 해당 코드를 관리 합니다. 인프라를 원본 리포지토리의 코드로 저장 하 고 버전을 만듭니다.

Azure Resource Manager를 사용 하면 리소스 관리자 템플릿을 통해 코드로 인프라를 구현할 수 있습니다. 템플릿은 Azure 솔루션에 대 한 인프라 및 구성이 포함 된 JavaScript Object Notation (JSON) 파일입니다. 템플릿에서는 선언적 구문을 사용 합니다 .이 구문을 사용 하면 프로그래밍 명령의 시퀀스를 작성 하지 않고도 배포 하려는 항목의 상태를 지정할 수 있습니다. 템플릿에서 배포할 리소스 및 해당 리소스에 대 한 속성을 지정 합니다.

## <a name="benefits-of-resource-manager-templates"></a>리소스 관리자 템플릿의 이점

리소스 관리자 템플릿은 몇 가지 이점을 제공 합니다. 다음을 할 수 있습니다.

* 이러한 리소스를 개별적으로 처리 하는 대신, 솔루션에 대 한 모든 리소스를 그룹으로 배포, 관리 및 모니터링 합니다.

* 개발 수명 주기 내내 솔루션을 반복적으로 배포 하 고 리소스가 일관 된 상태로 배포 되도록 합니다.

* 스크립트 대신 선언적 템플릿을 통해 인프라를 관리 합니다.

* 리소스 간의 종속성을 정의 하 여 올바른 순서로 배포 합니다.

* 다른 당사자가 필요로 하는 중간 작업이 없기 때문에 새 버전 및 서비스를 즉시 활용 하세요.

## <a name="template-file"></a>템플릿 파일

템플릿 내에서 JSON의 기능을 확장 하는 [템플릿 식을](template-expressions.md) 작성할 수 있습니다. 이러한 식은 리소스 관리자에서 제공 하는 [함수](resource-group-template-functions.md) 를 사용 합니다.

템플릿에는 다음과 같은 섹션이 있습니다.

* [매개 변수](template-parameters.md) -배포 하는 동안 다른 환경에서 동일한 템플릿을 사용할 수 있도록 하는 값을 제공 합니다.

* [Variables](template-variables.md) -템플릿에 사용 되는 값을 정의 합니다.

* [사용자 정의 함수](template-user-defined-functions.md) -템플릿을 간소화 하는 사용자 지정 함수를 만듭니다.

* [리소스](resource-group-authoring-templates.md#resources) -배포할 리소스를 지정 합니다.

* [출력](template-outputs.md) -배포 된 리소스의 값을 반환 합니다.

## <a name="dependencies"></a>종속성

리소스가 올바른 순서로 생성되도록 Azure Resource Manager가 종속성을 분석합니다. 한 리소스가 다른 리소스(예: 디스크에 대한 스토리지 계정을 필요로 하는 가상 머신)의 값에 의존하는 경우 종속성을 설정합니다. 자세한 정보는 [Azure 리소스 관리자 템플릿에서 종속성 정의](resource-group-define-dependencies.md)를 참조하세요.

## <a name="conditional-deployment"></a>조건부 배포

템플릿에 리소스를 추가 하 고 필요에 따라 배포할 수 있습니다. 일반적으로 리소스를 배포 해야 하는지 여부를 나타내는 매개 변수 값을 전달 합니다. 자세한 내용은 [리소스 관리자 템플릿에서 조건부 배포](conditional-resource-deployment.md)를 참조 하세요.

## <a name="create-multiple-instances"></a>여러 인스턴스 만들기

템플릿에서 JSON 블록을 여러 번 반복 하는 대신 복사 요소를 사용 하 여 변수, 속성 또는 리소스의 인스턴스를 둘 이상 지정할 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿에서 리소스, 속성 또는 변수 반복](resource-group-create-multiple.md)을 참조 하세요.

## <a name="export-templates"></a>템플릿 내보내기

리소스 그룹의 현재 상태를 내보내거나 특정 배포에 사용 된 템플릿을 확인 하 여 기존 리소스 그룹에 대 한 템플릿을 가져올 수 있습니다. [내보낸 템플릿](export-template-portal.md)을 살펴보면 템플릿 구문에 대해 알아보는 데 도움이 됩니다.

포털에서 솔루션을 만들 때 자동으로 솔루션에 배포 템플릿을 포함합니다. 솔루션용 템플릿으로 시작하고 특정 요구 사항에 맞게 사용자 지정할 수 있기 때문에 서식 파일을 처음부터 새로 만들 필요가 없습니다. 샘플은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](./resource-manager-quickstart-create-templates-use-the-portal.md)를 참조하세요.

## <a name="template-deployment-process"></a>템플릿 배포 프로세스

템플릿을 배포 하는 경우 리소스 관리자는 템플릿을 구문 분석 하 고 해당 구문을 REST API 작업으로 변환 합니다. 예를 들어 리소스 관리자가 다음 리소스 정의로 템플릿을 받는 경우:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Microsoft.Storage 리소스 공급자에게 전송되는 다음 REST API 작업으로 정의를 변환합니다.

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

리소스가 이미 리소스 그룹에 있고 요청에 속성에 대 한 업데이트가 포함 되어 있지 않은 경우 아무 동작도 수행 되지 않습니다. 리소스가 존재 하지만 속성이 변경 된 경우 기존 리소스가 업데이트 됩니다. 리소스가 존재 하지 않는 경우 새 리소스가 생성 됩니다. 이 방법을 사용 하면 템플릿을 다시 배포 하 고 리소스가 일관 된 상태로 유지 되는 것을 알 수 있습니다.

## <a name="template-design"></a>템플릿 디자인

템플릿 및 리소스 그룹을 정의하는 방법은 사용자 및 솔루션을 관리하려는 방법에 전적으로 달려 있습니다. 예를 들어 단일 템플릿을 통해 3계층 애플리케이션을 단일 리소스 그룹에 배포할 수 있습니다.

![3계층 템플릿](./media/template-deployment-overview/3-tier-template.png)

그러나 단일 템플릿에서 전체 인프라를 정의할 필요가 없습니다. 대부분 배포 요구 사항을 대상, 목적에 특정 템플릿 집합으로 나누는 것이 좋습니다. 서로 다른 솔루션에 이러한 템플릿을 쉽게 다시 사용할 수 있습니다. 특정 솔루션을 배포하려면 모든 필수 템플릿에 연결하는 마스터 템플릿을 만듭니다. 다음 이미지는 세 개의 중첩된 템플릿을 포함하는 부모 템플릿을 통해 3계층 솔루션을 배포하는 방법을 보여 줍니다.

![중첩된 계층 템플릿](./media/template-deployment-overview/nested-tiers-template.png)

계층이 별도 수명 주기를 갖도록 계획하는 경우 3계층을 별도 리소스 그룹에 배포할 수 있습니다. 리소스는 다른 리소스 그룹의 리소스에 계속해서 연결될 수 있습니다.

![계층 템플릿](./media/template-deployment-overview/tier-templates.png)

중첩된 템플릿에 대한 자세한 내용은 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 템플릿 파일의 속성에 대 한 자세한 내용은 [Azure Resource Manager 템플릿 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조 하세요.
* 템플릿 개발을 시작 하려면 [Visual Studio Code를 사용 하 여 Azure Resource Manager 템플릿 만들기](resource-manager-tools-vs-code.md)를 참조 하세요.


