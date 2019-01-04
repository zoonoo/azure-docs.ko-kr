---
title: Azure에 대한 HTTPS를 통한 Windows 원격 관리 | Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 1a341bf36842e49faf8e39f4056232c97cc4232c
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195976"
---
# <a name="windows-remote-management-over-https"></a>HTTPS를 통한 Windows 원격 관리

이 섹션에서는 PowerShell을 사용하여 원격으로 관리 및 배포할 수 있도록 Azure에서 호스팅하는 Windows 기반 VM을 구성하는 방법을 설명합니다.  PowerShell 원격을 사용하도록 설정하려면 대상 VM에서 WinRM(Windows 원격 관리) HTTPS 엔드포인트를 공개해야 합니다.  PowerShell 원격에 대한 자세한 내용은 [원격 명령 실행](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6)을 참조하세요.  WinRM에 대한 자세한 내용은 [Windows 원격 관리](https://docs.microsoft.com/windows/desktop/WinRM/portal)를 참조하세요.

"클래식" Azure 방법, 즉 Azure Service Manager 포털 또는 사용되지 않는 [Azure Service Management API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)) 중 하나를 사용하여 VM을 만든 경우 WinRM 엔드포인트를 사용하여 해당 VM을 자동으로 구성합니다.  그러나 다음과 같은 "최신" Azure 방법 중 하나를 사용하여 VM을 만드는 경우 HTTPS를 통한 WinRM에 대해 VM이 구성되지 *않습니다*.  

- [Azure 호환 VHD 만들기](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd) 섹션에서 설명한 대로 일반적으로 승인된 [Azure Portal](https://portal.azure.com/) 사용
- [Azure Resource Manager 템플릿 사용](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Azure PowerShell 또는 Azure CLI 명령 셸 사용  예를 들어 [빠른 시작: PowerShell을 사용하여 Azure에서 Windows 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) 및 [빠른 시작: Azure CLI를 사용하여 Linux 가상 머신 만들기](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)를 참조하세요.

이 WinRM 엔드포인트는 [VM 이미지 인증](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm)에서 설명한 대로 VM을 온보딩하기 위한 인증 도구 키트를 실행하는 데에도 필요합니다.

반면에 Linux VM은 일반적으로 SSH 콘솔에서 [Azure CLI](https://docs.microsoft.com/cli/azure) 또는 Linux 명령을 사용하여 원격으로 관리됩니다.  또한 Azure는 [Linux VM에서 스크립트를 실행](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm)하는 몇 가지 대체 메서드를 제공합니다.  더 복잡한 시나리오의 경우 Windows 또는 Linux 기반 VM에 사용할 수 있는 여러 가지 자동화 및 통합 솔루션이 있습니다.


## <a name="configure-and-deploy-with-winrm"></a>WinRM을 사용하여 구성 및 배포

Windows 기반 VM에 대한 WinRM 엔드포인트는 서로 다른 다음 두 가지 개발 단계에서 구성할 수 있습니다.

- 만드는 동안 - VM을 기존 VHD에 배포하는 동안 구성합니다.  이는 새 제안에 기본적으로 설정되는 방법입니다.  이 방법을 사용하려면 제공된 Azure Resource Manager 템플릿을 사용하고 사용자 지정된 PowerShell 스크립트를 실행하여 Azure 인증서를 만들어야 합니다. 
- 배포 후 - Azure에서 호스팅되는 기존 VM에 구성합니다.  Azure에 VM 솔루션이 이미 배포되어 있고 이에 대해 Window 원격 관리를 사용하도록 설정해야 하는 경우 이 방법을 사용합니다.  이 방법을 사용하려면 Azure Portal에서 수동으로 변경하고 대상 VM에서 스크립트를 실행해야 합니다. 


## <a name="next-steps"></a>다음 단계
새 VM을 만드는 경우 [VHD에서 VM을 배포](./cpp-deploy-vm-vhd.md)하는 동안 WinRM을 사용하도록 설정할 수 있습니다.  그렇지 않은 경우 기존 VM에서 WinRM을 사용하도록 설정할 수 있습니다.  
