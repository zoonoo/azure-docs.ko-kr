---
title: Azure 컨테이너 제품에 대한 기술 개념 - Microsoft 상업용 Marketplace
description: Azure Marketplace에서 컨테이너 제품을 구성하는 데 도움이 되는 기술 리소스 및 지침입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/09/2020
ms.openlocfilehash: 3599052e49b91747580bf506f06ebfb8d2e34423
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346811"
---
# <a name="create-an-azure-container-offer"></a>Azure 컨테이너 제품 만들기

이 문서에서는 Azure Marketplace에서 컨테이너 제품을 만드는 데 도움이 되는 기술 리소스 및 권장 사항을 제공합니다.

## <a name="before-you-begin"></a>시작하기 전에

빠른 시작, 자습서 및 샘플에 대해서는 [Azure Container Instances 설명서](../../container-instances/index.yml)를 참조하세요.

## <a name="fundamental-technical-knowledge"></a>기본 기술 지식

이러한 자산을 설계, 구축 및 테스트하려면 시간이 걸리고 Azure 플랫폼과 제안을 작성하는 데 사용되는 기술에 대한 기술적 지식이 모두 필요합니다.

엔지니어링 팀은 솔루션 영역 외에도 다음 Microsoft 기술에 대한 지식을 갖추고 있어야 합니다.

- [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해
- [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking)에 대한 실무 지식
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 대한 실무 지식
- [JSON](https://www.json.org/)에 대한 실무 지식

## <a name="suggested-tools"></a>권장되는 도구

컨테이너 이미지를 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure/)

개발 환경에 다음 도구를 추가하는 것이 좋습니다.

- [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 확장: [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

[Azure 개발자 도구](https://azure.microsoft.com/) 페이지에서 사용 가능한 도구를 검토합니다. Visual Studio를 사용하는 경우 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)에서 사용할 수 있는 도구를 검토합니다.

## <a name="create-the-container-image"></a>컨테이너 이미지 만들기

자세한 내용은 다음 자습서를 참조하세요.

- [자습서: Azure Container Instances에 배포할 컨테이너 이미지 만들기](../../container-instances/container-instances-tutorial-prepare-app.md)
- [자습서: Azure Container Registry 작업을 사용하여 클라우드에 컨테이너 이미지 빌드 및 배포](../../container-registry/container-registry-tutorial-quick-task.md)

## <a name="next-steps"></a>다음 단계

- [컨테이너 제품 만들기](create-azure-container-offer.md)