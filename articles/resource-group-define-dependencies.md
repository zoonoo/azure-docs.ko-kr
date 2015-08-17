<properties
   pageTitle="Azure 리소스 관리자 템플릿에서 종속성 정의"
   description="배포 중 다른 리소스에 종속된 것으로 리소스를 설정하는 방법에 대해 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="mmercuri"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2015"
   ms.author="mmercuri"/>

# Azure 리소스 관리자 템플릿에서 종속성 정의

지정된 리소스의 경우 토폴로지 성공에 매우 중요한 자식 종속성과 여러 업스트림이 있을 수 있습니다. **dependsOn** 및 **resources** 속성을 사용하여 다른 리소스에 대한 종속성을 정의할 수 있습니다. **reference** 함수를 사용하여 종속성을 지정할 수도 있습니다.

    {
        "name": "<name-of-the-resource>",
        "type": "<resource-provider-namespace/resource-type-name>",
        "apiVersion": "<supported-api-version-of-resource>",
        "location": "<location-of-resource>",
        "tags": { <name-value-pairs-for-resource-tagging> },
        "dependsOn": [ <array-of-related-resource-names> ],
        "properties": { <settings-for-the-resource> },
        "resources": { <dependent-resources> }
    }

 또한 리소스 간에 관계를 정의하고 리소스 그룹 전체에서 이러한 관계의 정의를 지원할 수 있는 리소스 링크가 있습니다.

## dependsOn

지정된 가상 컴퓨터에 대해 데이터베이스 리소스를 성공적으로 프로비전했는지 여부에 종속될 수 있습니다. 또 다른 경우, 클러스터 관리 도구를 사용하여 가상 컴퓨터를 배포하기 전에 설치할 클러스터의 여러 노드에 종속될 수도 있습니다.

템플릿 내에서 dependsOn 속성은 리소스에 대해 이러한 종속성을 정의하는 기능을 제공합니다. 값은 리소스 이름의 쉼표로 구분된 목록일 수 있습니다. 종속된 순서에 따라 리소스 간의 종속성이 평가되고 리소스가 배포됩니다. 리소스가 서로 종속되어 있지 않으면 병렬로 배포됩니다.

dependsOn을 사용하여 리소스 간의 종속성을 매핑하도록 할 수 있지만 배포의 성능에 영향을 줄 수 있으므로 해당 작업을 수행하는 이유를 이해하는 것이 중요합니다. 예를 들어 리소스가 상호 연결되는 방식을 문서화하려고 하기 때문에 이 작업을 수행 중인 경우, dependsOn은 올바른 접근 방법이 아닙니다. dependsOn의 수명 주기는 배포 동안이며 배포 후에는 사용할 수 없습니다. 배포된 후에는 이러한 종속성을 쿼리할 방법이 없습니다. dependsOn를 사용하면 실수로 배포 엔진이 병렬 처리를 수행하는 것을 방해하여 성능에 영향을 줄 위험이 있습니다. 리소스 간의 관계에 대해 문서화하고 쿼리 기능을 제공하려면 [리소스 연결](resource-group-link-resources.md)을 대신 사용해야 합니다.

참조 개체는 리소스에 대한 종속성을 내포하고 있기 때문에 reference 함수를 사용하여 리소스의 표현을 가져오는 경우 이 요소는 필요하지 않습니다. 사실 reference과 dependsOn을 사용하는 옵션이 있는 경우 reference 함수를 사용하여 암시적 참조가 되도록 하는 것을 권장합니다. 그 이유로는 다시 한번 더 성능을 들 수 있습니다. 참조는 템플릿 내에서 참조되므로 필요한 것으로 알려진 암시적 종속성을 정의합니다. 존재 여부에 따라 관련되어 성능이 최적화되지 못하게 하므로 배포 엔진의 병렬 처리를 쓸데 없이 방해하는 잠재적 위험을 피할 수 있습니다.

## 리소스

resources 속성을 사용하면 정의되는 리소스에 관련된 자식 리소스를 지정할 수 있습니다. 자식 리소스는 5개 수준 깊이까지만 정의할 있습니다. 자식 리소스 및 부모 리소스 간에 암시적 종속성은 생성되지 않습니다. 부모 리소스 다음에 자식 리소스를 배포해야 하는 경우 dependsOn 속성을 사용하여 해당 종속성을 확실하게 명시해야 합니다.

## reference 함수

reference 함수를 사용하면 식을 다른 JSON 이름 및 값 쌍 또는 런타임 리소스에서 해당 값을 파생시키는 식을 작성할 수 있습니다. 참조 식은 한 리소스가 다른 리소스에 종속되어 있음을 암시적으로 선언합니다. 아래의 **propertyPath**로 나타낸 속성은 선택적으로, 지정하지 않으면 참조는 리소스가 됩니다.

    reference('resourceName').propertyPath

이 요소 또는 dependsOn 요소를 사용하여 종속성을 지정할 수 있지만 같은 종속 리소스에 대해 두 가지를 모두 사용할 필요는 없습니다. 배포 엔진이 병렬로 배포 측면을 수행하지 못하게 하는 불필요한 dependsOn 요소가 실수로 생기는 위험을 방지할 수 있도록 암시적 참조를 사용하는 것이 좋습니다.

자세한 내용은 [reference 함수](../resource-group-template-functions/#reference)를 참조하세요.

## 다음 단계

- Azure 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요. 
- 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](resource-group-template-functions.md)를 참조하세요.

<!---HONumber=August15_HO6-->