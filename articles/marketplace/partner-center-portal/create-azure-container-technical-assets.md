---
title: Azure 컨테이너 기술 자산 준비
description: 이 문서에서는 Azure Marketplace에서 컨테이너 오퍼를 구성하기 위한 단계 및 요구 사항에 대해 설명합니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 62c77681accb19183b5e8aa3ae8d80c225b2d284
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266020"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Azure 컨테이너 기술 자산 준비

> [!IMPORTANT]
> Azure 컨테이너 오퍼의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 클라우드 파트너 포털에 대한 [컨테이너 기술 자산 준비의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) 지침을 따라 오퍼를 관리하십시오.

이 문서에서는 Azure Marketplace에서 컨테이너 오퍼를 구성하기 위한 단계 및 요구 사항에 대해 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

빠른 시작, 자습서 및 샘플에 대 한 [Azure 컨테이너 인스턴스를](https://docs.microsoft.com/azure/container-instances)참조 합니다.

## <a name="fundamental-technical-knowledge"></a>기본 기술 지식

이러한 자산을 디자인, 빌드 및 테스트하려면 시간이 걸리며 Azure 플랫폼과 오퍼를 빌드하는 데 사용되는 기술에 대한 기술 지식이 필요합니다.

솔루션 도메인 외에도 엔지니어링 팀은 다음과 같은 Microsoft 기술에 대한 지식을 가지고 있어야 합니다.

- [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해
- [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
- [Azure 가상 컴퓨터,](https://azure.microsoft.com/services/virtual-machines/) [Azure 저장소](https://azure.microsoft.com/services/?filter=storage)및 Azure [네트워킹에](https://azure.microsoft.com/services/?filter=networking) 대한 작업 지식
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 대한 실무 지식
- [JSON의](https://www.json.org/)작업 지식 .

## <a name="suggested-tools"></a>권장되는 도구

컨테이너 이미지를 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

개발 환경에 다음 도구를 추가하는 것이 좋습니다.

- [Azure Storage Explorer](https://aka.ms/GetStartedWithStorageExplorer)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 확장: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 확장 : [예쁜 JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Azure 개발자 도구 페이지에서 사용 가능한 도구를 [검토합니다.](https://azure.microsoft.com/) Visual Studio를 사용하는 경우 [Visual Studio 마켓플레이스에서](https://marketplace.visualstudio.com/)사용할 수 있는 도구를 검토합니다.

## <a name="create-the-container-image"></a>컨테이너 이미지 만들기

자세한 내용은 다음 자습서를 참조하십시오.

- [자습서: Azure 컨테이너 인스턴스에 배포할 컨테이너 이미지 만들기](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [자습서: Azure 컨테이너 레지스트리 작업을 통해 클라우드에서 컨테이너 이미지를 빌드하고 배포합니다.](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-step"></a>다음 단계

- [컨테이너 오퍼를 만듭니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)
