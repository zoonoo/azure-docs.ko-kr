---
title: PowerShell에서 RDP 사용자 이름 및 암호 업데이트
description: Azure PowerShell 스크립트 샘플 - 특정 노트 유형의 모든 Service Fabric 클러스터 노드에 대해 RDP 사용자 이름과 암호를 업데이트합니다.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: b7f229162ed745408121d898f5af516d22a0c039
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984932"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>클러스터 VM의 관리자 사용자 이름과 암호를 업데이트합니다.

Service Fabric 클러스터의 각 [노드 유형](../service-fabric-cluster-nodetypes.md)은 가상 머신 확장 집합입니다. 이 샘플 스크립트는 특정 노드 유형의 클러스터 가상 머신에 대해 관리자 사용자 이름과 암호를 업데이트합니다.  VMAccessAgent 확장을 확장 집합에 추가합니다. 관리자 암호는 수정 가능한 확장 집합 속성이 아니기 때문입니다.  사용자 이름 및 암호 변경은 확장 집합의 모든 노드에 적용됩니다. 필요에 따라 매개 변수를 사용자 지정합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | 클러스터 노드 유형의 속성을 가져옵니다(가상 머신 확장 집합).   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| 가상 머신 확장 집합에 확장을 추가합니다.|
| [업데이트 AzVmss](/powershell/module/az.compute/update-azvmss)|가상 머신 확장 집합의 상태를 로컬 VMSS 개체의 상태로 업데이트합니다.|

## <a name="duration"></a>Duration

예를 들어 5개의 노드가 있는 단일 노드 형식은 사용자 이름 또는 암호를 변경하는 데 45~60분이 걸릴 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.
