---
title: Azure PowerShell 샘플 - Azure Cloud Service(추가 지원) 업데이트
description: Azure Cloud Service(추가 지원) 배포를 업데이트하기 위한 샘플 스크립트
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: fcb92ec3aa8f360107f0e7220415c57344c6d83a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99821343"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>Azure Cloud Service(추가 지원) 업데이트

이러한 샘플은 기존 Azure Cloud Service(추가 지원) 배포를 업데이트하는 다양한 방법을 다룹니다.

## <a name="add-an-extension-to-existing-cloud-service"></a>기존 Cloud Service에 확장 추가
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing Cloud Service extension object
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension + $rdpExtension
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>Cloud Service에서 모든 확장 제거
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfileExtension = @()
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>Cloud Service에서 원격 데스크톱 확장 제거
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension | Where-Object { $_.Name -ne "RDPExtension" }
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>규모 확장/축소 역할 인스턴스
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfileRole | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfileRole | Where-Object {$_.Name -eq "ContosoFrontend"}
 $role.SkuCapacity -= 1

# Update Cloud Service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>다음 단계
Azure Cloud Services(추가 지원)에 대한 자세한 내용은 [Azure Cloud Services(추가 지원) 개요](overview.md)를 참조하세요.
- [Cloud Services(추가 지원) 샘플 리포지토리](https://github.com/Azure-Samples/cloud-services-extended-support) 방문