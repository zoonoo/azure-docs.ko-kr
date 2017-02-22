---
title: "Azure 솔루션에서 관련 리소스 연결 | Microsoft Docs"
description: "Azure Resource Manager에서 다른 리소스 그룹의 관련 리소스 간에 링크를 만듭니다."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0738d072-c093-4cf1-a790-de13025d9d60
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 2750e971ae281e498e9c505e1cd2790cefbfbc78


---
# <a name="link-related-resources-from-different-resource-groups"></a>다른 리소스 그룹의 관련 리소스 연결
배포 중 다른 리소스에 종속적인 것으로 리소스를 표시할 수 있지만 배포 시 라이프사이클은 종료됩니다. 배포 후에는 종속 리소스 간에 관계가 식별되지 않습니다. Resource Manager는 리소스 간에 지속적인 관계를 설정하기 위해 리소스 연결이라는 기능을 제공합니다.

리소스 연결을 통해 리소스 그룹에 걸쳐 있는 관계를 문서화할 수 있습니다. 예를 들어 자체 수명 주기를 갖는 데이터베이스를 하나의 리소스 그룹에 두고 다른 주기를 갖는 앱을 다른 리소스 그룹에 두는 것이 일반적입니다. 응용 프로그램은 응용 프로그램과 데이터베이스 간 연결을 표시하도록 데이터베이스에 연결합니다. 

연결된 모든 리소스는 동일한 구독에 속해야 합니다. 각 리소스는 다른 50개의 리소스에 연결될 수 있습니다. 관련된 리소스 쿼리는 REST API를 통해서만 가능합니다. 연결된 리소스 중 하나가 삭제되거나 이동되면 링크 소유자는 나머지 링크를 청정리해야 합니다. 다른 리소스에 연결된 리소스를 삭제하는 경우 경고가 나타나지 **않습니다** .

## <a name="linking-in-templates"></a>템플릿 안에서 연결
템플릿 안에서 연결을 정의하기 위해 **/providers/links**와 리소스 공급자 네임스페이스 및 원본 리소스 형식을 통합한 리소스 형식을 포함합니다. 이름은 원본 리소스의 이름을 포함해야 합니다. 대상 리소스의 리소스 ID를 제공 합니다. 다음 예제에서는 웹 사이트와 저장소 계정 간 연결을 설정합니다.

    {
      "type": "Microsoft.Web/sites/providers/links",
      "apiVersion": "2015-01-01",
      "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
      "dependsOn": [ "[variables('siteName')]" ],
      "properties": {
        "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
        "notes": "This web site uses the storage account to store user information."
      }
    }


템플릿 포맷에 대한 전체 설명은 [리소스 연결 - 템플릿 스키마](resource-manager-template-links.md)를 참조하십시오.

## <a name="linking-with-rest-api"></a>REST API를 사용하여 연결
배포된 리소스 간 링크를 정의하려면 다음을 실행합니다.

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

{subscription-id}를 구독 ID로 바꿉니다. {resource-group}, {provider-namespace, {resource-type} 및 {resource-name}을 링크의 첫 번째 리소스를 식별하는 값으로 바꿉니다. {link-name}을 만들려는 링크의 이름으로 바꿉니다. api-version으로 2015-01-01을 사용합니다.

요청에서 다음과 같이 링크의 두 번째 리소스를 정의하는 개체를 포함합니다.

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

properties 요소는 두 번째 리소스에 대한 식별자를 포함합니다.

다음 구독에 대한 연결을 쿼리할 수 있습니다.

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

링크에 대한 정보를 검색하는 방법을 비롯한 추가 예제를 보려면 [연결된 리소스](https://docs.microsoft.com/rest/api/resources/resourcelinks)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* 태그를 사용하여 리소스를 구성할 수도 있습니다. 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](resource-group-using-tags.md)을 참조하세요.
* 템플릿을 만들고 배포할 리소스를 정의하는 방법에 대한 설명을 보려면 [템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.




<!--HONumber=Jan17_HO4-->


