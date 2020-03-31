---
title: Azure 사이트 복구를 사용 하 고 복제에서 디스크 제외
description: Azure 사이트 복구를 사용하여 복제에서 Azure로 디스크를 제외하는 방법
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 57bf06f0fde85714530c06cbd008db08de7460d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281848"
---
# <a name="exclude-disks-from-disaster-recovery"></a>재해 복구에서 디스크 제외

이 문서에서는 Azure [사이트](site-recovery-overview.md)복구를 사용하여 온-프레미스에서 Azure로 재해 복구 중에 디스크를 복제에서 제외하는 방법을 설명합니다. 다음과 같은 여러 가지 이유로 디스크를 복제에서 제외할 수 있습니다.

- 제외된 디스크에서 변동된 중요하지 않은 데이터가 복제되지 않도록 합니다.
- 소비된 복제 대역폭 또는 대상 측 리소스를 최적화합니다.
- 필요하지 않은 데이터를 복제하지 않음으로써 저장소 및 네트워크 리소스를 저장합니다.
- Azure VM이 사이트 복구 복제 제한에 도달했습니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

테이블에 요약된 대로 복제에서 디스크를 제외할 수 있습니다.

**Azure 간** | **VMware에서 Azure로** | **Hyper-V에서 Azure로** 
--- | --- | ---
예(파워쉘 사용) | yes | yes 

## <a name="exclude-limitations"></a>제한 제외

**제한 사항** | **Azure VM** | **VMware VM** | **Hyper-V VM**
--- | --- | ---
**디스크 유형** | 기본 디스크를 복제에서 제외할 수 있습니다.<br/><br/> 운영 체제 디스크 또는 동적 디스크는 제외할 수 없습니다. 임시 디스크는 기본적으로 제외됩니다. | 기본 디스크를 복제에서 제외할 수 있습니다.<br/><br/> 운영 체제 디스크 또는 동적 디스크는 제외할 수 없습니다. | 기본 디스크를 복제에서 제외할 수 있습니다.<br/><br/> 운영 체제 디스크를 제외할 수 없습니다. 동적 디스크는 제외하지 않는 것이 좋습니다. 사이트 복구는 게스트 VM에서 기본 또는 동적 VHS를 식별할 수 없습니다. 종속 동적 볼륨 디스크가 모두 제외되지 않으면 보호된 동적 디스크가 장애 조치 VM에서 실패한 디스크가 되고 해당 디스크의 데이터에 액세스할 수 없습니다.
**디스크 복제** | 복제하는 디스크는 제외할 수 없습니다.<br/><br/> VM에 대한 복제를 비활성화하고 다시 사용할 수 있습니다. |  복제하는 디스크는 제외할 수 없습니다. |  복제하는 디스크는 제외할 수 없습니다.
**모빌리티 서비스(VMware)** | 관련 없음 | Mobility 서비스가 설치된 VM에서만 디스크를 제외할 수 있습니다.<br/><br/> 즉, 디스크를 제외하려는 VM에 Mobility 서비스를 수동으로 설치해야 합니다. 푸시 설치 메커니즘은 복제가 활성화된 후에만 Mobility 서비스를 설치하므로 사용할 수 없습니다. | 관련이 없습니다.
**추가/제거** | 관리 디스크가 있는 Azure VM에서 디스크를 추가하고 제거할 수 있습니다. | 복제를 사용하도록 설정한 후에는 디스크를 추가하거나 제거할 수 없습니다. 디스크를 추가하려면 복제를 비활성화한 다음 다시 사용할 수 있습니다. | 복제를 사용하도록 설정한 후에는 디스크를 추가하거나 제거할 수 없습니다. 복제를 비활성화한 다음 다시 사용할 수 있습니다.
**장애 조치** | 앱에 제외한 디스크가 필요한 경우 장애 조치 후 복제된 앱을 실행할 수 있도록 디스크를 수동으로 만들어야 합니다.<br/><br/> 또는 Azure 자동화를 복구 계획에 통합하여 VM 장애 조치 중에 디스크를 만들 수 있습니다. | 앱에 필요한 디스크를 제외하는 경우 장애 조치 후 Azure에서 수동으로 만듭니다. | 앱에 필요한 디스크를 제외하는 경우 장애 조치 후 Azure에서 수동으로 만듭니다.
**수동으로 만든 온-프레미스 장애 복구 디스크** | 관련 없음 | **Windows VM**: Azure에서 수동으로 만든 디스크는 다시 실패하지 않습니다. 예를 들어 세 개의 디스크를 장애 조치하고 Azure VM에서 직접 두 개의 디스크를 만드는 경우 실패한 세 개의 디스크만 다시 장애복구됩니다.<br/><br/> **Linux VM**: Azure에서 수동으로 생성된 디스크가 다시 실패합니다. 예를 들어 세 개의 디스크를 장애 조치하고 Azure VM에서 두 개의 디스크를 만들면 5개 모두 다시 실패하게 됩니다. 장애 복구에서 수동으로 만든 디스크는 제외할 수 없습니다. | Azure에서 수동으로 만든 디스크는 다시 실패하지 않습니다. 예를 들어 세 개의 디스크를 장애 조치하고 Azure VM에서 직접 두 개의 디스크를 만드는 경우 장애조치된 디스크 3개만 다시 장애조치됩니다.
**온-프레미스 장애 복구 제외 디스크** | 관련 없음 | 원래 컴퓨터로 장애 복구하면 장애 조치 VM 디스크 구성에 제외된 디스크가 포함되지 않습니다. VMware에서 Azure 복제로 제외된 디스크는 장애 복구 VM에서 사용할 수 없습니다. | 장애 조치가 원래 Hyper-V 위치에 있는 경우 장애 복구 VM 디스크 구성은 원래 원본 VM 디스크구성과 동일하게 유지됩니다. 하이퍼 V 사이트에서 Azure 복제로 제외된 디스크는 장애 복구 VM에서 사용할 수 있습니다.



## <a name="typical-scenarios"></a>일반적인 시나리오

제외 할 수있는 훌륭한 후보인 데이터 변동의 예로는 페이징 파일 (pagefile.sys)에 쓰기및 Microsoft SQL Server의 tempdb 파일에 기록합니다. 워크로드 및 저장소 하위 시스템에 따라 페이징 및 tempdb 파일은 상당한 양의 변동을 등록할 수 있습니다. 이러한 유형의 데이터를 Azure에 복제하는 것은 리소스를 많이 사용합니다.

- 운영 체제와 페이징 파일이 모두 포함된 단일 가상 디스크를 사용하여 VM에 대한 복제를 최적화하려면 다음을 수행할 수 있습니다.
    1. 단일 가상 디스크를 두 개의 가상 디스크로 분할합니다. 한 가상 디스크에는 운영 체제가 있고 다른 하나에는 페이징 파일이 있습니다.
    2. 복제에서 페이징 파일 디스크를 제외합니다.

- Microsoft SQL Server tempdb 파일과 시스템 데이터베이스 파일이 모두 포함된 디스크에 대한 복제를 최적화하려면 다음을 수행할 수 있습니다.
    1. 별도의 두 디스크에 tempdb와 시스템 데이터베이스를 유지합니다.
    2. 복제에서 tempdb 디스크를 제외합니다.

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>예제 1: SQL Server tempdb 디스크 제외

템플릿DB를 제외하려는 원본 SQL Server Windows VM - **SalesDB***에 대한 디스크 제외, 장애 조치 및 장애 조치(failover)를 처리하는 방법을 살펴보겠습니다. 

### <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

우리는 소스 윈도우 VM SalesDB에 이러한 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | 디스크 0 | C:\ | 운영 체제 디스크.
DB-Disk1| Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스1.
DB-Disk2(보호에서 디스크 제외됨) | Disk2 | E:\ | 임시 파일.
DB-Disk3(보호에서 디스크 제외됨) | Disk3 | F:\ | SQL tempdb 데이터베이스.<br/><br/> 폴더 경로 - F:\MSSQL\데이터\. 장애 조치(failover) 전에 폴더 경로를 기록해 둡을 기록합니다.
DB-Disk4 | Disk4 |G:\ | 사용자 데이터베이스 2

1. SalesDB VM에 대한 복제를 사용하도록 설정합니다.
2. 해당 디스크의 데이터 변동이 일시적이기 때문에 Disk2 및 Disk3를 복제에서 제외합니다. 


### <a name="handle-disks-during-failover"></a>장애 조치 중 디스크 처리

디스크가 복제되지 않으므로 Azure에 장애 조치(failover)하면 장애 조치 후 생성된 Azure VM에 이러한 디스크가 존재하지 않습니다. Azure VM에는 이 테이블에 요약된 디스크가 있습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
디스크 0 | C:\ | 운영 체제 디스크.
Disk1 | E:\ | 임시 저장소<br/><br/>Azure에서 이 디스크를 추가합니다. Disk2 및 Disk3가 복제에서 제외되었기 때문에 E: 사용 가능한 목록에서 첫 번째 드라이브 문자입니다. Azure에서 E:를 임시 스토리지 볼륨에 할당합니다. 복제된 디스크의 다른 드라이브 문자는 동일하게 유지됩니다.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1
Disk3 | G:\ | 사용자 데이터베이스 2

이 예제에서는 Disk3 SQL tempdb 디스크가 복제에서 제외되어 Azure VM에서 사용할 수 없으므로 SQL 서비스가 중지된 상태이며 F:\MSSQL\Data 경로가 필요합니다. 다음 몇 가지 방법으로 이 경로를 만들 수 있습니다. 

- 장애 조치 후 새 디스크를 추가하고 tempdb 폴더 경로를 할당합니다.
- tempdb 폴더 경로에 기존 임시 스토리지 디스크를 사용합니다.

#### <a name="add-a-new-disk-after-failover"></a>장애 조치 후 새 디스크 추가

1. 장애 조치(failover) 전에 SQL tempdb.mdf 및 tempdb.ldf 경로를 적어둡니다.
2. Azure 포털에서 장애 조치 Azure VM에 새 디스크를 추가합니다. 디스크는 원본 SQL tempdb 디스크(Disk3)와 크기가 같거나 커야 합니다.
3. Azure VM에 로그인합니다.
4. 디스크 관리(diskmgmt.msc) 콘솔에서 새로 추가한 디스크를 초기화하고 포맷합니다.
5. SQL tempdb 디스크에서 사용한 것과 동일한 드라이브 문자를 할당합니다(F:)
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



### <a name="vmware-vms-disks-during-failback-to-original-location"></a>VMware VM: 원래 위치로 장애 조치 중 디스크

이제 원래 온-프레미스 위치로 장애 조치할 때 VMware VM에서 디스크를 처리하는 방법을 살펴보겠습니다.

- **Azure에서 만든 디스크**: 이 예제에서는 Windows VM을 사용하기 때문에 장애 조치하거나 VM을 다시 보호할 때 Azure에서 수동으로 만든 디스크가 사이트로 다시 복제되지 않습니다.
- **Azure의 임시 저장소 디스크**: 임시 저장소 디스크가 온-프레미스 호스트로 다시 복제되지 않습니다.
- **제외된 디스크**: 장애 조치 후 온-프레미스 VM에서 VMware에서 Azure 복제로 제외된 디스크를 사용할 수 없습니다.

VMware VM을 원래 위치로 되돌리기 전에 Azure VM 디스크 설정은 다음과 같습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
디스크 0 | C:\ | 운영 체제 디스크.
Disk1 | E:\ | 임시 저장소.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스1.
Disk3 | G:\ | 사용자 데이터베이스2.

장애 조치 후 원래 위치에 있는 VMware VM에는 테이블에 요약된 디스크가 있습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
디스크 0 | C:\ | 운영 체제 디스크.
Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스1.
Disk2 | G:\ | 사용자 데이터베이스2.


### <a name="hyper-v-vms-disks-during-failback-to-original-location"></a>하이퍼 VM: 원래 위치로 장애 조치 중 디스크

이제 원래 온-프레미스 위치로 장애 조치할 때 Hyper-V VM에서 디스크를 처리하는 방법을 살펴보겠습니다.

- **Azure에서 만든 디스크**: Azure에서 수동으로 만든 디스크는 VM을 다시 장애 조치하거나 다시 보호할 때 사이트에 다시 복제되지 않습니다.
- **Azure의 임시 저장소 디스크**: 임시 저장소 디스크가 온-프레미스 호스트로 다시 복제되지 않습니다.
- **제외된 디스크**: 장애 조치 후 VM 디스크 구성은 원래 VM 디스크 구성과 동일합니다. 하이퍼-V에서 Azure로의 복제에서 제외된 디스크는 장애 복구 VM에서 사용할 수 있습니다.

Hyper-V VM을 원래 위치로 되돌리기 전에 Azure VM 디스크 설정은 다음과 같습니다.

**게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
디스크 0 | C:\ | 운영 체제 디스크.
Disk1 | E:\ | 임시 저장소.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스1.
Disk3 | G:\ | 사용자 데이터베이스2.

Azure에서 온-프레미스 하이퍼-V로 계획된 장애 조치(failback) 후 원래 위치의 Hyper-V VM에는 테이블에 요약된 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크 #** | **드라이브 문자** | **디스크 데이터 형식**
 --- | --- | --- | ---
DB-Disk0-OS | 디스크 0 |   C:\ | 운영 체제 디스크.
DB-Disk1 | Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스1.
DB-Disk2(제외된 디스크) | Disk2 | E:\ | 임시 파일.
DB-Disk3(제외된 디스크) | Disk3 | F:\ | SQL 템플릿 데이터베이스<br/><br/> 폴더 경로(F:\MSSQL\데이터\).
DB-Disk4 | Disk4 | G:\ | 사용자 데이터베이스 2


## <a name="example-2-exclude-the-paging-file-disk"></a>예 2: 페이징 파일 디스크 제외

D 드라이브와 대체 드라이브 모두에서 pagefile.sys 파일 디스크를 제외하려는 원본 Windows VM에 대한 디스크 제외, 장애 조치 및 장애 조치(failover)를 처리하는 방법을 살펴보겠습니다.


### <a name="paging-file-on-the-d-drive"></a>D 드라이브의 페이징 파일

이러한 디스크는 소스 VM에 있습니다.

**디스크 이름** | **게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | 디스크 0 | C:\ | 운영 체제 디스크
DB-Disk1(복제에서 제외) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

소스 VM의 페이징 파일 설정은 다음과 같습니다.

![원본 가상 컴퓨터의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-d-drive-source-vm.png)

1. VM에 대한 복제를 사용하도록 설정합니다.
2. DB-Disk1은 복제에서 제외합니다.

#### <a name="disks-after-failover"></a>장애 조치 후 디스크

장애 조치 후 Azure VM에는 테이블에 요약된 디스크가 있습니다.

**디스크 이름** | **게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | 디스크 0 | C:\ | 운영 체제 디스크
DB-Disk1 | Disk1 | D:\ | 임시 저장/페이지 파일.sys <br/><br/> DB-디스크1(D:) D: 사용 가능한 목록에서 첫 번째 드라이브 문자입니다.<br/><br/> Azure에서 D:를 임시 스토리지 볼륨에 할당합니다.<br/><br/> D: 사용 가능하므로 VM 페이징 파일 설정은 동일하게 유지됩니다.
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

Azure VM의 페이징 파일 설정은 다음과 같습니다.

![Azure Virtual Machine의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-azure-vm-after-failover.png)

### <a name="paging-file-on-another-drive-not-d"></a>다른 드라이브의 페이징 파일(D:아님)

페이징 파일이 D 드라이브에 없는 예제를 살펴보겠습니다.  

이러한 디스크는 소스 VM에 있습니다.

**디스크 이름** | **게스트 OS 디스크** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | 디스크 0 | C:\ | 운영 체제 디스크
DB-Disk1(복제에서 제외) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

온-프레미스 VM의 페이징 파일 설정은 다음과 같습니다.

![온-프레미스 가상 머신의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-g-drive-source-vm.png)

1. VM에 대한 복제를 사용하도록 설정합니다.
2. DB-Disk1은 복제에서 제외합니다.

#### <a name="disks-after-failover"></a>장애 조치 후 디스크

장애 조치 후 Azure VM에는 테이블에 요약된 디스크가 있습니다.

**디스크 이름** | **게스트 OS 디스크 #** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | 디스크 0  |C:\ | 운영 체제 디스크
DB-Disk1 | Disk1 | D:\ | 임시 저장소<br/><br/> D:가 목록에서 사용 가능한 첫 번째 드라이브 문자이므로 Azure에서 D:를 임시 스토리지 볼륨에 할당합니다.<br/><br/> 복제된 모든 디스크의 경우 드라이브 문자는 동일하게 유지됩니다.<br/><br/> G: 디스크를 사용할 수 없기 때문에 시스템에서 는 페이징 파일에 대한 C: 드라이브를 사용합니다.
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

Azure VM의 페이징 파일 설정은 다음과 같습니다.

![Azure Virtual Machine의 페이징 파일 설정](./media/exclude-disks-replication/pagefile-azure-vm-after-failover-2.png)


## <a name="next-steps"></a>다음 단계

- 임시 저장소 디스크에 대한 지침에 대해 자세히 알아보십시오.
    - Azure VM에서 SSD를 사용하여 SQL 서버 TempDB 및 버퍼 풀 확장을 저장하는 방법에 대해 [알아보기](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
    - Azure VM의 SQL Server성능 모범 사례를 [검토합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)
- 배포가 설정되고 실행된 후에는 다양한 장애 조치(Failover)에 대해 [자세히 알아보세요](failover-failback-overview.md).

