---
title: 기술 자산 Azure 컨테이너 이미지 만들기 | Azure Marketplace
description: Azure 컨테이너에 대한 기술 자산을 만듭니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: c639389fdd0d4624152fcdfa4432be09a18a97bc
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794334"
---
# <a name="prepare-your-container-technical-assets"></a>컨테이너 기술 자산 준비

이 문서에서는 Azure Marketplace에서 컨테이너 제품을 구성하기 위한 단계 및 요구 사항을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

빠른 시작, 자습서 및 샘플을 제공하는 [Azure Container Instances](https://docs.microsoft.com/azure/container-instances) 설명서를 검토하세요.

## <a name="fundamental-technical-knowledge"></a>기본 기술 지식

이러한 자산을 설계, 구축 및 테스트하려면 시간이 걸리고 Azure 플랫폼과 제안을 작성하는 데 사용되는 기술에 대한 기술적 지식이 모두 필요합니다.
 
엔지니어링 팀은 솔루션 영역 외에도 다음 Microsoft 기술에 대한 지식을 갖추고 있어야 합니다.

-   [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해 
-   [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
-   [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking)에 대한 실무 지식
-   [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 대한 실무 지식
-   [JSON](https://www.json.org/)에 대한 실무 지식

## <a name="suggested-tools"></a>권장되는 도구

컨테이너 이미지를 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

또한 개발 환경에 다음 도구를 추가하는 것이 좋습니다.

-   [Azure Storage 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   내선 번호: [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

또한 [ Azure 개발자 도구 ](https://azure.microsoft.com/tools/) 페이지에서 사용 가능한 도구를 검토하고, Visual Studio를 사용하는 경우 [ Visual Studio Marketplace ](https://marketplace.visualstudio.com/)를 검토하는 것이 좋습니다.

## <a name="create-the-container-image"></a>컨테이너 이미지 만들기

자세한 내용은 다음을 참조하세요.

* [자습서: Azure Container Instances에 배포에 대 한 컨테이너 이미지 만들기](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [자습서: 빌드 및 Azure 컨테이너 레지스트리 작업을 사용 하 여 클라우드에서 컨테이너 이미지 배포](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>다음 단계

[컨테이너 제품 만들기](./cpp-create-offer.md)
