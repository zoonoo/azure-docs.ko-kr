---
title: "Azure VM에서 VM 이미지 만들기 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 만든 기존 Azure VM에서 일반화된 VM 이미지를 만드는 방법을 알아봅니다"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 349b8dc78ace79efca771efb78e453a47e51023e
ms.openlocfilehash: 6e80064adadcc542523acb1d2478e6b8f0163acb


---
# <a name="download-the-template-for-a-vm"></a>VM에 대한 템플릿 다운로드
포털 또는 PowerShell을 사용하여 Azure에서 VM을 만들 때 Resource Manager 템플릿은 자동으로 만들어집니다. 배포를 빠르게 복제하는 데 이 템플릿을 사용할 수 있습니다. 템플릿은 리소스 그룹에 있는 모든 리소스에 대한 정보를 포함합니다. 즉 가상 컴퓨터의 경우 네트워킹 리소스를 포함하여 해당 리소스 그룹에서 VM을 지원하기 위해 만든 모든 항목이 템플릿에 있음을 의미합니다.

## <a name="download-the-template-using-the-portal"></a>포털을 사용한 템플릿 다운로드
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 허브메뉴에서 **가상 컴퓨터**를 선택합니다.
3. 목록에서 가상 컴퓨터를 선택합니다.
4. **Automation 스크립트**를 선택합니다.
5. **다운로드**를 선택하고 .zip 파일을 로컬 컴퓨터에 저장합니다.
6. .zip 파일을 열고 파일을 폴더에 풉니다. .zip 파일은 다음을 포함합니다.
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

template.json 파일은 템플릿입니다.

## <a name="download-the-template-using-powershell"></a>PowerShell을 사용한 템플릿 다운로드
또한 [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) cmdlet을 사용하여 .json 템플릿 파일을 다운로드할 수 있습니다. `-path` 매개 변수를 사용하여 .json 파일의 파일 이름 및 경로를 제공합니다. 이 예제에서는 로컬 컴퓨터의 **C:\users\public\downloads** 폴더에 **myResourceGroup**이라는 리소스 그룹에 대한 템플릿을 다운로드하는 방법을 보여 줍니다.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>다음 단계
템플릿을 사용하여 리소스를 배포하는 방법에 대해 알아보려면 [Resource Manager 템플릿 연습](../resource-manager-template-walkthrough.md)을 참조하세요.




<!--HONumber=Nov16_HO4-->


