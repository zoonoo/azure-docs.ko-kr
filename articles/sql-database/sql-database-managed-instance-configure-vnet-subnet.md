---
title: Azure SQL Database Managed Instance 기존 VNET/서브넷 구성 | Microsoft Docs
description: 이 항목에서는 Azure SQL Database Managed Instance를 배포할 수 있는 기존 VNet(가상 네트워크) 및 서브넷을 구성하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 53aba5192ddf57598965fcfe0db5f2b18423c7e9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345596"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance의 기존 VNet 구성

Azure SQL Database Managed Instance는 Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 및 Managed Instance 전용 서브넷에만 배포되어야 합니다. [Managed Instance VNet 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)에 따라 구성된 경우 기존 VNet 및 서브넷을 사용할 수 있습니다. 

아직 구성되지 않은 새 서브넷이 [요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)에 맞게 조정된 서브넷인지 확실하지 않거나, 사용자가 일부 변경한 후에도 [네트워크 요구 사항](sql-database-managed-instance-connectivity-architecture.md#network-requirements)을 준수하는 서브넷인지 확인하려는 경우에는 이 섹션에서 설명한 스크립트를 사용하여 네트워크의 유효성을 검사하고 네트워크를 수정할 수 있습니다. 

  > [!Note]
  > 리소스 관리자 가상 네트워크에서 Managed Instance만을 만들 수 있습니다. 클래식 배포 모델을 사용하여 배포된 Azure VNet은 지원되지 않습니다. 리소스를 배포한 후에는 서브넷 크기를 조정할 수 없으므로 [Managed Instance의 서브넷 크기 결정](#determine-the-size-of-subnet-for-managed-instances) 섹션의 지침에 따라 서브넷 크기를 계산해야 합니다.

## <a name="validate-and-modify-an-existing-virtual-network"></a>기존 가상 네트워크 유효성 검사 및 수정 

기존 서브넷 내에서 Managed Instance를 만들려는 경우 서브넷을 준비하는 데 다음 PowerShell 스크립트가 권장됩니다.
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
서브넷 준비는 간단한 세 단계로 수행됩니다.

1. 유효성 검사 - 선택한 가상 네트워크와 서브넷은 Managed Instance 네트워킹 요구 사항에 대해 유효성을 검사합니다.
2. 확인 - 사용자에게 Managed Instance 배포를 위해 서브넷을 준비해야 할 일련의 변경 사항이 나타나고 동의를 묻는 메시지가 표시됩니다.
3. 준비 - 가상 네트워크 및 서브넷이 제대로 구성됩니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- VNet을 만들고, Managed Instance를 만들고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Database Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
