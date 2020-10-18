---
title: 가용성 그룹에 대 한 DNN 수신기 구성
description: DNN (분산 네트워크 이름) 수신기를 구성 하 여 VNN (가상 네트워크 이름) 수신기를 대체 하 고 Azure VM의 SQL Server에서 Always On 가용성 그룹으로 트래픽을 라우팅하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: abfcd6a13bc5e8ad262fe47111eb680ad00a34df
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168976"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>가용성 그룹에 대 한 DNN 수신기 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Vm에 대 한 SQL Server를 사용 하 여 분산 네트워크 이름 (DNN)은 트래픽을 적절 한 클러스터 된 리소스로 라우팅합니다. Azure Load Balancer 없이는 VNN (가상 네트워크 이름) 수신기 보다 AG (Always On 가용성 그룹)에 연결 하는 쉬운 방법을 제공 합니다. 

이 문서에서는 DNN 수신기를 구성 하 여 고가용성 및 HADR (재해 복구)을 위해 Azure Vm의 SQL Server를 사용 하 여 VNN 수신기를 대체 하 고 트래픽을 가용성 그룹으로 라우팅하는 방법을 설명 합니다. 

DNN listener 기능은 현재 Windows Server 2016 이상에서 SQL Server 2019 CU8부터만 사용할 수 있습니다. 

대체 연결 옵션의 경우 [Vnn 수신기를 고려 하 고 대신 Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) 합니다. 

## <a name="overview"></a>개요

DNN (분산 네트워크 이름) 수신기는 [SQL Server vm에서 Always On 가용성 그룹](availability-group-overview.md)와 함께 사용 될 때 기존 vnn (가상 네트워크 이름) 가용성 그룹 수신기를 대체 합니다. 이렇게 하면 트래픽을 라우팅하는 데 필요한 Azure Load Balancer, 배포 및 유지 관리를 간소화 하 고 장애 조치 (failover)를 향상 시킬 필요가 없습니다. 

DNN 수신기를 사용 하 여 기존 VNN 수신기를 교체 하거나 기존 VNN 수신기와 함께 사용 하 여 가용성 그룹에 두 개의 고유 연결 지점이 있습니다. 하나는 VNN 수신기 이름 (기본값이 아닌 경우 포트)을 사용 하 고 다른 하나는 DNN 수신기 이름 및 포트를 사용 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하기 전에 다음이 준비되어 있어야 합니다.

- CU8 이상, Windows Server 2016 이상에서 SQL Server 2019
- 분산 네트워크 이름이 [HADR 솔루션에 대 한 적절 한 연결 옵션](hadr-cluster-best-practices.md#connectivity)이라고 결정 했습니다.
- [Always On 가용성 그룹](availability-group-overview.md)을 구성 했습니다. 
- 최신 버전의 [PowerShell](/powershell/azure/install-az-ps)을 설치 했습니다. 


## <a name="create-script"></a>스크립트 만들기

PowerShell을 사용 하 여 DNN (분산 네트워크 이름) 리소스를 만들고 가용성 그룹에 연결 합니다. 

이렇게 하려면 다음 단계를 수행하세요. 

1. 메모장과 같은 텍스트 편집기를 엽니다. 
1. 다음 스크립트를 복사하여 붙여넣습니다. 

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. 등의 파일로 스크립트를 저장 `.ps1` `add_dnn_listener.ps1` 합니다. 


## <a name="execute-script"></a>스크립트 실행

DNN 수신기를 만들려면 가용성 그룹, 수신기 이름 및 포트의 이름에 대 한 매개 변수를 전달 하는 스크립트를 실행 합니다. 

예를 들어 가용성 그룹 이름 `ag1` , 수신기 이름 `dnnlsnr` 및 수신기 포트를로 가정 하 고 `6789` 다음 단계를 수행 합니다. 

1. 명령 프롬프트 또는 PowerShell과 같은 명령줄 인터페이스 도구를 엽니다. 
1. 스크립트를 저장 `.ps1` 한 위치 (예: c:\documents.)로 이동 합니다. 
1. 스크립트를 실행 ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` 합니다. 예를 들면 다음과 같습니다. 

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>수신기 확인

SQL Server Management Studio 또는 Transact-sql을 사용 하 여 DNN 수신기가 성공적으로 만들어졌는지 확인 합니다. 

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) 에서 **가용성 그룹 수신기** 를 확장 하 여 DNN 수신기를 확인 합니다. 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="SQL Server Management Studio의 가용성 그룹 수신기에서 DNN 수신기 보기 (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Transact-sql을 사용 하 여 DNN 수신기의 상태를 확인 합니다. 

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

`1`에 대 한 값은 `is_distributed_network_name` 수신기가 DNN (분산 네트워크 이름) 수신기 임을 나타냅니다. 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="SQL Server Management Studio의 가용성 그룹 수신기에서 DNN 수신기 보기 (SSMS)":::


## <a name="update-connection-string"></a>연결 문자열 업데이트

DNN 수신기에 연결 하도록 응용 프로그램에 대 한 연결 문자열을 업데이트 합니다. 장애 조치 (failover) 시 빠른 연결을 보장 하려면 `MultiSubnetFailover=True` SQL 클라이언트에서 지 원하는 경우 연결 문자열에를 추가 합니다. 

## <a name="test-failover"></a>테스트 장애 조치

가용성 그룹의 장애 조치 (failover)를 테스트 하 여 기능을 보장 합니다. 

장애 조치 (failover)를 테스트 하려면 다음 단계를 수행 합니다. 

1. [SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)를 사용 하 여 DNN 수신기 또는 복제본 중 하나에 연결 합니다. 
1. **개체 탐색기**에서 **Always On 가용성 그룹** 을 확장 합니다. 
1. 가용성 그룹을 마우스 오른쪽 단추로 클릭 하 고 **장애 조치** (failover)를 선택 하 여 **장애 조치 마법사**를 엽니다. 
1. 메시지의 지시에 따라 장애 조치 (failover) 대상을 선택 하 고 보조 복제본에 대 한 가용성 그룹을 장애 조치 (failover) 합니다. 
1. 데이터베이스가 새 주 복제본에서 동기화 된 상태 인지 확인 합니다. 
1. 필드 원래 주 복제본 또는 다른 보조 복제본으로 장애 복구 (failback) 합니다. 

## <a name="test-connectivity"></a>연결 테스트

다음 단계를 사용 하 여 DNN 수신기에 대 한 연결을 테스트 합니다.

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 엽니다.
1. DNN 수신기에 연결 합니다. 
1. 새 쿼리 창을 열고를 실행 하 여 연결 된 복제본을 확인 `SELECT @@SERVERNAME` 합니다. 
1. 가용성 그룹을 다른 복제본으로 장애 조치 (failover) 합니다.
1. 적절 한 시간 후를 실행 하 여 `SELECT @@SERVERNAME` 가용성 그룹이 현재 다른 복제본에 호스트 되는지 확인 합니다. 


## <a name="limitations"></a>제한 사항

- 현재 가용성 그룹에 대 한 DNN 수신기는 Windows Server 2016 이상에서 SQL Server 2019 CU8 이상 에서만 지원 됩니다. 
- 다른 SQL Server 기능 및 DNN를 사용 하 여 가용성 그룹으로 작업 하는 경우 추가 고려 사항이 있을 수 있습니다. 자세한 내용은 [DNN 상호 운용성을 사용 하는 AG](availability-group-dnn-interoperability.md)를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

Azure에서 HADR 기능을 SQL Server 하는 방법에 대 한 자세한 내용은 [가용성 그룹](availability-group-overview.md) 및 [장애 조치 (Failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)를 참조 하세요. 또한 고가용성 및 재해 복구를 위한 환경을 구성 하는 [모범 사례](hadr-cluster-best-practices.md) 를 배울 수 있습니다. 


