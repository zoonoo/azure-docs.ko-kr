---
title: Azure container 제품에 대 한 기술 개념-Microsoft 상업적 marketplace
description: Azure Marketplace에서 컨테이너 제품을 구성 하는 데 도움이 되는 기술 리소스 및 지침입니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: b3c6f88df151cc497f0de670d5d78a05c7477459
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791887"
---
# <a name="create-an-azure-container-offer"></a>Azure 컨테이너 제안 만들기

> [!IMPORTANT]
> Azure container 제품의 관리를 Cloud 파트너 포털에서 파트너 센터로 전환 하 고 있습니다. 제품이 마이그레이션될 때까지 Cloud 파트너 포털에 대 한 [컨테이너 기술 자산 준비](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) 의 지침에 따라 제품을 관리 하세요.

이 문서에서는 Azure Marketplace에 대 한 컨테이너 제품을 만드는 데 도움이 되는 기술 리소스 및 권장 사항을 제공 합니다.

## <a name="before-you-begin"></a>시작하기 전에

빠른 시작, 자습서 및 샘플은 [Azure Container Instances 설명서](https://docs.microsoft.com/azure/container-instances)를 참조 하세요.

## <a name="fundamental-technical-knowledge"></a>기본 기술 지식

이러한 자산의 디자인, 빌드 및 테스트는 시간이 걸리고 제품을 빌드하는 데 사용 되는 Azure 플랫폼 및 기술에 대 한 기술 지식이 필요 합니다.

엔지니어링 팀은 솔루션 도메인 외에 다음과 같은 Microsoft 기술에 대해 알고 있어야 합니다.

- [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해
- [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)및 [azure 네트워킹](https://azure.microsoft.com/services/?filter=networking) 에 대 한 작업 지식
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 대한 실무 지식
- [JSON](https://www.json.org/)에 대 한 실무 지식.

## <a name="suggested-tools"></a>권장되는 도구

컨테이너 이미지를 관리 하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택 합니다.

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

개발 환경에 이러한 도구를 추가 하는 것이 좋습니다.

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 확장: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 확장: [JSON을 꾸밀](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)합니다.

[Azure 개발자 도구](https://azure.microsoft.com/) 페이지에서 사용 가능한 도구를 검토 합니다. Visual Studio를 사용 하는 경우 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)에서 사용할 수 있는 도구를 검토 합니다.

## <a name="create-the-container-image"></a>컨테이너 이미지 만들기

자세한 내용은 다음 자습서를 참조 하세요.

- [자습서: Azure Container Instances에 배포할 컨테이너 이미지 만들기](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [자습서: Azure Container Registry 작업을 사용 하 여 클라우드에서 컨테이너 이미지를 빌드하고 배포](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)합니다.

## <a name="next-steps"></a>다음 단계

- [컨테이너 제품을 만듭니다](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
