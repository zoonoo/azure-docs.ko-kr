---
title: 인스턴스 풀 방법 안내(미리 보기)
description: 이 문서에서는 Azure SQL Database 인스턴스 풀(미리 보기)을 만들고 관리하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299365"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Azure SQL Database 인스턴스 풀(미리 보기) 방법 가이드

이 문서에서는 [인스턴스 풀을](sql-database-instance-pools.md)만들고 관리하는 방법에 대한 자세한 내용을 제공합니다.

## <a name="instance-pool-operations"></a>인스턴스 풀 작업

다음 표에서는 Azure 포털 및 PowerShell에서 인스턴스 풀과 관련된 사용 가능한 작업 및 가용성을 보여 주었습니다.

|명령|Azure portal|PowerShell|
|:---|:---|:---|
|인스턴스 풀 만들기|예|yes|
|인스턴스 풀 업데이트(제한된 수의 속성)|예 |yes |
|인스턴스 풀 사용량 및 속성 확인|예|yes |
|인스턴스 풀 삭제|예|yes|
|인스턴스 풀 내에서 관리되는 인스턴스 만들기|예|yes|
|관리되는 인스턴스 리소스 사용량 업데이트|yes |yes|
|관리되는 인스턴스 사용량 및 속성 확인|yes|yes|
|풀에서 관리되는 인스턴스 삭제|yes|yes|
|풀에 배치된 관리 인스턴스에서 데이터베이스 만들기|yes|yes|
|관리되는 인스턴스에서 데이터베이스 삭제|yes|yes|

사용 가능한 [PowerShell 명령](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |설명 |
|:---|:---|
|[뉴 아즈Sql인스턴스풀](/powershell/module/az.sql/new-azsqlinstancepool/) | Azure SQL Database 인스턴스 풀을 만듭니다. |
|[겟-아즈Sql인스턴스풀](/powershell/module/az.sql/get-azsqlinstancepool/) | Azure SQL 인스턴스 풀에 대한 정보를 반환합니다. |
|[세트-아즈Sql인스턴스풀](/powershell/module/az.sql/set-azsqlinstancepool/) | Azure SQL Database 인스턴스 풀에 대한 속성을 설정합니다. |
|[제거-아즈Sql인스턴스풀](/powershell/module/az.sql/remove-azsqlinstancepool/) | Azure SQL Database 인스턴스 풀을 제거합니다. |
|[겟-아즈Sql인스턴스풀사용법](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Azure SQL 인스턴스 풀 사용량에 대한 정보를 반환합니다. |


PowerShell을 사용하려면 [최신 버전의 PowerShell 코어를 설치하고](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)지침에 따라 [Azure PowerShell 모듈을 설치합니다.](https://docs.microsoft.com/powershell/azure/install-az-ps)

풀 및 단일 인스턴스 내의 인스턴스와 관련된 작업의 경우 표준 [관리되는 인스턴스 명령을](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)사용하지만 풀의 인스턴스에 대해 이러한 명령을 사용할 때 *인스턴스 풀 이름* 속성을 채워야 합니다.

## <a name="how-to-deploy-managed-instances-into-pools"></a>관리되는 인스턴스를 풀에 배포하는 방법

풀에 인스턴스를 배포하는 프로세스는 다음 두 단계로 구성됩니다.

1. 일회성 인스턴스 풀 배포. 이 작업은 기간이 [빈 서브넷에서 만든 단일 인스턴스를](sql-database-managed-instance.md#managed-instance-management-operations)배포하는 것과 동일한 장기 실행 작업입니다.

2. 인스턴스 풀에서 반복적인 인스턴스 배포. 인스턴스 풀 매개 변수는 이 작업의 일부로 명시적으로 지정해야 합니다. 일반적으로 최대 5분이 소요되는 비교적 빠른 작업입니다.

공개 미리 보기에서는 두 단계 모두 PowerShell 및 리소스 관리자 템플릿을 사용하여만 지원됩니다. Azure 포털 환경을 현재 사용할 수 없습니다.

관리되는 인스턴스가 풀에 배포된 후 Azure Portal을 사용하여 가격 책정 계층 페이지에서 해당 속성을 변경할 *수 있습니다.*


## <a name="create-an-instance-pool"></a>인스턴스 풀 만들기

인스턴스 풀을 만들려면 다음을 수행합니다.

1. [서브넷을 사용하여 가상 네트워크를 만듭니다.](#create-a-virtual-network-with-a-subnet)
2. [인스턴스 풀을 만듭니다.](#create-an-instance-pool)


### <a name="create-a-virtual-network-with-a-subnet"></a>서브넷을 사용하여 가상 네트워크 만들기 

동일한 가상 네트워크 내에 여러 인스턴스 풀을 배치하려면 다음 문서를 참조하십시오.

- [Azure SQL Database 관리 인스턴스에 대한 VNet 서브넷 크기를 확인합니다.](sql-database-managed-instance-determine-size-vnet-subnet.md)
- [Azure 포털 템플릿을](sql-database-managed-instance-create-vnet-subnet.md) 사용하여 새 가상 네트워크 및 서브넷을 만들거나 [기존 가상 네트워크를 준비하는](sql-database-managed-instance-configure-vnet-subnet.md)방법에 대한 지침을 따릅니다.
 


### <a name="create-an-instance-pool"></a>인스턴스 풀 만들기 

이전 단계를 완료하면 인스턴스 풀을 만들 준비가 된 것입니다.

인스턴스 풀에는 다음 제한 사항이 적용됩니다.

- 일반 용 및 Gen5만 공개 미리 보기에서 사용할 수 있습니다.
- 풀 이름은 소문자, 숫자 및 하이픈만 포함할 수 있으며 하이픈으로 시작할 수 없습니다.
- AHB(Azure 하이브리드 혜택)를 사용하려는 경우 인스턴스 풀 수준에서 적용됩니다. 풀 생성 중에 라이선스 유형을 설정하거나 생성 후 언제든지 업데이트할 수 있습니다.

> [!IMPORTANT]
> 인스턴스 풀을 배포하는 작업은 약 4.5시간이 소요되는 장기 실행 작업입니다.

네트워크 매개 변수를 얻으려면 다음을 수행하십시오.

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

인스턴스 풀을 만들려면 다음을 수행합니다.

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> 인스턴스 풀을 배포하는 것은 장기 실행 작업이므로 이 문서에서 다음 단계를 실행하기 전에 인스턴스 풀이 완료될 때까지 기다려야 합니다.

## <a name="create-a-managed-instance-inside-the-pool"></a>풀 내에서 관리되는 인스턴스 만들기 

인스턴스 풀을 성공적으로 배포한 후에는 인스턴스 를 만듭니다.

관리되는 인스턴스를 만들려면 다음 명령을 실행합니다.

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

풀 내부에 인스턴스를 배포하는 데 몇 분 정도 걸립니다. 첫 번째 인스턴스를 만든 후 추가 인스턴스를 만들 수 있습니다.

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>인스턴스 내부에 데이터베이스 만들기 

풀 내에 있는 관리되는 인스턴스에서 데이터베이스를 만들고 관리하려면 단일 인스턴스 명령을 사용합니다.

관리되는 인스턴스 내에서 데이터베이스를 만들려면 다음을 수행합니다.

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>인스턴스 풀 사용 받기 
 
풀 내부의 인스턴스 목록을 얻으려면 다음을 수행합니다.

```powershell
$instancePool | Get-AzSqlInstance
```


풀 리소스 사용량을 얻으려면 다음을 수행합니다.

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


풀 및 그 안에 있는 인스턴스에 대한 자세한 사용 개요를 얻으려면 다음을 수행하십시오.

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

인스턴스에 데이터베이스를 나열하려면 다음을 수행합니다.

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> 인스턴스당 데이터베이스가 아닌 풀당 100개의 데이터베이스가 있습니다.


## <a name="scale-a-managed-instance-inside-a-pool"></a>풀 내에서 관리되는 인스턴스 확장 


관리되는 인스턴스를 데이터베이스로 채우면 저장소 또는 성능과 관련된 인스턴스 제한을 칠 수 있습니다. 이 경우 풀 사용량을 초과하지 않은 경우 인스턴스를 확장할 수 있습니다.
풀 내에서 관리되는 인스턴스를 확장하는 작업은 몇 분 정도 걸립니다. 크기 조정의 전제 조건은 인스턴스 풀 수준에서 사용 가능한 vCore및 저장소입니다.

vCore 및 저장소 크기를 업데이트하려면 다음을 수행하십시오.

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


저장소 크기만 업데이트하려면 다음을 수행하십시오.

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>풀 내의 관리되는 인스턴스에 연결

풀에서 관리되는 인스턴스에 연결하려면 다음 두 단계가 필요합니다.

1. [인스턴스에 대한 공용 끝점을 사용하도록 설정합니다.](#enable-the-public-endpoint-for-the-instance)
2. [NSG(네트워크 보안 그룹)에 인바운드 규칙을 추가합니다.](#add-an-inbound-rule-to-the-network-security-group)

두 단계가 모두 완료되면 인스턴스 를 만드는 동안 제공된 공용 끝점 주소, 포트 및 자격 증명을 사용하여 인스턴스에 연결할 수 있습니다. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>인스턴스에 대한 공용 끝점 사용

인스턴스에 대한 공용 끝점을 사용하도록 설정하면 Azure 포털을 사용하거나 다음 PowerShell 명령을 사용하여 수행할 수 있습니다.


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

이 매개 변수는 인스턴스 를 만드는 동안에도 설정할 수 있습니다.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>네트워크 보안 그룹에 인바운드 규칙 추가 

이 단계는 Azure 포털을 통해 수행하거나 PowerShell 명령을 사용하여 수행할 수 있으며 관리되는 인스턴스에 대해 서브넷이 준비된 후 언제든지 수행할 수 있습니다.

자세한 내용은 [네트워크 보안 그룹의 공용 끝점 트래픽 허용을](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)참조하십시오.


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>기존 단일 인스턴스를 인스턴스 풀 내부로 이동 
 
풀 안팎으로 인스턴스를 이동하는 것은 공개 미리 보기 제한 사항 중 하나입니다. 사용할 수 있는 해결 방법은 풀 외부의 인스턴스에서 이미 풀에 있는 인스턴스로 데이터베이스의 정시 복원을 기반으로 합니다. 

두 인스턴스모두 동일한 구독 및 리전에 있어야 합니다. 지역 간 및 구독 간 복원은 현재 지원되지 않습니다.

이 프로세스에는 가동 중지 시간이 있습니다.

기존 데이터베이스를 이동하려면 다음을 수행합니다.

1. 마이그레이션하는 관리되는 인스턴스에서 워크로드를 일시 중지합니다.
2. 스크립트를 생성하여 시스템 데이터베이스를 만들고 인스턴스 풀 내에 있는 인스턴스에서 실행합니다.
3. 단일 인스턴스에서 풀의 인스턴스까지 각 데이터베이스의 시점 복원을 수행합니다.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. 응용 프로그램을 새 인스턴스로 가리키고 워크로드를 다시 시작합니다.

데이터베이스가 여러 개인 경우 각 데이터베이스에 대해 프로세스를 반복합니다.


## <a name="next-steps"></a>다음 단계

- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [관리형 인스턴스 VNet 구성을](sql-database-managed-instance-connectivity-architecture.md)참조하십시오.
- 관리되는 인스턴스를 만들고 백업 파일에서 데이터베이스를 복원하는 빠른 시작의 경우 [관리되는 인스턴스 만들기를](sql-database-managed-instance-get-started.md)참조하십시오.
- 마이그레이션을 위해 Azure 데이터베이스 마이그레이션 서비스(DMS)를 사용하는 자습서의 경우 [DMS를 사용하여 관리되는 인스턴스 마이그레이션을](../dms/tutorial-sql-server-to-managed-instance.md)참조하십시오.
- 기본 제공 문제 해결 인텔리전스를 사용하여 관리되는 인스턴스 데이터베이스 성능에 대한 고급 모니터링은 [Azure SQL Analytics를 사용하여 Azure SQL Database 모니터를](../azure-monitor/insights/azure-sql.md)참조하십시오.
- 가격 정보는 [SQL Database 관리 인스턴스 가격 책정을](https://azure.microsoft.com/pricing/details/sql-database/managed/)참조하십시오.
