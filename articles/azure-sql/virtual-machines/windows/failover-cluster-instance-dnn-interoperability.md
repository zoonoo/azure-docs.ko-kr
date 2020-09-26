---
title: SQL Server FCI와의 기능 상호 운용성 & DNN
description: 'Azure Vm의 SQL Server에서 장애 조치 (failover) 클러스터 인스턴스를 사용 하 여 특정 SQL Server 기능 및 DNN (분산 네트워크 이름) 리소스를 사용할 때의 추가 고려 사항에 대해 알아봅니다. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: ca782e9949f990857db408919cac342d7f712d2b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272619"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>SQL Server FCI와의 기능 상호 운용성 & DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

하드 코드 된 VNN (가상 네트워크 이름)을 사용 하는 특정 SQL Server 기능이 있습니다. 따라서 장애 조치 (failover) 클러스터 인스턴스와 DNN (분산 네트워크 이름) 리소스를 사용 하 고 Azure Vm에서 SQL Server 하는 경우 몇 가지 추가 고려 사항이 있습니다. 

이 문서에서는 DNN 리소스를 사용 하는 경우 네트워크 별칭을 구성 하는 방법과 추가 고려 사항이 필요한 SQL Server 기능에 대해 알아봅니다.

## <a name="create-network-alias-fci"></a>네트워크 별칭 (FCI) 만들기

일부 서버 쪽 구성 요소는 하드 코드 된 VNN 값을 사용 하며, 제대로 작동 하려면 VNN을 DNN DNS 이름에 매핑하는 네트워크 별칭이 필요 합니다. [서버 별칭 만들기](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) 의 단계에 따라 VNN을 DNN DNS 이름에 매핑하는 별칭을 만듭니다. 

기본 인스턴스의 경우 vnn을 DNN DNS 이름에 직접 매핑할 수 있습니다. 예를 들어 VNN = DNN DNS 이름입니다.
예를 들어 VNN 이름이이 고 `FCI1` , 인스턴스 이름이이 `MSSQLSERVER` 고, DNN가 (이전에 연결 된 클라이언트)이 고, 이제에 연결 된 경우 `FCI1DNN` `FCI` `FCI1DNN` vnn을 `FCI1` DNN에 매핑합니다 `FCI1DNN` . 

명명 된 인스턴스의 경우에는 전체 인스턴스에 대해 네트워크 별칭 매핑을 수행 해야 합니다 `VNN\Instance`  =  `DNN\Instance` . 예를 들어 VNN 이름이이 고 `FCI1` , 인스턴스 이름이이 `instA` 고, DNN가 (이전에 연결 된 클라이언트)이 고, 이제에 연결 된 경우 `FCI1DNN` `FCI1\instA` `FCI1DNN\instaA` vnn을 `FCI1\instaA` DNN에 매핑합니다 `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>클라이언트 드라이버

ODBC, OLEDB, ADO.NET, JDBC, PHP 및 Node.js 드라이버의 경우 사용자는 연결 문자열에서 서버 이름으로 DNN DNS 이름을 명시적으로 지정 해야 합니다. 장애 조치 (failover) 시 빠른 연결을 보장 하려면 `MultiSubnetFailover=True` SQL 클라이언트에서 지 원하는 경우 연결 문자열에를 추가 합니다. 

## <a name="tools"></a>도구

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)및 [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) 사용자는 연결 문자열에서 서버 이름으로 DNN DNS 이름을 명시적으로 지정 해야 합니다. 

## <a name="availability-groups-and-fci"></a>가용성 그룹 및 FCI

장애 조치 (failover) 클러스터 인스턴스를 복제본 중 하나로 사용 하 여 Always On 가용성 그룹을 구성할 수 있습니다. 이 구성에서 FCI 복제본의 미러링 끝점 URL은 FCI DNN를 사용 해야 합니다. 마찬가지로 FCI가 읽기 전용 복제본으로 사용 되는 경우 fci 복제본에 대 한 읽기 전용 라우팅은 FCI DNN을 사용 해야 합니다. 

미러링 끝점의 형식은 `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` 입니다. 

예를 들어, DNN DNS 이름이이 `dnnlsnr` 고 `5022` 가 fci의 미러링 끝점 포트인 경우 끝점 URL을 만드는 Transact-sql (t-sql) 코드 조각은 다음과 같습니다. 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

마찬가지로 읽기 전용 라우팅 URL의 형식은 `TCP://<DNN DNS name>:<SQL Server instance port>` 입니다. 

예를 들어 DNN DNS 이름이이 고 `dnnlsnr` `1444` 가 읽기 전용 대상 SQL Server fci에서 사용 하는 포트인 경우 읽기 전용 라우팅 URL을 만드는 t-sql 코드 조각은 다음과 같습니다. 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

기본 1433 포트인 경우 URL에서 포트를 생략할 수 있습니다. 명명 된 인스턴스의 경우 명명 된 인스턴스에 대 한 정적 포트를 구성 하 고 읽기 전용 라우팅 URL에서 지정 합니다.  

## <a name="replication"></a>복제

복제에는 게시자, 배포자, 구독자의 세 가지 구성 요소가 있습니다. 이러한 구성 요소는 장애 조치 (failover) 클러스터 인스턴스일 수 있습니다. FCI VNN은 복제 구성에서 강력 하 고 암시적으로 사용 되기 때문에 복제 작업을 수행 하려면 DNN에 VNN을 매핑하는 네트워크 별칭이 필요할 수 있습니다. 

복제 내에서 FCI 이름으로 VNN 이름을 계속 사용 하지만 *복제를 구성 하기 전에*다음과 같은 원격 상황에서 네트워크 별칭을 만듭니다.

| **복제 구성 요소 (DNN를 사용 하는 FCI)** | **원격 구성 요소** | **네트워크 별칭 맵** | **네트워크 맵이 있는 서버**| 
|---------|---------|---------|-------- | 
|게시자 | 배포자 | 게시자 VNN에서 게시자로 DNN| 배포자| 
|배포자|가입자 |배포자 VNN에서 배포자로 DNN| 가입자 | 
|배포자|게시자 | 배포자 VNN에서 배포자로 DNN | 게시자| 
|가입자| 배포자| 구독자 VNN에서 구독자로 DNN | 배포자| 

예를 들어 복제 토폴로지에서 DNN를 사용 하 여 FCI로 구성 된 게시자가 있고 배포자가 원격 이라고 가정 합니다. 이 경우 게시자 VNN을 게시자 DNN 매핑하기 위해 배포자 서버에 네트워크 별칭을 만듭니다. 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="SQL Server 구성 관리자를 사용 하 여 DNN DNS 이름을 네트워크 별칭으로 구성 합니다." :::

다음 이미지 예제와 같이 명명 된 인스턴스에 대 한 전체 인스턴스 이름을 사용 합니다. 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="명명 된 인스턴스에 대 한 네트워크 별칭을 구성할 때 전체 인스턴스 이름을 사용 합니다." :::

## <a name="database-mirroring"></a>데이터베이스 미러링

FCI를 사용 하 여 데이터베이스 미러링을 데이터베이스 미러링 파트너로 구성할 수 있습니다. SQL Server Management Studio GUI 아닌 [transact-sql (t-sql)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) 을 사용 하 여 구성 합니다. T-sql을 사용 하면 VNN 대신 DNN를 사용 하 여 데이터베이스 미러링 끝점을 만들 수 있습니다. 

예를 들어 DNN DNS 이름이이 `dnnlsnr` 고 데이터베이스 미러링 끝점이 7022 인 경우 다음 t-sql 코드 조각은 데이터베이스 미러링 파트너를 구성 합니다. 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

클라이언트 액세스의 경우 장애 조치 ( **failover) 파트너** 속성은 데이터베이스 미러링 장애 조치 (failover)를 처리할 수 있지만 fci 장애 조치 (failover) 

## <a name="msdtc"></a>MSDTC

FCI는 MSDTC (Microsoft DTC(Distributed Transaction Coordinator))에 의해 조정 되는 분산 트랜잭션에 참여할 수 있습니다. 클러스터 된 msdtc와 로컬 MSDTC는 모두 FCI DNN에서 지원 되지만, Azure에서 부하 분산 장치는 클러스터 된 MSDTC에 여전히 필요 합니다. FCI에 정의 된 DNN는 Azure에서 클러스터형 MSDTC에 대 한 Azure Load Balancer 요구 사항을 대체 하지 않습니다. 

## <a name="filestream"></a>FileStream

FCI의 데이터베이스에는 FileStream이 지원 되지만 DNN에서 파일 시스템 Api를 사용 하 여 FileStream 또는 FileTable에 액세스 하는 것은 지원 되지 않습니다. 

## <a name="linked-servers"></a>연결된 서버

FCI DNN 연결 된 서버를 사용 하는 것이 지원 됩니다. DNN를 직접 사용 하 여 연결 된 서버를 구성 하거나 네트워크 별칭을 사용 하 여 VNN을 DNN에 매핑합니다. 


예를 들어 명명 된 인스턴스에 대 한 DNN DNS 이름을 사용 하 여 연결 된 서버를 만들려면 `dnnlsnr` `insta1` 다음 Transact-sql (t-sql) 명령을 사용 합니다.

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

또는 VNN (가상 네트워크 이름)을 사용 하 여 연결 된 서버를 만들 수는 있지만 VNN을 DNN 매핑하기 위해 네트워크 별칭을 정의 해야 합니다. 

예를 들어 인스턴스 이름 `insta1` , vnn 이름 `vnnname` 및 DNN name의 경우 `dnnlsnr` 다음 transact-sql (t-sql) 명령을 사용 하 여 vnn을 사용 하 여 연결 된 서버를 만듭니다.

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

그런 다음 매핑할 네트워크 별칭을 만듭니다 `vnnname\insta1` `dnnlsnr\insta1` . 



## <a name="frequently-asked-questions"></a>질문과 대답


- DNN에서 지원 되는 SQL Server 버전은 무엇 인가요? 

   SQL Server 2019 CU2 이상입니다.

- DNN가 사용 되는 경우 예상 되는 장애 조치 (failover) 시간은 무엇 인가요?

   DNN의 경우 장애 조치 (failover) 시간은 추가 된 시간 없이 FCI 장애 조치 (failover) 시간입니다 (Azure Load Balancer를 사용 하는 프로브 시간 등).

- SQL 클라이언트에서 OLEDB 및 ODBC를 사용 하 여 DNN를 지원 하기 위한 버전 요구 사항이 있나요?

   `MultiSubnetFailover=True`DNN에 대 한 연결 문자열 지원을 권장 합니다. SQL Server 2012 (11.x)부터 사용할 수 있습니다.

- DNN를 사용 하는 데 필요한 SQL Server 구성 변경 내용이 있나요? 

   SQL Server DNN를 사용 하기 위해 구성 변경이 필요 하지는 않지만 일부 SQL Server 기능을 사용 하려면 더 많은 주의가 필요할 수 있습니다. 

- DNN는 다중 서브넷 클러스터를 지원 하나요?

   예. 클러스터는 서브넷에 관계 없이 클러스터에 있는 모든 노드의 실제 IP 주소를 사용 하 여 DNS의 DNN을 바인딩합니다. SQL 클라이언트는 서브넷에 관계 없이 DNS 이름의 모든 IP 주소를 시도 합니다. 



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요. 

- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 장애 조치 (failover) 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

