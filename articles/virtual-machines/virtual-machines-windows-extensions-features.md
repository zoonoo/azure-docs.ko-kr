---
title: "가상 컴퓨터 확장 및 기능 | Microsoft Docs"
description: "확장이 제공하거나 개선하는 기능별로 그룹화하여 Azure 가상 컴퓨터에 사용할 수 있는 확장을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 7bd48e2ef9177369190494c38bfdcf1bb99e61ea


---
# <a name="about-virtual-machine-extensions-and-features"></a>가상 컴퓨터 확장 및 기능 정보
## <a name="azure-vm-extensions"></a>Azure VM 확장
Azure 가상 컴퓨터 확장은 Azure 가상 컴퓨터에서 배포 후 구성 및 자동화 작업을 제공하는 작은 응용 프로그램입니다. 예를 들어 가상 컴퓨터에서 소프트웨어가 설치되도록 요구하거나, 바이러스 백신 보호 또는 Docker 구성을 요구하는 경우 VM 확장을 사용하여 이러한 작업을 완료할 수 있습니다. Azure CLI, PowerShell, Resource Manager 템플릿 및 Azure Portal을 사용하 여 Azure VM 확장을 실행할 수 있습니다. 확장을 새 가상 컴퓨터 배포와 번들로 제공하거나 기존 시스템에 대해 실행할 수 있습니다.

이 문서는 Azure 가상 컴퓨터 확장에 대한 필수 구성 요소와 사용 가능한 VM 확장을 검색하는 방법에 대한 지침을 제공합니다. 

## <a name="azure-vm-agent"></a>Azure VM 에이전트
Azure VM 에이전트는 Azure 가상 컴퓨터와 Azure 패브릭 컨트롤러 간 상호 작용을 관리합니다. VM 에이전트는 VM 확장 실행을 포함하여 Azure 가상 컴퓨터 배포 및 관리의 다양한 기능적 측면을 담당합니다. Azure VM 에이전트는 Azure 갤러리 이미지에 미리 설치되며 지원되는 운영 체제에 설치될 수 있습니다. 

지원되는 운영 체제 및 설치 지침에 대한 자세한 내용은 [Azure Virtual Machines 에이전트](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.

## <a name="discover-vm-extensions"></a>VM 확장 검색
Azure 가상 컴퓨터와 함께 여러 다양한 VM 확장을 사용할 수 있습니다. 전체 목록을 보려면 Azure CLI와 함께 다음 명령을 실행하고 해당 위치를 선택한 위치로 바꿉니다.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>일반 VM 확장
| 확장 이름 | 설명 | 추가 정보 |
| --- | --- | --- |
| Windows용 사용자 지정 스크립트 확장 |Azure 가상 컴퓨터에 대해 스크립트 실행 |[Windows용 사용자 지정 스크립트 확장](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Windows용 DSC 확장 |PowerShell DSC(Desired State Configuration) 확장 |[Docker VM 확장](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure 진단 확장 |Azure 진단 관리 |[Azure 진단 확장](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |




<!--HONumber=Nov16_HO3-->


