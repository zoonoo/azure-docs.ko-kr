---
title: "Azure 스택에서 PowerShell 사용 하 여 템플릿을 배포 | Microsoft Docs"
description: "리소스 관리자 템플릿 및 PowerShell을 사용 하 여 가상 컴퓨터를 배포 하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: d271b155d65a7dd95a92262da338cf3a272d140b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>PowerShell을 사용하여 Azure Stack의 템플릿 배포

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

PowerShell을 사용 하 여 Azure 리소스 관리자 템플릿을 Azure 스택 개발 키트를 배포 합니다.  리소스 관리자 템플릿 배포 및 조정를 단일 작업에서 응용 프로그램에 대 한 모든 리소스를 프로 비전 합니다.

## <a name="run-azurerm-powershell-cmdlets"></a>AzureRM PowerShell cmdlet 실행
이 예제는 스크립트를 실행 하면 Azure 스택 개발 키트를 가상 컴퓨터를 배포할 리소스 관리자 템플릿을 사용 하 여 합니다.  계속 하기 전에 확인 해야 [PowerShell 구성](azure-stack-powershell-configure-user.md)  

이 예제에서는 서식 파일에 사용 된 VHD에는 windows Server 2012-R2 Datacenter입니다.

1. 로 이동 <http://aka.ms/AzureStackGitHub>, 검색할는 **단순 windows vm 101** 서식 파일을 다음 위치에 저장: c:\\템플릿\\ azuredeploy-101-단순-windows-vm.json 합니다.
2. PowerShell에서 다음 배포 스크립트를 실행합니다. 대체 *username* 및 *암호* 사용자 이름 및 암호 사용 합니다. 후속 사용에 대 한 값이 증가 *$myNum* 배포를 덮어쓰지 않도록 매개 변수입니다.
   
   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
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
3. Azure 스택 포털을 열려면 **찾아보기**, 클릭 **가상 컴퓨터**, 새 가상 컴퓨터를 찾아서 (*myDeployment001*).


## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 템플릿 배포](azure-stack-deploy-template-visual-studio.md)

