---
title: Azure Stack에서 PowerShell을 사용 하 여 템플릿 배포 | Microsoft Docs
description: PowerShell을 사용 하 여 Azure Stack에 템플릿을 배포 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 34ced37213ddb585b382965cf73c454d4c9f93e9
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014049"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>PowerShell을 사용 하 여 Azure Stack에 템플릿 배포

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack에 Azure Resource Manager 템플릿을 배포 하려면 PowerShell을 사용할 수 있습니다. 이 문서에서는 PowerShell를 사용 하 여 템플릿을 배포 하는 방법을 설명 합니다.

## <a name="run-azurerm-powershell-cmdlets"></a>AzureRM PowerShell cmdlet 실행

이 예제에서는 **AzureRM** GitHub에서 PowerShell cmdlet 및 템플릿을 저장 합니다. 이 템플릿은 Windows Server 2012 R2 Datacenter 가상 컴퓨터를 만듭니다.

>[!NOTE]
>이 예제를 시도 하기 전에 확인 되었는지 [PowerShell 구성](azure-stack-powershell-configure-user.md) Azure Stack 사용자에 대 한 합니다.

1. 로 이동 [ http://aka.ms/AzureStackGitHub ](https://aka.ms/AzureStackGitHub) 찾고 합니다 **101-간단한-windows-vm** 템플릿. 이 위치에 템플릿을 저장할: `C:\templates\azuredeploy-101-simple-windows-vm.json`합니다.
2. 관리자 권한 PowerShell 명령 프롬프트를 엽니다.
3. 바꿉니다 `username` 고 `password` 와 사용자 이름 및 암호를 스크립트를 실행 한 후 다음 스크립트에서:

    ```PowerShell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"
   
    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    >이 스크립트를 실행할 때마다 값이 증가 된 `$myNum` 배포를 덮어쓰지 않도록 매개 변수입니다.

4. Azure Stack 포털 선택 **찾아보기**를 선택한 후 **가상 머신** 새 가상 컴퓨터를 찾으려면 (**myDeployment001**).

## <a name="next-steps"></a>다음 단계

- [Visual Studio를 사용하여 템플릿 배포](azure-stack-deploy-template-visual-studio.md)
