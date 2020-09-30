---
title: 'PowerShell: 관리되는 인스턴스 만들기'
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 관리형 인스턴스를 만드는 Azure PowerShell 예제 스크립트를 제공합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: fd091cedb67c07b7de2c7e8540e99c3e8daf7dcf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323815"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>PowerShell을 사용하여 관리형 인스턴스 만들기

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

이 PowerShell 스크립트 예제는 새 가상 네트워크 내의 전용 서브넷에 관리형 인스턴스를 만듭니다. 또한 가상 네트워크에 대한 네트워크 보안 그룹과 경로 테이블을 구성합니다. 스크립트가 성공적으로 실행되면 가상 네트워크나 온-프레미스 환경 내에서 관리형 인스턴스에 액세스할 수 있습니다. [Azure SQL Database Managed Instance에 연결하도록 Azure VM 구성](../connect-vm-instance-configure.md) 및 [온-프레미스에서 Azure SQL Managed Instance로의 지점 및 사이트 간 연결 구성](../point-to-site-p2s-configure.md)을 참조하세요.

> [!IMPORTANT]
> 제한 사항은 [지원되는 지역](../resource-limits.md#supported-regions)과 [지원되는 구독 유형](../resource-limits.md#supported-subscription-types)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 1.4.0 이상이 필요합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령 중 일부를 사용합니다. 아래 표에서 사용된 명령 및 기타 명령에 대한 자세한 내용을 보려면 명령 관련 설명서에 대한 링크를 클릭하세요.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 가상 네트워크를 만듭니다. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | 가상 네트워크에 서브넷 구성을 추가합니다. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | 리소스 그룹의 가상 네트워크를 가져옵니다. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | 가상 네트워크에 대한 목표 상태를 설정합니다. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | 가상 네트워크에 서브넷을 가져옵니다. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | 가상 네트워크의 서브넷 구성에 대한 목표 상태를 구성합니다. |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | 경로 테이블을 만듭니다. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | 경로 테이블을 가져옵니다. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | 경로 테이블에 대한 목표 상태를 설정합니다. |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | 관리형 인스턴스를 만듭니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure SQL Managed Instance에 대한 추가 PowerShell 스크립트 샘플은 [Azure SQL Managed Instance PowerShell 스크립트](../../database/powershell-script-content-guide.md)에서 찾을 수 있습니다.
