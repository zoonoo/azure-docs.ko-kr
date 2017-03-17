---
title: "타일 갤러리의 관련 리소스 및 연결된 리소스"
description: "Azure Preview 포털의 타일 갤러리에 표시되는 관련 리소스 및 연결된 리소스 대해 알아봅니다."
services: azure-portal
documentationcenter: 
author: adamabdelhamed
manager: wpickett
editor: 
ms.assetid: dba96d29-f518-49c8-bfd2-f14cecb44cbf
ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: efa7bce26c2c4c153b083e0e34d689a11d27dd16
ms.lasthandoff: 03/06/2017


---
# <a name="related-and-linked-resources-in-the-tile-gallery"></a>타일 갤러리의 관련 리소스 및 연결된 리소스
타일 갤러리를 사용하면 특정 리소스에 대한 타일을 찾아 현재 블레이드에 끌어 놓을 수 있습니다. 타일 갤러리를 사용하여 리소스에 걸쳐 있는 관리 뷰를 만들 수 있습니다. 지정된 리소스의 경우 관련 리소스에 해당 리소스 그룹의 모든 리소스 및 해당 리소스로/부터 연결되는 모든 리소스가 포함됩니다.

## <a name="linked-resources-in-resource-manager"></a>Resource Manager의 연결된 리소스
연결은 Resource Manager의 기능입니다.  동일한 리소스 그룹에 없다고 하더라도 리소스 간의 관계를 선언할 수 있습니다. 연결은 리소스의 런타임, 청구, 역할 기반 액세스에 전혀 영향을 주지 않습니다.  이는 단지 타일 갤러리 같은 도구가 풍부한 관리 경험을 제공할 수 있도록 관계를 나타내는 데 사용할 수 있는 메커니즘입니다.  도구는 링크 API를 사용하여 링크를 검사하고 사용자 지정 관계 관리 환경도 제공할 수 있습니다. 

## <a name="how-do-i-link-my-resources"></a>내 리소스를 연결하는 방법
포털을 통하거나 Azure PowerShell 또는 Azure CLI를 통해 템플릿을 배포하여 리소스를 만들 때 일부 종속된 리소스에 대해 링크가 자동으로 만들어집니다. [연결된 리소스 REST API](/rest/api/resources/resourcelinks)를 사용하여 프로그래밍 방식으로 리소스를 연결할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
* Resource Manager 템플릿 작성에 대한 소개를 보려면 [템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.
* 포털을 통해 리소스 그룹 작업에 대해 자세하게 파악하려면 [Azure Portal을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-portal.md)를 참조하세요.


