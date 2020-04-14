---
title: Azure 컨테이너 이미지 기술 자산 만들기 | Azure 마켓플레이스
description: Azure 컨테이너에 대한 기술 자산을 만듭니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 1116bd03cf0ef734b74b387ff8f3cff74b29b215
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270318"
---
# <a name="prepare-your-container-technical-assets"></a>컨테이너 기술 자산 준비

> [!IMPORTANT]
> 2020년 4월 13일부터 Azure 컨테이너 오퍼의 관리를 파트너 센터로 이전합니다. 마이그레이션 후 파트너 센터에서 오퍼를 만들고 관리합니다. Azure Container [기술 자산 준비의](https://aka.ms/CreateContainerTechAssets) 지침에 따라 마이그레이션된 오퍼를 관리합니다.

이 문서에서는 Azure Marketplace에서 컨테이너 제품을 구성하기 위한 단계 및 요구 사항을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

빠른 시작, 자습서 및 샘플을 제공하는 [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) 설명서를 검토하세요.

## <a name="fundamental-technical-knowledge"></a>기본 기술 지식

이러한 자산을 설계, 구축 및 테스트하려면 시간이 걸리고 Azure 플랫폼과 제안을 작성하는 데 사용되는 기술에 대한 기술적 지식이 모두 필요합니다.
 
엔지니어링 팀은 솔루션 영역 외에도 다음 Microsoft 기술에 대한 지식을 갖추고 있어야 합니다.

-    [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해 
-    [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
-    [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking)에 대한 실무 지식
-    [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 대한 실무 지식
-    [JSON](https://www.json.org/)에 대한 실무 지식

## <a name="suggested-tools"></a>권장되는 도구

컨테이너 이미지를 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.

-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

또한 개발 환경에 다음 도구를 추가하는 것이 좋습니다.

-    [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    확장: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

또한 [ Azure 개발자 도구 ](https://azure.microsoft.com/tools/) 페이지에서 사용 가능한 도구를 검토하고, Visual Studio를 사용하는 경우 [ Visual Studio Marketplace ](https://marketplace.visualstudio.com/)를 검토하는 것이 좋습니다.

## <a name="create-the-container-image"></a>컨테이너 이미지 만들기

자세한 내용은 다음을 참조하세요.

* [자습서: Azure 컨테이너 인스턴스에 배포할 컨테이너 이미지 만들기](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [자습서: Azure 컨테이너 레지스트리 작업을 통해 클라우드에서 컨테이너 이미지를 빌드하고 배포합니다.](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>다음 단계

[컨테이너 제품 만들기](./cpp-create-offer.md)
