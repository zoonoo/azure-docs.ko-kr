---
title: Visual Studio를 사용하여 Virtual Machine Scale Set 배포
description: Visual Studio 및 Resource Manager 템플릿을 사용하여 Virtual Machine Scale Sets 배포 | Microsoft Azure
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: c49b4f42bc726c68880bdd4d6f58956936e83177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066971"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Visual Studio에서 Virtual Machine Scale Set를 만드는 방법

이 문서에서는 Visual Studio 리소스 그룹 배포를 사용하여 Azure 가상 시스템 규모 집합을 배포하는 방법을 보여 주며 있습니다.

[Azure 가상 시스템 크기 집합은](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) 자동 크기 조정 및 로드 분산을 사용하여 유사한 가상 시스템의 컬렉션을 배포하고 관리하는 Azure Compute 리소스입니다. [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates)을 사용하여 Virtual Machine Scale Sets를 프로비전하고 배포할 수 있습니다. Azure 리소스 관리자 템플릿은 Azure CLI, PowerShell, REST를 사용하여 배포할 수 있으며 Visual Studio에서 직접 배포할 수도 있습니다. Visual Studio는 Azure 리소스 그룹 배포 프로젝트의 일부로 배포할 수 있는 예제 템플릿 집합을 제공합니다.

Azure 리소스 그룹 배포는 단일 배포 작업을 통해 관련된 Azure 리소스 집합을 그룹화하여 게시하는 방법입니다. 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포를](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Visual Studio에서 가상 컴퓨터 규모 집합 배포를 시작하려면 다음 필수 구성 조건이 필요합니다.

* Visual Studio 2013 이상
* Azure SDK 2.7, 2.8 또는 2.9

>[!NOTE]
>이 문서에서는 [Azure SDK 2.8에서](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)Visual Studio 2019를 사용합니다.

## <a name="create-a-project"></a>프로젝트 만들기<a name="creating-a-project"></a> 

1. Visual Studio를 열고 **새 프로젝트 만들기**를 선택합니다.

1. **새 프로젝트 만들기에서**C#에 대한 **Azure 리소스 그룹을** 선택한 다음 **다음을 선택합니다.**

1. 새 프로젝트 구성에서 이름을 입력하고 **만들기를** **선택합니다.**

    ![프로젝트 이름 지정 및 만들기](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. 템플릿 목록에서 Windows 가상 컴퓨터 **규모 집합** 또는 **Linux 가상 컴퓨터 규모 집합** 템플릿을 선택합니다. **확인**을 선택합니다.

   ![가상 컴퓨터 템플릿 선택](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

프로젝트를 만든 후 **솔루션 탐색기에는** PowerShell 배포 스크립트, Azure 리소스 관리자 템플릿 및 가상 시스템 확장 집합에 대한 매개 변수 파일이 포함됩니다.

## <a name="customize-your-project"></a>프로젝트 사용자 지정

이제 템플릿을 편집하여 응용 프로그램의 요구에 맞게 사용자 지정할 수 있습니다. 가상 시스템 확장 속성을 추가하거나 로드 분산 규칙을 편집할 수 있습니다. 기본적으로 가상 시스템 크기 집합 템플릿은 **AzureDiagnostics** 확장을 배포하도록 구성되어 자동 크기 조정 규칙을 쉽게 추가할 수 있습니다. 또한 템플릿은 인바운드 NAT 규칙으로 구성된 공용 IP 주소가 있는 로드 밸런서를 배포합니다.

로드 밸러블러를 사용하면 SSH(Linux) 또는 RDP(Windows)를 사용하여 가상 컴퓨터 인스턴스에 연결할 수 있습니다. 프런트 엔드 포트 범위는 50000에서 시작합니다. Linux의 경우 SSH가 50000을 포트하는 경우 배율 조정 집합의 첫 번째 가상 컴퓨터 의 포트 22로 로드 밸런싱 경로가 조정됩니다. 포트 50001에 연결하면 두 번째 가상 시스템의 포트 22로 라우팅됩니다.

 Visual Studio를 사용하여 템플릿을 편집하는 좋은 방법은 **JSON 개요를**사용하는 것입니다. 매개 변수, 변수 및 리소스를 구성할 수 있습니다. Visual Studio는 스키마를 이해하여 배포하기 전에 템플릿의 오류를 지적할 수 있습니다.

![JSON 탐색기](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>프로젝트 배포

Azure 리소스 관리자 템플릿을 배포하여 가상 시스템 규모 집합 리소스를 만듭니다.

1. **솔루션 탐색기에서**프로젝트를 마우스 오른쪽 단추로 클릭하고 새 **배포를** > **선택합니다.**

    ![프로젝트 배포](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. **리소스 그룹에 배포에서**사용할 구독을 선택하고 리소스 그룹을 선택합니다. 필요한 경우 리소스 그룹을 만들 수 있습니다.

1. 그런 다음 **매개변수 편집을** 선택하여 템플릿에 전달되는 매개변수를 입력합니다.

   ![구독 및 리소스 그룹 입력](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. 운영 체제의 사용자 이름과 암호를 제공합니다. 이러한 값은 배포를 만드는 데 필요합니다. Visual Studio용 PowerShell 도구가 설치되어 있지 않은 경우 **암호 저장을** 선택하여 숨겨진 PowerShell 명령 프롬프트를 피하거나 [키 자격 증명 모음 지원을](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)사용합니다. 계속하려면 **저장을** 선택합니다.

    ![배포 매개 변수 편집](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. **리소스 그룹에 배포에서** **배포를**선택합니다. 작업은 **배포-AzureResourceGroup.ps1** 스크립트를 실행 합니다. **출력** 창에 배포 진행률이 표시됩니다.

   ![출력에는 결과가 표시됩니다.](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>가상 머신 스케일 세트 살펴보기<a name="exploring-your-virtual-machine-scale-set"></a>

새 가상 컴퓨터 축척 집합을 보려면**클라우드 탐색기** **보기를** > 선택합니다. **모두 새로 고침을**사용합니다.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**클라우드 탐색기를** 사용하면 응용 프로그램을 개발하는 동안 Visual Studio에서 Azure 리소스를 관리할 수 있습니다. [Azure Portal](https://portal.azure.com) 및 [Azure 리소스 탐색기](https://resources.azure.com/)에서 Virtual Machine Scale Set를 볼 수도 있습니다.

 포털은 웹 브라우저를 사용하여 Azure 인프라를 관리하는 가장 좋은 방법을 제공합니다. Azure 리소스 탐색기는 Azure 리소스를 탐색하고 디버깅하는 쉬운 방법을 제공합니다. Azure 리소스 탐색기는 인스턴스 보기를 제공 하며 보고 있는 리소스에 대 한 PowerShell 명령을 표시 합니다.

## <a name="next-steps"></a>다음 단계

Visual Studio를 통해 가상 머신 스케일 집합을 성공적으로 배포한 후에는 응용 프로그램 요구 사항에 맞게 프로젝트를 추가로 사용자 지정할 수 있습니다. 예를 들어 **Insights** 리소스를 추가하여 자동 크기 조정을 구성합니다. 독립 실행형 가상 컴퓨터와 같은 인프라를 템플릿에 추가하거나 사용자 지정 스크립트 확장을 사용하여 응용 프로그램을 배포할 수 있습니다. 좋은 예제 템플릿은 Azure [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates) GitHub 리포지토리에서 찾을 수 있습니다. `vmss`를 검색합니다.
