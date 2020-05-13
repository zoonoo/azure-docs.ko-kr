---
title: Visual Studio를 사용하여 Virtual Machine Scale Set 배포
description: Visual Studio 및 Resource Manager 템플릿을 사용하여 Virtual Machine Scale Sets 배포 | Microsoft Azure
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: visual-studio
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 826dc1858984508e54e160675dd10f6dfad88af8
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124366"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Visual Studio에서 Virtual Machine Scale Set를 만드는 방법

이 문서에서는 Visual Studio 리소스 그룹 배포를 사용 하 여 Azure 가상 머신 확장 집합을 배포 하는 방법을 보여 줍니다.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) 는 자동 크기 조정 및 부하 분산을 사용 하 여 유사한 가상 머신 컬렉션을 배포 하 고 관리 하기 위한 azure Compute 리소스입니다. [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates)을 사용하여 Virtual Machine Scale Sets를 프로비전하고 배포할 수 있습니다. Azure Resource Manager 템플릿은 Azure CLI, PowerShell, REST를 사용 하거나 Visual Studio에서 직접 배포할 수 있습니다. Visual Studio는 Azure 리소스 그룹 배포 프로젝트의 일부로 배포 될 수 있는 예제 템플릿 집합을 제공 합니다.

Azure 리소스 그룹 배포는 단일 배포 작업을 통해 관련된 Azure 리소스 집합을 그룹화하여 게시하는 방법입니다. 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

Visual Studio에서 Virtual Machine Scale Sets 배포를 시작 하려면 다음 필수 구성 요소가 필요 합니다.

* Visual Studio 2013 이상
* Azure SDK 2.7, 2.8 또는 2.9

>[!NOTE]
>이 문서에서는 [AZURE SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)에서 Visual Studio 2019을 사용 합니다.

## <a name="create-a-project"></a>프로젝트 만들기<a name="creating-a-project"></a> 

1. Visual Studio를 열고 **새 프로젝트 만들기**를 선택합니다.

1. **새 프로젝트 만들기**에서 c #에 대 한 **Azure 리소스 그룹** 을 선택 하 고 **다음**을 선택 합니다.

1. **새 프로젝트 구성**에서 이름을 입력 하 고 **만들기**를 선택 합니다.

    ![프로젝트 이름 및 만들기](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. 템플릿 목록에서 **Windows 가상 머신 확장 집합** 또는 **Linux 가상 머신 확장 집합** 템플릿을 선택 합니다. **확인**을 선택합니다.

   ![가상 컴퓨터 템플릿 선택](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

프로젝트를 만든 후에는 가상 머신 확장 집합에 대 한 PowerShell 배포 스크립트, Azure Resource Manager 템플릿 및 매개 변수 파일을 포함 **솔루션 탐색기** 합니다.

## <a name="customize-your-project"></a>프로젝트 사용자 지정

이제 템플릿을 편집 하 여 응용 프로그램의 요구에 맞게 사용자 지정할 수 있습니다. 가상 컴퓨터 확장 속성을 추가 하거나 부하 분산 규칙을 편집할 수 있습니다. 기본적으로 가상 머신 확장 집합 템플릿은 자동 크기 조정 규칙을 쉽게 추가할 수 있도록 **Azurediagnostics** 확장을 배포 하도록 구성 됩니다. 또한이 템플릿은 인바운드 NAT 규칙으로 구성 된 공용 IP 주소를 사용 하 여 부하 분산 장치를 배포 합니다.

부하 분산 장치를 사용 하면 SSH (Linux) 또는 RDP (Windows)를 사용 하 여 가상 머신 인스턴스에 연결할 수 있습니다. 프런트 엔드 포트 범위는 50000에서 시작합니다. Linux의 경우 포트 5만로 SSH 하는 경우 부하 분산은 확장 집합에 있는 첫 번째 가상 머신의 포트 22로 라우팅합니다. 포트 50001에 연결 하는 것은 두 번째 가상 컴퓨터의 포트 22로 라우팅됩니다.

 Visual Studio를 사용 하 여 템플릿을 편집 하는 좋은 방법은 **JSON 개요**를 사용 하는 것입니다. 매개 변수, 변수 및 리소스를 구성할 수 있습니다. 스키마를 이해 하 고 나면 Visual Studio는 템플릿을 배포 하기 전에 템플릿에서 오류를 가리킬 수 있습니다.

![JSON 탐색기](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>프로젝트 배포

Azure Resource Manager 템플릿을 배포 하 여 가상 머신 확장 집합 리소스를 만듭니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **Deploy**  >  **새**배포를 선택 합니다.

    ![프로젝트 배포](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. **리소스 그룹에 배포**에서 사용할 구독을 선택 하 고 리소스 그룹을 선택 합니다. 필요한 경우 리소스 그룹을 만들 수 있습니다.

1. 그런 다음 **매개 변수 편집** 을 선택 하 여 템플릿에 전달 되는 매개 변수를 입력 합니다.

   ![구독 및 리소스 그룹 입력](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. 운영 체제에 대 한 사용자 이름 및 암호를 제공 합니다. 이러한 값은 배포를 만드는 데 필요 합니다. PowerShell Tools for Visual Studio 설치 되어 있지 않은 경우 숨겨진 PowerShell 명령 프롬프트를 방지 하려면 **암호 저장** 을 선택 하거나 [Key Vault 지원](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)을 사용 합니다. **저장** 을 선택 하 여 계속 합니다.

    ![배포 매개 변수 편집](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. **리소스 그룹에 배포**에서 **배포**를 선택 합니다. 작업은 **deploy-azureresourcegroup.ps1** 스크립트를 실행 합니다. **출력** 창에 배포 진행률이 표시됩니다.

   ![출력 결과 표시](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>가상 머신 확장 집합 살펴보기<a name="exploring-your-virtual-machine-scale-set"></a>

**View**  >  새 가상 머신 확장 집합을 보려면**클라우드 탐색기** 보기를 선택 합니다. 필요한 경우 **모두 새로 고침**을 사용 합니다.

![클라우드 탐색기](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**클라우드 탐색기** 를 사용 하면 응용 프로그램을 개발 하는 동안 Visual Studio에서 Azure 리소스를 관리할 수 있습니다. [Azure Portal](https://portal.azure.com) 및 [Azure 리소스 탐색기](https://resources.azure.com/)에서 Virtual Machine Scale Set를 볼 수도 있습니다.

 포털은 웹 브라우저를 사용 하 여 Azure 인프라를 관리 하는 최상의 방법을 제공 합니다. Azure Resource Explorer은 Azure 리소스를 탐색 하 고 디버그 하는 쉬운 방법을 제공 합니다. Azure Resource Explorer는 인스턴스 뷰를 제공 하 고, 보고 있는 리소스에 대 한 PowerShell 명령도 보여 줍니다.

## <a name="next-steps"></a>다음 단계

Visual Studio를 통해 Virtual Machine Scale Sets 성공적으로 배포 되 면 응용 프로그램 요구 사항에 맞게 프로젝트를 추가로 사용자 지정할 수 있습니다. 예를 들어, **Insights** 리소스를 추가 하 여 자동 크기 조정을 구성 합니다. 독립 실행형 가상 컴퓨터와 같은 템플릿에 인프라를 추가 하거나 사용자 지정 스크립트 확장을 사용 하 여 응용 프로그램을 배포할 수 있습니다. 좋은 예제 템플릿은 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates) GitHub 리포지토리에서 찾을 수 있습니다. `vmss`를 검색합니다.
