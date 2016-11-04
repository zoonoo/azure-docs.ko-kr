---
title: 타일 갤러리의 관련 리소스 및 연결된 리소스
description: Azure Preview 포털의 타일 갤러리에 표시되는 관련 리소스 및 연결된 리소스 대해 알아봅니다.
services: azure-portal
documentationcenter: ''
author: adamabdelhamed
manager: wpickett
editor: ''

ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab

---
# 타일 갤러리의 관련 리소스 및 연결된 리소스
타일 갤러리를 사용하면 특정 리소스에 대한 타일을 찾아 현재 블레이드에 끌어 놓을 수 있습니다. 타일 갤러리를 사용하여 리소스에 걸쳐 있는 관리 뷰를 만들 수 있습니다. 지정된 리소스의 경우 관련된 리소스는 해당 리소스와 동일한 리소스 그룹을 공유하는 모든 리소스 및 해당 리소스 양끝에 연결된 모든 리소스를 포함합니다.

## Azure 리소스 관리자의 연결된 리소스
연결은 Azure 리소스 관리자의 기능입니다. 동일한 리소스 그룹에 없다고 하더라도 리소스 간의 관계를 선언할 수 있습니다. 연결은 리소스의 런타임, 청구, 역할 기반 액세스에 전혀 영향을 주지 않습니다. 이는 단지 타일 갤러리 같은 도구가 풍부한 관리 경험을 제공할 수 있도록 관계를 나타내는 데 사용할 수 있는 메커니즘입니다. 도구는 링크 API를 사용하여 링크를 검사하고 사용자 지정 관계 관리 환경도 제공할 수 있습니다.

## 내 리소스를 연결하는 방법
포털을 통하거나 Azure PowerShell 또는 Azure CLI를 통해 템플릿을 배포하여 리소스를 만들 때 일부 종속된 리소스에 대해 링크가 자동으로 만들어집니다. [연결된 리소스 REST API](https://msdn.microsoft.com/library/azure/mt238499.aspx)를 사용하거나 템플릿에서 관계를 선언하여 프로그래밍 방식으로 리소스를 연결할 수도 있습니다. 연결된 리소스 작업에 대한 자세한 내용은 [Azure 리소스 관리자에서 리소스 연결](../resource-group-link-resources.md)을 참조하세요.

## 다음 단계
* Azure 리소스 관리자 템플릿 작성에 대한 소개를 보려면 [템플릿 작성](../resource-group-authoring-templates.md)을 참조하세요.
* 리소스 간의 링크를 만드는 방법에 대해 자세히 살펴보려면 [Azure 리소스 관리자에서 리소스 연결](../resource-group-link-resources.md)을 참조하세요.
* 미리 보기 포털을 통해 리소스 그룹 작업에 대해 자세하게 파악하려면 [Azure Preview 포털을 사용하여 Azure 리소스 관리](resource-group-portal.md)를 참조하세요.

<!---HONumber=Oct15_HO3-->