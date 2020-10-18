---
title: 가용성 그룹 및 DNN 수신기와의 기능 상호 운용성
description: 'Azure Vm의 SQL Server에서 Always On 가용성 그룹을 사용 하 여 특정 SQL Server 기능 및 DNN (분산 네트워크 이름) 수신기로 작업할 때의 추가 고려 사항에 대해 알아봅니다. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 567e1696bb06b3237c30c45384b8049ff82b5848
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168921"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>AG 및 DNN listener와의 기능 상호 운용성 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

하드 코드 된 VNN (가상 네트워크 이름)을 사용 하는 특정 SQL Server 기능이 있습니다. 따라서 Always On 가용성 그룹과 Azure Vm의 SQL Server 함께 DNN (분산 네트워크 이름) 수신기를 사용 하는 경우 몇 가지 추가 고려 사항이 있을 수 있습니다. 

이 문서에서는 가용성 그룹 DNN 수신기와 SQL Server 기능 및 상호 운용성에 대해 자세히 설명 합니다. 


## <a name="client-drivers"></a>클라이언트 드라이버

ODBC, OLEDB, ADO.NET, JDBC, PHP 및 Node.js 드라이버의 경우 사용자는 DNN 수신기 이름 및 포트를 연결 문자열의 서버 이름으로 명시적으로 지정 해야 합니다. 장애 조치 (failover) 시 빠른 연결을 보장 하려면 `MultiSubnetFailover=True` SQL 클라이언트에서 지 원하는 경우 연결 문자열에를 추가 합니다. 

## <a name="tools"></a>도구

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)및 [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) 사용자는 수신기에 연결 하기 위해 연결 문자열의 서버 이름으로 DNN 수신기 이름 및 포트를 명시적으로 지정 해야 합니다. 

SSMS (SQL Server Management Studio) GUI를 통해 DNN 수신기를 만드는 것은 현재 지원 되지 않습니다. 


## <a name="availability-groups-and-fci"></a>가용성 그룹 및 FCI

FCI (장애 조치 (failover) 클러스터 인스턴스)를 복제본 중 하나로 사용 하 여 Always On 가용성 그룹을 구성할 수 있습니다. 이 구성이 DNN 수신기에서 작동 하려면 [DNN를 사용 해야](failover-cluster-instance-distributed-network-name-dnn-configure.md) 합니다. fci 가상 IP 주소를 AG DNN IP 목록에 배치할 수 있는 방법이 없기 때문입니다. 

이 구성에서 FCI 복제본의 미러링 끝점 URL은 FCI DNN를 사용 해야 합니다. 마찬가지로 FCI가 읽기 전용 복제본으로 사용 되는 경우 fci 복제본에 대 한 읽기 전용 라우팅은 FCI DNN을 사용 해야 합니다. 

미러링 끝점의 형식은 `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` 입니다. 

예를 들어 FCI DNN DNS 이름이이 `dnnlsnr` 고 `5022` 가 fci의 미러링 끝점 포트인 경우 끝점 URL을 만드는 Transact-sql (t-sql) 코드 조각은 다음과 같습니다. 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

마찬가지로 읽기 전용 라우팅 URL의 형식은 `TCP://<FCI DNN DNS name>:<SQL Server instance port>` 입니다. 

예를 들어 DNN DNS 이름이이 고 `dnnlsnr` `1444` 가 읽기 전용 대상 SQL Server fci에서 사용 하는 포트인 경우 읽기 전용 라우팅 URL을 만드는 t-sql 코드 조각은 다음과 같습니다. 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

기본 1433 포트인 경우 URL에서 포트를 생략할 수 있습니다. 명명 된 인스턴스의 경우 명명 된 인스턴스에 대 한 정적 포트를 구성 하 고 읽기 전용 라우팅 URL에서 지정 합니다.  

## <a name="distributed-availability-group"></a>분산 가용성 그룹

분산 된 가용성 그룹은 현재 DNN 수신기에서 지원 되지 않습니다. 

## <a name="replication"></a>복제

트랜잭션, 병합 및 스냅숏 복제는 모두 VNN 수신기를 수신기에 연결 된 복제 개체의 DNN 수신기 및 포트로 대체 하도록 지원 합니다. 

가용성 그룹에서 복제를 사용 하는 방법에 대 한 자세한 내용은 [게시자 및 ag](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [구독자, ag](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server), [배포자 및 ag](/sql/relational-databases/replication/configure-distribution-availability-group)를 참조 하세요.

## <a name="msdtc"></a>MSDTC

로컬 및 클러스터형 MSDTC가 모두 지원 되지만 MSDTC는 동적 포트를 사용 하 여 HA 포트를 구성 하는 표준 Azure Load Balancer 필요 합니다. 따라서 VM은 표준 IP 예약을 사용 하거나 인터넷에 노출 될 수 없습니다. 

RPC 끝점 매퍼 포트 135에 대 한 규칙과 실제 MSDTC 포트의 두 가지 규칙을 정의 합니다. 장애 조치 (failover) 후 새 노드에서 LB 규칙을 변경한 후 새 MSDTC 포트로 수정 합니다. 

MSDTC가 로컬 인 경우 아웃 바운드 통신을 허용 해야 합니다. 

## <a name="distributed-query"></a>분산 쿼리 

분산 쿼리는 AG DNN 수신기 및 포트를 사용 하 여 구성할 수 있는 연결 된 서버에 의존 합니다. 포트가 1433이 아니면 연결 된 서버를 구성할 때 SQL Server Management Studio (SSMS)에서 **기타 데이터 원본 사용** 옵션을 선택 합니다. 

## <a name="filestream"></a>FileStream

Filestream은 지원 되지만, 사용자가 Windows 파일 API를 사용 하 여 범위 파일 공유에 액세스 하는 시나리오에는 지원 되지 않습니다. 

## <a name="filetable"></a>Filetable

Filetable은 지원 되지만, 사용자가 Windows 파일 API를 사용 하 여 범위 파일 공유에 액세스 하는 시나리오에는 지원 되지 않습니다. 

## <a name="linked-servers"></a>연결된 서버

AG DNN 수신기 이름 및 포트를 사용 하 여 연결 된 서버를 구성 합니다. 포트가 1433이 아니면 연결 된 서버를 구성할 때 SQL Server Management Studio (SSMS)에서 **기타 데이터 원본 사용** 옵션을 선택 합니다. 


## <a name="frequently-asked-questions"></a>질문과 대답


- AG DNN 수신기를 지 원하는 SQL Server 버전은 무엇 인가요? 

   SQL Server 2019 CU8 이상입니다.

- DNN 수신기가 사용 되는 경우 예상 되는 장애 조치 (failover) 시간은 무엇 인가요?

   DNN 수신기의 경우 장애 조치 (failover) 시간은 추가 시간 없이 AG 장애 조치 (failover) 시간입니다 (예: Azure Load Balancer 사용 하는 프로브 시간).

- SQL 클라이언트에서 OLEDB 및 ODBC를 사용 하 여 DNN를 지원 하기 위한 버전 요구 사항이 있나요?

   `MultiSubnetFailover=True`DNN 수신기에 대 한 연결 문자열 지원을 권장 합니다. SQL Server 2012 (11.x)부터 사용할 수 있습니다.

- DNN 수신기를 사용 하는 데 필요한 SQL Server 구성 변경 내용이 있나요? 

   SQL Server DNN를 사용 하기 위해 구성 변경이 필요 하지는 않지만 일부 SQL Server 기능을 사용 하려면 더 많은 주의가 필요할 수 있습니다. 

- DNN는 다중 서브넷 클러스터를 지원 하나요?

   예. 클러스터는 서브넷에 관계 없이 가용성의 모든 복제본에 대 한 실제 IP 주소를 사용 하 여 DNS의 DNN을 바인딩합니다. SQL 클라이언트는 서브넷에 관계 없이 DNS 이름의 모든 IP 주소를 시도 합니다. 



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요. 

- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)   
- [Always on 가용성 그룹](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

