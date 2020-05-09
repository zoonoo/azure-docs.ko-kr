---
title: Azure Site Recovery를 사용 하 여 복제에서 디스크 제외
description: Azure Site Recovery를 사용 하 여 복제에서 Azure로 디스크를 제외 하는 방법입니다.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: abecc19cac57a4a95d01b7a7ec076259088b101b
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900275"
---
# <a name="exclude-disks-from-disaster-recovery"></a>재해 복구에서 디스크 제외

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 온-프레미스에서 Azure로 재해 복구 하는 동안 복제에서 디스크를 제외 하는 방법을 설명 합니다. 여러 가지 이유로 인해 복제에서 디스크를 제외할 수 있습니다.

- 따라서 제외 된 디스크에 대 한 중요 하지 않은 데이터 변동 복제 되지 않습니다.
- 사용 중인 복제 대역폭 또는 대상 쪽 리소스를 최적화 합니다.
- 필요 하지 않은 데이터를 복제 하지 않고 저장소 및 네트워크 리소스를 저장 합니다.
- Azure Vm이 복제 제한 Site Recovery에 도달 했습니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

테이블에 요약 된 대로 복제에서 디스크를 제외할 수 있습니다.

**Azure 간** | **VMware에서 Azure로** | **Hyper-V에서 Azure로** 
--- | --- | ---
예 | 예 | 예 

## <a name="exclude-limitations"></a>제외 제한 사항

**제한 사항** | **Azure VM** | **VMware VM** | **Hyper-V VM**
--- | --- | ---
**디스크 유형** | 기본 디스크는 복제에서 제외할 수 있습니다.<br/><br/> 운영 체제 디스크 또는 동적 디스크는 제외할 수 없습니다. 임시 디스크는 기본적으로 제외 됩니다. | 기본 디스크는 복제에서 제외할 수 있습니다.<br/><br/> 운영 체제 디스크 또는 동적 디스크는 제외할 수 없습니다. | 기본 디스크는 복제에서 제외할 수 있습니다.<br/><br/> 운영 체제 디스크를 제외할 수 없습니다. 동적 디스크는 제외하지 않는 것이 좋습니다. Site Recovery 게스트 VM에서 기본 또는 동적 VHS를 식별할 수 없습니다. 모든 종속 동적 볼륨 디스크가 제외 되지 않으면 보호 된 동적 디스크가 장애 조치 (failover) VM에서 디스크에 오류가 발생 하 고 해당 디스크의 데이터에 액세스할 수 없게 됩니다.
**디스크 복제** | 복제 하는 디스크는 제외할 수 없습니다.<br/><br/> VM에 대 한 복제를 사용 하지 않도록 설정 하 고 다시 활성화 합니다. |  복제 하는 디스크는 제외할 수 없습니다. |  복제 하는 디스크는 제외할 수 없습니다.
**모바일 서비스 (VMware)** | 관련 없음 | 모바일 서비스가 설치 된 Vm 에서만 디스크를 제외할 수 있습니다.<br/><br/> 즉, 디스크를 제외 하려는 Vm에 모바일 서비스를 수동으로 설치 해야 합니다. 밀어넣기 설치 메커니즘은 복제를 사용 하도록 설정한 후에만 모바일 서비스를 설치 하기 때문에 사용할 수 없습니다. | 관련이 없습니다.
**추가/제거** | 관리 디스크를 사용 하 여 복제를 사용 하는 Azure Vm에 관리 디스크를 추가할 수 있습니다. 복제를 사용 하는 Azure Vm에서 디스크를 제거할 수 없습니다. | 복제를 사용 하도록 설정한 후에는 디스크를 추가 하거나 제거할 수 없습니다. 디스크를 추가 하려면 복제를 사용 하지 않도록 설정한 다음 다시 활성화 합니다. | 복제를 사용 하도록 설정한 후에는 디스크를 추가 하거나 제거할 수 없습니다. 복제를 사용 하지 않도록 설정한 다음 다시 활성화 합니다.
**장애 조치 ** | 앱에 제외 된 디스크가 필요한 경우 장애 조치 (failover) 후 복제 된 앱을 실행할 수 있도록 디스크를 수동으로 만들어야 합니다.<br/><br/> 또는 Azure automation을 복구 계획에 통합 하 여 VM 장애 조치 (failover) 중에 디스크를 만들 수 있습니다. | 앱에 필요한 디스크를 제외 하는 경우 장애 조치 (failover) 후 Azure에서 수동으로 만듭니다. | 앱에 필요한 디스크를 제외 하는 경우 장애 조치 (failover) 후 Azure에서 수동으로 만듭니다.
**온-프레미스 장애 복구-수동으로 만든 디스크** | 관련 없음 | **Windows vm**: Azure에서 수동으로 만든 디스크는 장애 복구 되지 않습니다. 예를 들어 3 개의 디스크를 장애 조치 (failover) 하 고 Azure VM에 두 개의 디스크를 직접 만들면 장애 조치 된 디스크 세 개만 장애 복구 됩니다.<br/><br/> **Linux vm**: Azure에서 수동으로 만든 디스크는 장애 복구 됩니다. 예를 들어 3 개의 디스크를 장애 조치 (failover) 하 고 Azure VM에 두 개의 디스크를 만들면 5 개가 모두 장애 복구 됩니다. 장애 복구에서 수동으로 만든 디스크는 제외할 수 없습니다. | Azure에서 수동으로 만든 디스크는 장애 복구 되지 않습니다. 예를 들어 3 개의 디스크를 장애 조치 (failover) 하 고 Azure VM에 두 개의 디스크를 직접 만들면 장애 조치 된 디스크 세 개만 장애 복구 됩니다.
**온-프레미스 장애 복구 (failback)-제외 된 디스크** | 관련 없음 | 원래 컴퓨터로 장애 복구 (failback) 하는 경우 VM 디스크 구성에 제외 된 디스크가 포함 되지 않습니다. VMware에서 Azure로 복제에서 제외 된 디스크는 장애 복구 VM에서 사용할 수 없습니다. | 장애 복구 (failback)가 원래 Hyper-v 위치인 경우 장애 복구 VM 디스크 구성은 원래 원본 VM 디스크와 동일 하 게 유지 됩니다. Hyper-v 사이트에서 Azure 복제로 제외 된 디스크는 장애 복구 VM에서 사용할 수 있습니다.



## <a name="typical-scenarios"></a>일반적인 시나리오

제외 하기에 적합 한 데이터 변동의 예에는 페이징 파일 (파일 시스템)에 대 한 쓰기와 Microsoft SQL Server의 tempdb 파일에 대 한 쓰기가 포함 됩니다. 워크 로드 및 저장소 하위 시스템에 따라 페이징 및 tempdb 파일은 상당한 변동의 변동를 등록할 수 있습니다. 이러한 유형의 데이터를 Azure에 복제 하는 것은 리소스를 많이 사용 합니다.

- 운영 체제와 페이징 파일을 모두 포함 하는 단일 가상 디스크를 사용 하 여 VM에 대 한 복제를 최적화 하려면 다음 작업을 수행 합니다.
    1. 단일 가상 디스크를 두 개의 가상 디스크로 분할합니다. 한 가상 디스크에는 운영 체제가 있고 다른 하나에는 페이징 파일이 있습니다.
    2. 복제에서 페이징 파일 디스크를 제외합니다.

- Microsoft SQL Server tempdb 파일과 시스템 데이터베이스 파일을 모두 포함 하는 디스크에 대 한 복제를 최적화 하기 위해 다음을 할 수 있습니다.
    1. 별도의 두 디스크에 tempdb와 시스템 데이터베이스를 유지합니다.
    2. 복제에서 tempdb 디스크를 제외합니다.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>예제 1: SQL Server tempdb 디스크 제외

Tempdb를 제외 하려는 Windows VM-* * SalesDB * * * 원본 SQL Server에 대 한 디스크 제외, 장애 조치 (failover) 및 장애 조치 (failover)를 처리 하는 방법을 살펴보겠습니다. 

### <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

원본 Windows VM SalesDB에 이러한 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 운영 체제 디스크.
DB-Disk1| Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 Database1.
DB-Disk2(보호에서 디스크 제외됨) | Disk2 | E:\ | 임시 파일.
DB-Disk3(보호에서 디스크 제외됨) | Disk3 | F:\ | SQL tempdb 데이터베이스.<br/><br/> 폴더 경로-F:\MSSQL\Data\. 장애 조치 (failover) 전에 폴더 경로를 기록해 둡니다.
DB-Disk4 | Disk4 |G:\ | 사용자 데이터베이스 2

1. SalesDB VM에 대 한 복제를 사용 하도록 설정 합니다.
2. 이러한 디스크의 데이터 변동이 임시 이기 때문에 복제에서 Disk2 및 Disk3를 제외 합니다. 


### <a name="handle-disks-during-failover"></a>장애 조치 (failover) 중 디스크 처리

디스크가 복제 되지 않으므로 Azure로 장애 조치 (failover) 하면 장애 조치 (failover) 후 생성 된 Azure VM에 이러한 디스크가 표시 되지 않습니다. Azure VM에는이 표에 요약 된 디스크가 있습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
Disk0 | C:\ | 운영 체제 디스크.
Disk1 | E:\ | 임시 저장소<br/><br/>Azure에서이 디스크를 추가 합니다. Disk2 및 Disk3가 복제에서 제외 되었으므로 E:는 사용 가능한 목록의 첫 번째 드라이브 문자입니다. Azure에서 E:를 임시 스토리지 볼륨에 할당합니다. 복제 된 디스크의 다른 드라이브 문자는 동일 하 게 유지 됩니다.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1
Disk3 | G:\ | 사용자 데이터베이스 2

이 예제에서 Disk3는 SQL tempdb 디스크가 복제에서 제외 되었으며 Azure VM에서 사용할 수 없기 때문에 SQL 서비스가 중지 된 상태 이며 F:\MSSQL\Data 경로가 필요 합니다. 이 경로는 두 가지 방법으로 만들 수 있습니다. 

- 장애 조치 (failover) 후 새 디스크를 추가 하 고 tempdb 폴더 경로를 할당 합니다.
- tempdb 폴더 경로에 기존 임시 스토리지 디스크를 사용합니다.

#### <a name="add-a-new-disk-after-failover"></a>장애 조치 (failover) 후 새 디스크 추가

1. 장애 조치(failover) 전에 SQL tempdb.mdf 및 tempdb.ldf 경로를 적어둡니다.
2. Azure Portal에서 장애 조치 (failover) Azure VM에 새 디스크를 추가 합니다. 디스크는 원본 SQL tempdb 디스크 (Disk3)와 크기가 같거나 커야 합니다.
3. Azure VM에 로그인 합니다.
4. 디스크 관리(diskmgmt.msc) 콘솔에서 새로 추가한 디스크를 초기화하고 포맷합니다.
5. SQL tempdb 디스크 (F:)에 사용 된 것과 동일한 드라이브 문자를 할당 합니다.
6. F: 볼륨(F:\MSSQL\Data)에 tempdb 폴더를 만듭니다.
7. 서비스 콘솔에서 SQL 서비스를 시작합니다.

#### <a name="use-an-existing-temporary-storage-disk"></a>기존 임시 저장소 디스크 사용 

1. 명령 프롬프트를 엽니다.
2. 명령 프롬프트에서 SQL Server를 복구 모드에서 실행합니다.

        Net start MSSQLSERVER /f / T3608

3. 다음 sqlcmd를 실행하여 tempdb 경로를 새 경로로 바꿉니다.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Microsoft SQL Server 서비스를 중지합니다.

        Net stop MSSQLSERVER
5. Microsoft SQL Server 서비스를 시작합니다.

        Net start MSSQLSERVER



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware Vm: 원래 위치로 장애 복구 하는 동안 디스크

이제 원래 온-프레미스 위치로 장애 복구 하는 경우 VMware Vm에서 디스크를 처리 하는 방법을 알아보겠습니다.

- **Azure에서 만든 디스크**:이 예제에서는 Windows vm을 사용 하기 때문에 azure에서 수동으로 만드는 디스크는 장애 복구 (failback) 하거나 VM을 다시 보호할 때 사이트에 다시 복제 되지 않습니다.
- **Azure의 임시 저장소 디스크**: 임시 저장소 디스크가 온-프레미스 호스트로 다시 복제 되지 않습니다.
- **제외 된 디스크**: VMware에서 Azure로의 복제에서 제외 된 디스크는 장애 복구 (failback) 후 온-프레미스 VM에서 사용할 수 없습니다.

VMware Vm을 원래 위치로 장애 복구 하기 전에 Azure VM 디스크 설정은 다음과 같습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
Disk0 | C:\ | 운영 체제 디스크.
Disk1 | E:\ | 임시 저장소.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 Database1.
Disk3 | G:\ | 사용자 Database2.

장애 복구 (failback) 후 원래 위치의 VMware VM에는 표에 요약 된 디스크가 있습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
Disk0 | C:\ | 운영 체제 디스크.
Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 Database1.
Disk2 | G:\ | 사용자 Database2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-v Vm: 원래 위치로 장애 복구 하는 동안 디스크

이제 원래 온-프레미스 위치로 장애 복구 하는 경우 Hyper-v Vm에서 디스크를 처리 하는 방법을 알아보겠습니다.

- **Azure에서 만든 디스크**: azure에서 수동으로 만드는 디스크는 장애 복구 (failback) 하거나 VM을 다시 보호할 때 사이트에 다시 복제 되지 않습니다.
- **Azure의 임시 저장소 디스크**: 임시 저장소 디스크가 온-프레미스 호스트로 다시 복제 되지 않습니다.
- **제외 된 디스크**: 장애 복구 (failback) 후 vm 디스크 구성이 원본 vm 디스크 구성과 같습니다. Hyper-v에서 Azure로의 복제에서 제외 된 디스크는 장애 복구 VM에서 사용할 수 있습니다.

Hyper-v Vm을 원래 위치로 장애 복구 하기 전에 Azure VM 디스크 설정은 다음과 같습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
Disk0 | C:\ | 운영 체제 디스크.
Disk1 | E:\ | 임시 저장소.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 Database1.
Disk3 | G:\ | 사용자 Database2.

Azure에서 온-프레미스 Hyper-v로 계획 된 장애 조치 (failover) 후에 원래 위치의 Hyper-v VM에는 테이블에 요약 된 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크 #** | **드라이브 문자** | **디스크 데이터 형식**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | 운영 체제 디스크.
DB-Disk1 | Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 Database1.
DB-Disk2(제외된 디스크) | Disk2 | E:\ | 임시 파일.
DB-Disk3(제외된 디스크) | Disk3 | F:\ | SQL tempdb 데이터베이스<br/><br/> 폴더 경로 (F:\MSSQL\Data\).
DB-Disk4 | Disk4 | G:\ | 사용자 데이터베이스 2


## <a name="example-2-exclude-the-paging-file-disk"></a>예 2: 페이징 파일 디스크 제외

원본 Windows VM에 대 한 디스크 제외, 장애 조치 (failover) 및 장애 조치 (failover)를 처리 하는 방법을 살펴보겠습니다. 여기에서 D 드라이브와 대체 드라이브 모두에서 페이지 파일 디스크를 제외 하려고 합니다.


### <a name="paging-file-on-the-d-drive"></a>D 드라이브의 페이징 파일

원본 VM에 이러한 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 운영 체제 디스크
DB-Disk1 (복제에서 제외) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

원본 VM의 페이징 파일 설정은 다음과 같습니다.

![원본 가상 컴퓨터의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. VM에 대 한 복제를 사용 하도록 설정 합니다.
2. 복제에서 DB Disk1를 제외 합니다.

#### <a name="disks-after-failover"></a>장애 조치 (failover) 후 디스크

장애 조치 (failover) 후 Azure VM에는 테이블에 요약 된 디스크가 있습니다.

**디스크 이름** | **게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 운영 체제 디스크
DB-Disk1 | Disk1 | D:\ | 임시 저장소/파일 시스템 <br/><br/> DB-Disk1 (D:) 제외 됨, D: 사용 가능한 목록의 첫 번째 드라이브 문자입니다.<br/><br/> Azure에서 D:를 임시 스토리지 볼륨에 할당합니다.<br/><br/> D:를 사용할 수 있기 때문에 VM 페이징 파일 설정은 동일 하 게 유지 됩니다.
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

Azure VM의 페이징 파일 설정은 다음과 같습니다.

![Azure Virtual Machine의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>다른 드라이브 (D:)의 페이징 파일

D 드라이브에 페이징 파일이 없는 예를 살펴보겠습니다.  

원본 VM에 이러한 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 운영 체제 디스크
DB-Disk1 (복제에서 제외) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

온-프레미스 VM의 페이징 파일 설정은 다음과 같습니다.

![온-프레미스 가상 머신의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. VM에 대 한 복제를 사용 하도록 설정 합니다.
2. 복제에서 DB Disk1를 제외 합니다.

#### <a name="disks-after-failover"></a>장애 조치 (failover) 후 디스크

장애 조치 (failover) 후 Azure VM에는 테이블에 요약 된 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크 #** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | 운영 체제 디스크
DB-Disk1 | Disk1 | D:\ | 임시 저장소<br/><br/> D:가 목록에서 사용 가능한 첫 번째 드라이브 문자이므로 Azure에서 D:를 임시 스토리지 볼륨에 할당합니다.<br/><br/> 복제된 모든 디스크의 경우 드라이브 문자는 동일하게 유지됩니다.<br/><br/> G: 디스크를 사용할 수 없기 때문에 시스템은 페이징 파일에 C: 드라이브를 사용 합니다.
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

Azure VM의 페이징 파일 설정은 다음과 같습니다.

![Azure Virtual Machine의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>다음 단계

- 임시 저장소 디스크에 대 한 지침에 대해 자세히 알아보세요.
    - Azure Vm에서 Ssd를 사용 하 여 SQL Server TempDB 및 버퍼 풀 확장을 저장 하는 방법 [에 대해 알아봅니다](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) .
    - Azure Vm의 SQL Server에 대 한 성능 모범 사례를 [검토](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) 합니다.
- 배포가 설정되고 실행된 후에는 다양한 장애 조치(Failover)에 대해 [자세히 알아보세요](failover-failback-overview.md).

