---
title: Azure Marketplace의 가상 머신 제안에 대한 기술 자산 만들기 | Microsoft Docs
description: Azure Marketplace에서 가상 머신 제안에 대한 기술 자산을 만드는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/20/2018
ms.author: pbutlerm
ms.openlocfilehash: 6f1a93c3d3059e612d8c309b263e263dbb84c67f
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050104"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>가상 머신 제안에 대한 기술 자산 만들기

이 섹션에서는 Azure Marketplace의 VM(가상 머신) 제안에 대한 기술 자산을 만들고 구성하는 방법을 안내합니다.  VM에는 솔루션 VHD(가상 하드 디스크)와 선택적인 연결된 데이터 디스크의 두 가지 구성 요소가 있습니다.  

- *VHD(가상 하드 디스크)* - Azure Marketplace 제안을 통해 배포할 운영 체제와 솔루션이 포함되어 있습니다. VHD가 Linux 기반, Windows 기반 또는 사용자 지정 기반 VM인지 여부에 따라 VHD를 준비하는 프로세스가 달라집니다.
- *데이터 디스크* - 가상 머신에 대한 전용 영구 저장소를 나타냅니다. 솔루션 VHD(예: `C:` 드라이브)는 영구 정보를 저장하는 데 사용하지 *않습니다*.

VM 이미지에는 운영 체제 디스크 하나와 0개 이상의 데이터 디스크가 포함되어 있습니다. 디스크당 VHD 하나가 필요합니다. 데이터 디스크가 비어 있는 경우에도 VHD를 만들어야 합니다.
VM OS, VM 크기, 개방할 포트 및 최대 15개의 연결된 데이터 디스크를 구성해야 합니다.

> [!TIP] 
> 사용 중인 운영 체제에 상관없이 SKU에 필요한 최소 개수의 데이터 디스크만 추가합니다. 고객은 배포 시 이미지의 일부인 디스크를 제거할 수 없지만, 배포 중 또는 배포 후에 언제든지 디스크를 추가할 수 있습니다. 

> [!IMPORTANT]
> *새 이미지 버전에서 디스크 수를 변경 하지 마세요.*  이미지에서 데이터 디스크를 다시 구성해야 하는 경우 새 SKU를 정의합니다. 디스크 수가 다른 새 이미지 버전을 게시하면 자동 크기 조정 시의 새 이미지 버전에 기반한 새 배포, Azure Resource Manager 템플릿을 통한 솔루션의 자동 배포 및 기타 시나리오가 중단될 수 있습니다.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>기본 기술 지식

이러한 자산을 설계, 구축 및 테스트하려면 시간이 걸리고 Azure 플랫폼과 제안을 작성하는 데 사용되는 기술에 대한 기술적 지식이 모두 필요합니다. 엔지니어링 팀은 솔루션 영역 외에도 다음 Microsoft 기술에 대한 지식을 갖추고 있어야 합니다. 
-   [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해 
-   [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
-   [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking)에 대한 실무 지식
-   [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 대한 실무 지식
-   [JSON](https://www.json.org/)에 대한 실무 지식


## <a name="suggested-tools"></a>권장되는 도구 

VHD 및 VM을 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

또한 개발 환경에 다음 도구를 추가하는 것이 좋습니다. 

-   [Azure Storage 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio 코드](https://code.visualstudio.com/)
    *   확장: [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

또한 [ Azure 개발자 도구 ](https://azure.microsoft.com/tools/) 페이지에서 사용 가능한 도구를 검토하고, Visual Studio를 사용하는 경우 [ Visual Studio Marketplace ](https://marketplace.visualstudio.com/)를 검토하는 것이 좋습니다.


## <a name="next-steps"></a>다음 단계

이 섹션의 다음 문서에서는 이러한 VM 자산을 만들고 등록하는 단계를 안내합니다.

1. [Azure 호환 가상 하드 디스크 만들기](./cpp-create-vhd.md) - Azure와 호환되는 Linux 또는 Windows 기반 VHD를 만드는 방법을 설명합니다.  여기에는 크기 조정, 패치 및 업로드할 VM 준비와 같은 모범 사례가 포함됩니다.

2. [가상 머신에 연결](./cpp-connect-vm.md) - 새로 만든 VM에 원격으로 연결하여 로그인하는 방법을 설명합니다.  이 문서에서는 VM을 중지하여 사용량 비용을 절약하는 방법도 설명합니다.

3. [가상 머신 구성](./cpp-configure-vm.md) - 올바른 VHD 크기를 선택하고, 이미지를 일반화하며, 최신 업데이트(패치)를 적용하고, 사용자 지정 구성을 예약하는 방법을 설명합니다.

4. [가상 하드 디스크에서 가상 머신 배포](./cpp-deploy-vm-vhd.md) - Azure에서 배포한 VHD에서 VM을 등록하는 방법을 설명합니다.  필요한 도구 및 이 도구를 사용하여 사용자 VM 이미지를 만든 다음, [Microsoft Azure Portal](https://ms.portal.azure.com/) 또는 PowerShell 스크립트 중 하나를 사용하여 Azure에 이를 배포하는 방법을 나열합니다. 

5. [가상 머신 이미지 인증](./cpp-certify-vm.md) - Azure Marketplace 인증을 위해 VM 이미지를 테스트하고 제출하는 방법을 설명합니다. *Azure Certified용 인증 테스트 도구*를 얻는 방법과 이 도구를 사용하여 VM 이미지를 인증하는 방법을 설명합니다. 

6. [SAS URI 가져오기](./cpp-get-sas-uri.md) - VM 이미지에 대한 SAS(공유 액세스 서명) URI를 가져오는 방법을 설명합니다.
 
지원 문서로서 [일반적인 공유 액세스 서명 URL 문제](./cpp-common-sas-url-issues.md)에는 SAS URI를 사용할 때 발생할 수 있는 몇 가지 일반적인 문제와 이에 해당하는 가능한 해결 방법이 나열되어 있습니다.

이러한 모든 단계가 완료되면 Azure Marketplace에 [VM 제안을 게시](./cpp-publish-offer.md)할 준비가 됩니다.
