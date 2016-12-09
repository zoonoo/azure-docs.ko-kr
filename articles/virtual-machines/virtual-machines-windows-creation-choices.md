---
title: "Windows VM을 만드는 다양한 방법 | Microsoft Docs"
description: "리소스 관리자로 Windows 가상 컴퓨터를 만드는 다양한 방법을 나열합니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 809ba8f4-b54e-43c5-bbe3-8e710c49971f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: a5e0ba0128b7f20d6307f5dea784de007e71321c


---
# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>리소스 관리자로 Windows 가상 컴퓨터를 만드는 다양한 방법
가상 컴퓨터는 다양한 사용자와 목적에 맞게 조절 가능하기 때문에 Azure에서는 여러 방법으로 가상 컴퓨터를 만들 수 있습니다. 즉 가상 컴퓨터를 만드는 방법과 그에 대한 몇 가지 선택이 필요합니다. 이 문서에서는 이 선택 사항 및 지침에 대한 링크 요약을 제공합니다.

## <a name="azure-portal"></a>Azure 포털
Azure 포털의 사용은 특히 Azure로 시작한 경우 가상 컴퓨터를 사용해 볼 수 있는 간단한 방법입니다. 

[포털에서 Windows를 실행하는 가상 컴퓨터 만들기](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="template"></a>Template
가상 컴퓨터에 가용성 집합 및 저장소 계정과 같은 리소스의 조합이 필요합니다. 각 리소스를 개별적으로 배포하고 관리하는 대신, 모든 리소스를 하나의 조정된 작업으로 배포하고 프로비전하는 Azure Resource Manager 템플릿을 만들 수 있습니다.

* [리소스 관리자 템플릿을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="azure-powershell"></a>Azure PowerShell
명령 셸에서 작업하고 싶다면 Azure PowerShell을 사용할 수 있습니다.

* [PowerShell을 사용하여 Windows VM 만들기](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="visual-studio"></a>Visual Studio
Visual Studio를 사용하여 Azure Tools for Visual Studio 및 Azure SDK로 VM을 빌드, 관리 및 배포할 수 있습니다.

[Azure Tools for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)




<!--HONumber=Nov16_HO3-->


