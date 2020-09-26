---
title: 기존 가상 네트워크 구성
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Managed Instance를 배포할 수 있는 기존 가상 네트워크 및 서브넷을 구성 하는 방법을 설명 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 03/17/2020
ms.openlocfilehash: 17838d4a652be3a1b50da56812731e0f634a0c08
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323127"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance의 기존 가상 네트워크 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 Azure [virtual network](../../virtual-network/virtual-networks-overview.md) 내에 배포 되어야 하 고 관리 되는 인스턴스에서만 전용 서브넷 이어야 합니다. [SQL Managed Instance 가상 네트워크 요구 사항](connectivity-architecture-overview.md#network-requirements)에 따라 구성 된 경우 기존 가상 네트워크 및 서브넷을 사용할 수 있습니다.

다음 경우 중 하나에 해당하면 이 문서에 설명된 스크립트를 사용하여 네트워크가 유효한지 검사하고 수정할 수 있습니다.

- 아직 구성하지 않은 새 서브넷이 있습니다.
- 서브넷에 [요구 사항](connectivity-architecture-overview.md#network-requirements)에 맞는지 잘 모릅니다.
- 변경한 후에 서브넷이 여전히 [네트워크 요구 사항](connectivity-architecture-overview.md#network-requirements)을 준수하는지 확인하려고 합니다.

> [!Note]
> Azure Resource Manager 배포 모델을 통해 만든 가상 네트워크 에서만 관리 되는 인스턴스를 만들 수 있습니다. 클래식 배포 모델을 통해 만든 Azure Virtual Network는 지원되지 않습니다. [SQL Managed Instance에 대 한 서브넷 크기 결정](vnet-subnet-determine-size.md) 문서의 지침에 따라 서브넷 크기를 계산 합니다. 리소스를 배포한 후에는 서브넷 크기를 조정할 수 없습니다.
>
> 관리 되는 인스턴스를 만든 후에는 인스턴스나 VNet을 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.

## <a name="validate-and-modify-an-existing-virtual-network"></a>기존 가상 네트워크 유효성 검사 및 수정

기존 서브넷 내에서 관리 되는 인스턴스를 만들려는 경우 다음 PowerShell 스크립트를 통해 서브넷을 준비 하는 것이 좋습니다.

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

스크립트는 다음 3단계에 따라 서브넷을 준비합니다.

1. Validate: 선택한 가상 네트워크와 SQL Managed Instance 네트워킹 요구 사항에 대 한 서브넷의 유효성을 검사 합니다.
2. 확인: 사용자에 게 SQL Managed Instance 배포를 위한 서브넷을 준비 하기 위해 수행 해야 하는 변경 내용 집합이 표시 됩니다. 동의도 요청됩니다.
3. 준비: 가상 네트워크 및 서브넷이 제대로 구성됩니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [SQL Managed Instance 이란?](sql-managed-instance-paas-overview.md)을 참조 하세요.
- 가상 네트워크를 만들고, 관리 되는 인스턴스를 만들고, 데이터베이스 백업에서 데이터베이스를 복원 하는 방법을 보여 주는 자습서는 [관리 되는 인스턴스 만들기](instance-create-quickstart.md)를 참조 하세요.
- DNS 문제는 [사용자 지정 DNS 구성](custom-dns-configure.md)을 참조하세요.
