---
title: Azure PowerShell 샘플 - Azure Cloud Services(추가 지원) 다시 설정
description: Azure Cloud Service(추가 지원) 배포 다시 설정을 위한 샘플 스크립트
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e571a6cfae82274c2e4f3d85540c9724edb657a5
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110705169"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Azure Cloud Service(추가 지원) 다시 설정 
이러한 샘플은 기존 Azure Cloud Service(추가 지원) 배포를 다시 설정하는 다양한 방법을 다룹니다.

## <a name="reimage-role-instances-of-cloud-service"></a>클라우드 서비스의 역할 인스턴스를 이미지로 다시 설치
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
이 명령은 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 클라우드 서비스의 ContosoFrontEnd_IN_0 및 ContosoBackEnd_IN_1의 두 역할 인스턴스를 이미지로 다시 설치합니다.

## <a name="reimage-all-roles-of-cloud-service"></a>클라우드 서비스의 모든 역할을 이미지로 다시 설치
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
이 명령은 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 클라우드 서비스의 모든 역할 인스턴스를 이미지로 다시 설치합니다.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>클라우드 서비스의 단일 역할 인스턴스를 이미지로 다시 설치
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
이 명령은 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 클라우드 서비스의 ContosoFrontEnd_IN_0이라는 역할 인스턴스를 이미지로 다시 설치합니다.

## <a name="rebuild-role-instances-of-cloud-service"></a>클라우드 서비스의 역할 인스턴스 다시 빌드
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
이 명령은 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 클라우드 서비스의 ContosoFrontEnd_IN_0 및 ContosoBackEnd_IN_1의 두 역할 인스턴스를 다시 빌드합니다.

## <a name="rebuild-all-roles-of-cloud-service"></a>클라우드 서비스의 모든 역할 다시 빌드
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
이 명령은 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 클라우드 서비스의 모든 역할 인스턴스를 다시 빌드합니다.

## <a name="restart-role-instances-of-cloud-service"></a>클라우드 서비스의 역할 인스턴스 다시 시작
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
이 명령은 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 클라우드 서비스의 ContosoFrontEnd_IN_0 및 ContosoBackEnd_IN_1의 두 역할 인스턴스를 다시 시작합니다.

## <a name="restart-all-roles-of-cloud-service"></a>클라우드 서비스의 모든 역할 다시 시작
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
이 명령은 ContosOrg라는 리소스 그룹에 속하는 ContosoCS라는 클라우드 서비스의 모든 역할 인스턴스를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

- Azure Cloud Services(추가 지원)에 대한 자세한 내용은 [Azure Cloud Services(추가 지원) 개요](overview.md)를 참조하세요.
- [Cloud Services(추가 지원) 샘플 리포지토리](https://github.com/Azure-Samples/cloud-services-extended-support) 방문
