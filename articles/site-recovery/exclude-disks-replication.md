---
title: Azure Site Recovery를 사용하여 복제에서 디스크 제외
description: Azure Site Recovery를 사용하여 Azure로 복제할 때 디스크를 제외하는 방법.
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 778bb030d9768c5fbe1cb8aeba0becfc68c00629
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245401"
---
# <a name="exclude-disks-from-disaster-recovery"></a>재해 복구에서 디스크 제외

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스에서 Azure로 재해 복구를 수행하는 동안 복제에서 디스크를 제외하는 방법을 설명합니다. 다음과 같은 여러 이유로 복제에서 디스크를 제외할 수 있습니다.

- 제외되는 디스크에서 변동된 중요하지 않은 데이터가 복제되지 않도록 합니다.
- 소비되는 복제 대역폭 또는 대상 쪽 리소스를 최적화합니다.
- 불필요한 데이터를 복제하지 않음으로써 스토리지와 네트워크 리소스를 절약합니다.
- Azure VM이 Site Recovery 복제 한도에 도달했습니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

표에 요약된 대로 복제에서 디스크를 제외할 수 있습니다.

**Azure 간** | **VMware에서 Azure로** | **Hyper-V에서 Azure로** | **물리적 서버에서 Azure로**
--- | --- | --- | ---
예 | 예 | 예 | 예

## <a name="exclude-limitations"></a>제외 제한 사항

**제한 사항** | **Azure VM** | **VMware VM** | **Hyper-V VM**
--- | --- | ---
**디스크 유형** | 기본 디스크를 복제에서 제외할 수 있습니다.<br/><br/> 운영 체제 디스크 또는 동적 디스크를 제외할 수 없습니다. 임시 디스크는 기본적으로 제외됩니다. | 기본 디스크를 복제에서 제외할 수 있습니다.<br/><br/> 운영 체제 디스크 또는 동적 디스크를 제외할 수 없습니다. | 기본 디스크를 복제에서 제외할 수 있습니다.<br/><br/> 운영 체제 디스크를 제외할 수 없습니다. 동적 디스크는 제외하지 않는 것이 좋습니다. Site Recovery는 게스트 VM 내의 VHS 디스크가 기본 디스크인지 동적 디스크인지 여부를 확인할 수 없습니다. 모든 종속적인 동적 볼륨 디스크가 제외되지 않으면, 보호된 동적 디스크가 장애 조치 VM에서 실패한 디스크로 나타나며 해당 디스크의 데이터에 액세스할 수 없습니다.
**복제 중인 디스크** | 복제 중인 디스크는 제외할 수 없습니다.<br/><br/> VM에 대해 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정합니다. |  복제 중인 디스크는 제외할 수 없습니다. |  복제 중인 디스크는 제외할 수 없습니다.
**Mobility Service(VMware)** | 해당 없음 | Mobility Service가 설치된 VM에서만 디스크를 제외할 수 있습니다.<br/><br/> 즉, 디스크를 제외하려는 VM에 Mobility Service를 수동으로 설치해야 합니다. 푸시 설치 메커니즘은 복제가 사용 설정된 후에만 Mobility Service를 설치하므로 사용할 수 없습니다. | 해당 없음.
**추가/제거** | 관리 디스크를 사용하여 복제가 사용 설정된 Azure VM에 관리 디스크를 추가할 수 있습니다. 복제가 사용 설정된 Azure VM에서 디스크를 제거할 수 없습니다. | 복제가 사용 설정된 후 디스크를 추가 또는 제거할 수 없습니다. 디스크를 추가하려면 복제를 사용 중지한 후 다시 사용 설정합니다. | 복제가 사용 설정된 후 디스크를 추가 또는 제거할 수 없습니다. 복제를 사용 중지한 후 다시 사용 설정합니다.
**장애 조치(Failover)** | 앱에 제외한 디스크가 필요한 경우 장애 조치(failover) 후 수동으로 디스크를 만들어서 복제된 앱을 실행할 수 있도록 해야 합니다.<br/><br/> 또는 Azure Automation을 복구 계획에 통합하여 VM 장애 조치(failover) 중에 디스크를 만들 수 있습니다. | 앱에 필요한 디스크를 제외하는 경우 장애 조치(failover) 후 Azure에서 수동으로 만듭니다. | 앱에 필요한 디스크를 제외하는 경우 장애 조치(failover) 후 Azure에서 수동으로 만듭니다.
**수동으로 만들어진 디스크 온-프레미스 장애 복구** | 해당 없음 | **Windows VM**: Azure에서 수동으로 만들어진 디스크는 장애 복구되지 않습니다. 예를 들어 디스크 3개를 장애 조치(failover)하고 Azure VM에서 디스크 두 개를 직접 만드는 경우 장애 조치(failover)된 3개 디스크만 장애 복구됩니다.<br/><br/> **Linux VM**: Azure에서 수동으로 만들어진 디스크는 장애 복구됩니다. 예를 들어 디스크 3개를 장애 조치(failover)하고 Azure VM에서 디스크 두 개를 만드는 경우 5개 디스크 모두 장애 복구됩니다. 장애 복구에서 수동으로 만든 디스크는 제외할 수 없습니다. | Azure에서 수동으로 만들어진 디스크는 장애 복구되지 않습니다. 예를 들어 디스크 3개를 장애 조치(failover)하고 Azure VM에서 디스크 두 개를 직접 만드는 경우 장애 조치(failover)된 3개 디스크만 장애 복구됩니다.
**제외된 디스크 온-프레미스 장애 복구** | 해당 없음 | 원래 머신으로 장애 복구하는 경우 제외된 디스크는 장애 복구 VM 디스크 구성에 포함되지 않습니다. VMware에서 Azure로의 복제에서 제외된 디스크는 장애 복구 VM에서 사용할 수 없습니다. | 원본 Hyper-V 위치로 장애 복구하는 경우 장애 복구 VM 디스크 구성은 원본 소스 VM 디스크와 동일하게 유지됩니다. Hyper-V 사이트에서 Azure로의 복제에서 제외된 디스크는 장애 복구 VM에서 사용할 수 있습니다.



## <a name="typical-scenarios"></a>일반적인 시나리오

제외에 적합한 데이터 변동의 예를 들면 페이징 파일(pagefile.sys)에 대한 쓰기 및 Microsoft SQL Server의 tempdb 파일에 대한 쓰기가 있습니다. 워크로드 및 스토리지 하위 시스템에 따라 페이징 및 tempdb 파일이 상당한 양의 변동을 등록할 수 있습니다. 이러한 유형의 데이터를 Azure에 복제 하는 경우 리소스를 많이 사용하게 됩니다.

- 운영 체제와 페이징 파일을 모두 포함하는 단일 가상 디스크를 사용하는 VM에 대한 복제를 최적화하려면 다음 작업을 수행할 수 있습니다.
    1. 단일 가상 디스크를 두 개의 가상 디스크로 분할합니다. 한 가상 디스크에는 운영 체제가 있고 다른 하나에는 페이징 파일이 있습니다.
    2. 복제에서 페이징 파일 디스크를 제외합니다.

- Microsoft SQL Server tempdb 파일과 시스템 데이터베이스 파일을 모두 포함하는 디스크에 대한 복제를 최적화하려면 다음 작업을 수행할 수 있습니다.
    1. 별도의 두 디스크에 tempdb와 시스템 데이터베이스를 유지합니다.
    2. 복제에서 tempdb 디스크를 제외합니다.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>예제 1: SQL Server tempdb 디스크 제외

tempdb를 제외하려는 원본 SQL Server Windows VM(**SalesDB***)에 대한 디스크 제외, 장애 조치(failover) 및 장애 복구를 처리하는 방법을 살펴보겠습니다. 

### <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

원본 Windows VM SalesDB에 다음과 같은 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 운영 체제 디스크.
DB-Disk1| Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1.
DB-Disk2(보호에서 디스크 제외됨) | Disk2 | E:\ | 임시 파일.
DB-Disk3(보호에서 디스크 제외됨) | Disk3 | F:\ | SQL tempdb 데이터베이스.<br/><br/> 폴더 경로 - F:\MSSQL\Data\. 장애 조치(failover) 전에 폴더 경로를 기록해 둡니다.
DB-Disk4 | Disk4 |G:\ | 사용자 데이터베이스 2

1. SalesDB VM에 대해 복제를 사용 설정합니다.
2. Disk2와 Disk3의 데이터 변동은 임시적이므로 두 디스크를 복제에서 제외합니다. 


### <a name="handle-disks-during-failover"></a>장애 조치(failover) 중 디스크 처리

디스크가 복제되지 않으므로 Azure로 장애 조치(failover)하는 경우 장애 조치(failover) 후 생성된 Azure VM에는 이러한 디스크가 표시되지 않습니다. Azure VM에는 이 표에 요약된 디스크가 있습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
Disk0 | C:\ | 운영 체제 디스크.
Disk1 | E:\ | 임시 스토리지<br/><br/>Azure에서 이 디스크를 추가합니다. Disk2와 Disk3은 복제에서 제외되었으므로 E:가 사용 가능한 목록의 첫 번째 드라이브 문자입니다. Azure에서 E:를 임시 스토리지 볼륨에 할당합니다. 복제된 디스크의 다른 드라이브 문자는 동일하게 유지됩니다.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1
Disk3 | G:\ | 사용자 데이터베이스 2

이 예에서 SQL tempdb 디스크인 Disk3은 복제에서 제외되었고 Azure VM에서 사용할 수 없으므로 SQL 서비스가 중지된 상태이며 F:\MSSQL\Data 경로가 필요합니다. 이 경로는 두 가지 방법으로 만들 수 있습니다. 

- 장애 조치(failover) 후 새 디스크를 추가하고 tempdb 폴더 경로를 할당합니다.
- tempdb 폴더 경로에 기존 임시 스토리지 디스크를 사용합니다.

#### <a name="add-a-new-disk-after-failover"></a>장애 조치(failover) 후 새 디스크 추가

1. 장애 조치(failover) 전에 SQL tempdb.mdf 및 tempdb.ldf 경로를 적어둡니다.
2. Azure Portal에서 장애 조치(failover) Azure VM에 새 디스크를 추가합니다. 디스크의 크기는 원본 SQL tempdb 디스크(Disk3)와 같거나 커야 합니다.
3. Azure VM에 로그인합니다.
4. 디스크 관리(diskmgmt.msc) 콘솔에서 새로 추가한 디스크를 초기화하고 포맷합니다.
5. SQL tempdb 디스크(F:)에서 사용한 것과 동일한 드라이브 문자를 할당합니다.
6. F: 볼륨(F:\MSSQL\Data)에 tempdb 폴더를 만듭니다.
7. 서비스 콘솔에서 SQL 서비스를 시작합니다.

#### <a name="use-an-existing-temporary-storage-disk"></a>기존 임시 스토리지 디스크 사용 

1. 명령 프롬프트를 엽니다.
2. 명령 프롬프트에서 SQL Server를 복구 모드에서 실행합니다.

    ```console
    Net start MSSQLSERVER /f / T3608
    ```

3. 다음 sqlcmd를 실행하여 tempdb 경로를 새 경로로 바꿉니다.

    ```sql
    sqlcmd -A -S SalesDB        **Use your SQL DBname**
    USE master;     
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
    GO      
    ALTER DATABASE tempdb       
    MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
    GO
    ```

4. Microsoft SQL Server 서비스를 중지합니다.

    ```console
    Net stop MSSQLSERVER
    ```

5. Microsoft SQL Server 서비스를 시작합니다.

    ```console
    Net start MSSQLSERVER
    ```

### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware VM: 원래 위치로 장애 복구 중 디스크

이제 원래 온-프레미스 위치로 장애 복구하는 경우 VMware VM의 디스크를 처리하는 방법을 알아보겠습니다.

- **Azure에서 생성된 디스크**: 이 예에서는 Windows VM을 사용하므로 Azure에서 수동으로 만드는 디스크는 VM을 장애 복구하거나 다시 보호할 때 사이트에 다시 복제되지 않습니다.
- **Azure의 임시 스토리지 디스크**: 임시 스토리지 디스크는 온-프레미스 호스트로 다시 복제되지 않습니다.
- **제외된 디스크**: VMware에서 Azure로의 복제에서 제외된 디스크는 장애 복구 후 온-프레미스 VM에서 사용할 수 없습니다.

VMware VM을 원래 위치로 장애 복구하기 전에 Azure VM 디스크 설정은 다음과 같습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
Disk0 | C:\ | 운영 체제 디스크.
Disk1 | E:\ | 임시 스토리지.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1.
Disk3 | G:\ | 사용자 데이터베이스 2.

장애 복구 후 원래 위치의 VMware VM에는 표에 요약된 디스크가 있습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
Disk0 | C:\ | 운영 체제 디스크.
Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1.
Disk2 | G:\ | 사용자 데이터베이스 2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>Hyper-V VM: 원래 위치로 장애 복구 중 디스크

이제 원래 온-프레미스 위치로 장애 복구하는 경우 Hyper-V VM의 디스크를 처리하는 방법을 알아보겠습니다.

- **Azure에서 생성된 디스크**: Azure에서 수동으로 만드는 디스크는 VM을 장애 복구하거나 다시 보호할 때 사이트에 다시 복제되지 않습니다.
- **Azure의 임시 스토리지 디스크**: 임시 스토리지 디스크는 온-프레미스 호스트로 다시 복제되지 않습니다.
- **제외된 디스크**: 장애 복구 후 VM 디스크 구성은 원본 VM 디스크 구성과 동일합니다. Hyper-V 사이트에서 Azure로의 복제에서 제외된 디스크는 장애 복구 VM에서 사용할 수 있습니다.

Hyper-V VM을 원래 위치로 장애 복구하기 전에 Azure VM 디스크 설정은 다음과 같습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
Disk0 | C:\ | 운영 체제 디스크.
Disk1 | E:\ | 임시 스토리지.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1.
Disk3 | G:\ | 사용자 데이터베이스 2.

Azure에서 온-프레미스 Hyper-v로 계획된 장애 조치(failover) 후 원래 위치의 Hyper-V VM에는 표에 요약된 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크 #** | **드라이브 문자** | **디스크 데이터 형식**
 --- | --- | --- | ---
DB-Disk0-OS | Disk0 |   C:\ | 운영 체제 디스크.
DB-Disk1 | Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1.
DB-Disk2(제외된 디스크) | Disk2 | E:\ | 임시 파일.
DB-Disk3(제외된 디스크) | Disk3 | F:\ | SQL tempdb 데이터베이스<br/><br/> 폴더 경로(F:\MSSQL\Data\).
DB-Disk4 | Disk4 | G:\ | 사용자 데이터베이스 2


## <a name="example-2-exclude-the-paging-file-disk"></a>예제 2: 페이징 파일 디스크 제외

원본 Windows VM에 대해 디스크 제외와 장애 조치(failover)를 처리하는 방법을 살펴보겠습니다. 여기서 D 드라이브와 대체 드라이브의 pagefile.sys 파일 디스크를 제외하려고 합니다.


### <a name="paging-file-on-the-d-drive"></a>D 드라이브의 페이징 파일

원본 VM에는 다음과 같은 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 운영 체제 디스크
DB-Disk1(복제에서 제외) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

원본 VM의 페이징 파일 설정은 다음과 같습니다.

![원본 가상 컴퓨터의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. VM에 대한 복제를 활성화합니다.
2. DB-Disk1을 복제에서 제외합니다.

#### <a name="disks-after-failover"></a>장애 조치(failover) 후 디스크

장애 조치(failover) 후 Azure VM에는 표에 요약된 디스크가 있습니다.

**디스크 이름** | **게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 운영 체제 디스크
DB-Disk1 | Disk1 | D:\ | 임시 스토리지/pagefile.sys <br/><br/> DB-Disk1(D:)은 제외되었으므로 D:가 사용 가능한 목록에서 첫 번째 드라이브 문자입니다.<br/><br/> Azure에서 D:를 임시 스토리지 볼륨에 할당합니다.<br/><br/> D:를 사용할 수 있으므로 VM 페이징 파일 설정은 동일하게 유지 됩니다.
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

Azure VM의 페이징 파일 설정은 다음과 같습니다.

![Azure Virtual Machine의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>D:가 아닌 다른 드라이브의 페이징 파일

페이징 파일이 D 드라이브에 없는 예를 살펴보겠습니다.  

원본 VM에는 다음과 같은 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0 | C:\ | 운영 체제 디스크
DB-Disk1(복제에서 제외) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

온-프레미스 VM의 페이징 파일 설정은 다음과 같습니다.

![온-프레미스 가상 머신의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. VM에 대한 복제를 활성화합니다.
2. DB-Disk1을 복제에서 제외합니다.

#### <a name="disks-after-failover"></a>장애 조치(failover) 후 디스크

장애 조치(failover) 후 Azure VM에는 표에 요약된 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크 #** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | Disk0  |C:\ | 운영 체제 디스크
DB-Disk1 | Disk1 | D:\ | 임시 스토리지<br/><br/> D:가 목록에서 사용 가능한 첫 번째 드라이브 문자이므로 Azure에서 D:를 임시 스토리지 볼륨에 할당합니다.<br/><br/> 복제된 모든 디스크의 경우 드라이브 문자는 동일하게 유지됩니다.<br/><br/> G: 디스크를 사용할 수 없으므로 시스템에서 C: 드라이브를 페이징 파일에 사용합니다.
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

Azure VM의 페이징 파일 설정은 다음과 같습니다.

![Azure Virtual Machine의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>다음 단계

- 임시 스토리지 디스크에 대한 지침을 자세히 알아보세요.
    - Azure VM에서 SSD를 사용하여 SQL Server TempDB 및 버퍼 풀 확장을 저장하기는 방법에 대해 [알아보세요](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).
    - Azure VM의 SQL Server에 대한 성능 모범 사례를 [검토하세요](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).
- 배포가 설정되고 실행된 후에는 다양한 장애 조치(Failover)에 대해 [자세히 알아보세요](failover-failback-overview.md).
