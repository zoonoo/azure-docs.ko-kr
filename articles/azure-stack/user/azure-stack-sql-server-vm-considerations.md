---
title: SQL Server에 대 한 유용한 정보를 사용 하 고 Azure Stack virtual machines에서 성능을 향상 시키기 위해 | Microsoft Docs
description: 이 문서에서는 성능을 향상 시키고 Azure Stack Vm의 SQL Server를 최적화 하는 데 SQL server 모범 사례를 제공 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 03a354a7d670033fa86ebbb094710a836b6219c4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879067"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack"></a>Azure Stack에서 성능을 최적화 하기 위해 SQL server 모범 사례

이 문서에서는 SQL Server를 최적화 하 고 Microsoft Azure Stack virtual machines에서 성능을 향상 하려면 SQL server 모범 사례를 제공 합니다. Azure Stack virtual machines에서 SQL Server를 실행 하는 경우 동일한 데이터베이스 성능 튜닝 옵션 온-프레미스 서버 환경에서 SQL Server에 적용을 사용 합니다. Azure Stack 클라우드에 관계형 데이터베이스 성능은 여러 요인에 따라 달라 집니다. 가상 머신의 크기 패밀리 및 데이터 디스크의 구성 요소에 포함 됩니다.

SQL Server 이미지를 만들 때 [Azure Stack 포털에 가상 컴퓨터를 프로 비전 하는 것이 좋습니다.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)합니다. Azure Stack 관리 포털에서 관리 Marketplace에서에서 SQL IaaS 확장을 다운로드 하 고 여러분이 SQL 가상 머신 가상 하드 드라이브 (Vhd)를 다운로드 합니다. 여기에 SQL2014SP2, SQL2016SP1, SQL2017 포함 됩니다.

> [!NOTE]  
> 문서에서 설명 하는 전역 Azure portal을 사용 하 여 SQL Server 가상 컴퓨터를 프로 비전 하는 동안 제공 된 지침에도 적용 됩니다 Azure Stack 다음과 같은 차이점을 사용 하 여: SSD는 운영 체제 디스크를 사용할 수 없습니다 하 고 관리 디스크를 사용할 수 없는 저장소 구성에서 사소한 차이가 있습니다.

시작 합니다 *최상의* Azure Stack virtual machines에서 SQL Server에 대 한 성능에는이 문서에서는의 초점 이기도 합니다. 워크 로드가 덜 까다롭기 경우 권장 되는 모든 최적화가 필요 하지 않을 수도 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항 및 작업 패턴을 고려하세요.

> [!NOTE]  
> Azure virtual machines의 SQL Server에 대 한 성능 지침을 참조 하세요 [이 문서에서는](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)합니다.

## <a name="checklist-for-sql-server-best-practices"></a>SQL server 모범 사례에 대 한 검사 목록

Azure Stack virtual machines에서 SQL Server 성능을 최적화 하기 위한 다음 검사 목록은:


|영역|최적화|
|-----|-----|
|가상 머신 크기 |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) SQL Server Enterprise edition 이상.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) Web edition 및 SQL Server Standard edition 이상.|
|Storage |지원 되는 가상 컴퓨터 집합을 사용 하 여 [Premium storage](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences)합니다.|
|디스크 |최소 (로그 파일용 1) 및 데이터 파일 및 TempDB에 대해 하나씩 두 개의 데이터 디스크를 사용 하 고 용량 요구 사항에 따라 디스크 크기를 선택 합니다. 기본 데이터 파일 위치를 설정할 이러한 디스크 SQL Server 설치 중입니다.<br><br>데이터베이스 저장소나 로깅을 위해 운영 체제 또는 임시 디스크를 사용하지 않습니다.<br>가져올 저장소 공간을 사용 하 여 IO 처리량이 증가 하는 여러 Azure 데이터 디스크를 스트라이프 합니다.<br><br>문서화된 할당 크기로 포맷합니다.|
|I/O|데이터 파일에 대해 즉시 파일 초기화를 사용하도록 설정합니다.<br><br>적당히 작게 고정 된 증가분 (64MB-256 MB)를 사용 하 여 데이터베이스에서 자동 증가 제한 합니다.<br><br>데이터베이스에서 자동 축소를 사용하지 않도록 설정합니다.<br><br>데이터 디스크에서는 운영 체제 디스크가 아닌 기본 백업 및 데이터베이스 파일 위치를 설정 합니다.<br><br>잠긴 페이지를 사용하도록 설정합니다.<br><br>SQL Server 서비스 팩 및 누적 업데이트를 적용 합니다.|
|기능별|(사용 중인 SQL Server 버전에서 지 원하는) 경우 blob 저장소에 직접 백업 합니다.|
|||

에 대 한 자세한 *어떻게* 하 고 *이유* 이러한 최적화를 수행 하세요. 검토 세부 정보 및 다음 섹션에서 제공 된 지침.

## <a name="virtual-machine-size-guidance"></a>Virtual machine 크기 지침

다음 성능에 민감한 응용 프로그램에 대 한 [가상 머신 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 는 것이 좋습니다.

- **SQL Server Enterprise edition:** DS3 이상

- **SQL Server Standard edition 및 Web edition.** DS2 이상

Azure Stack DS 및 DS_v2 가상 머신 제품군 시리즈 간에 성능 차이점이 없음이 있습니다.

## <a name="storage-guidance"></a>저장소 지침

Azure Stack에서 DS 시리즈 (함께 DSv2 시리즈) virtual machines 최대 운영 체제 디스크 및 데이터 디스크 처리량 (IOPS)을 제공합니다. DS 또는 DSv2 시리즈에서 가상 머신 운영 체제 디스크 및 최대 2,300 IOPS 형식 또는 선택한 디스크의 크기에 관계 없이 데이터 디스크당 최대 1,000 개의 IOPS를 제공합니다.

데이터 디스크 처리량을 고유 하 게 가상 머신 제품군 시리즈에 따라 결정 됩니다. 할 수 있습니다 [이 문서를 참조](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 가상 머신 제품군 시리즈 당 데이터 디스크 처리량을 식별 합니다.

> [!NOTE]  
> 프로덕션 워크 로드에 대 한 디스크 및 데이터 디스크는 운영 체제에 가능한 최대 IOPS를 제공 하는 DSv2 시리즈 또는 DS 시리즈 가상 컴퓨터를 선택 합니다.

Azure Stack에서 저장소 계정을 만들 때 지역에서 복제 옵션은이 기능은 Azure Stack에서 사용할 수 없으므로 효과가 없습니다.

## <a name="disks-guidance"></a>디스크 지침

Azure Stack virtual machine에서 세 가지 기본 디스크 유형에

- **운영 체제 디스크:** 플랫폼 하나 이상의 디스크를 연결 하는 Azure Stack virtual machine을 만들면 (로 레이블이 지정 된 합니다 **C** 드라이브) 운영 체제 디스크에 대 한 가상 컴퓨터에 있습니다. 이 디스크는 저장소에 페이지 Blob으로 저장된 VHD입니다.

- **임시 디스크:** 임시 디스크 라는 다른 디스크를 포함 하는 azure Stack virtual machines (로 레이블이 지정 된 **D** 드라이브). 이는 스크래치 공간에 사용할 수 있는 노드의 디스크입니다.

- **데이터 디스크:** 데이터 디스크로 가상 컴퓨터에 추가 디스크를 연결할 수 있습니다 하 고 이러한 디스크는 저장소에 페이지 blob으로 저장 됩니다.

다음 섹션에서는 이러한 서로 다른 디스크를 사용하기 위한 권장 사항을 설명합니다.

### <a name="operating-system-disk"></a>운영 체제 디스크

운영 체제 디스크는 운영 체제의 실행 버전으로 부팅하고 탑재할 수 있는 VHD이며 **C** 드라이브로 레이블이 지정됩니다.

### <a name="temporary-disk"></a>임시 디스크

레이블이 지정 된 임시 저장소 드라이브는 **D** 드라이브, 지속 되지 않습니다. 손실, 사용자 데이터베이스 파일 또는 사용자 트랜잭션 로그 파일을 같은 원하지 않는 데이터를 저장 하지 마십시오 합니다 **D** 드라이브입니다.

각 데이터 디스크는 최대 데이터 디스크의 최대 2,300 IOPS 제공 합니다. 데이터 디스크에 TempDB를 저장 하는 것이 좋습니다.

### <a name="data-disks"></a>데이터 디스크

- **데이터 및 로그 파일에 데이터 디스크를 사용 합니다.** 디스크 스트라이프를 사용 하지 않는 경우 하나의 디스크에서 로그 파일을 포함 하는 위치에 Premium storage를 지 원하는 가상 컴퓨터에서 두 개의 데이터 디스크를 사용 하 고 데이터 및 TempDB 파일. 에 설명 된 대로 각 데이터 디스크의 IOPS 및 대역폭 (MB/s) 가상 머신 패밀리에 따라 숫자를 제공 [Azure Stack에서 지원 되는 가상 머신 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)합니다. 디스크 스트라이프 기술을 예: 저장소 공간을 사용 하는 경우 모든 데이터 및 로그 파일 (TempDB 포함)는 동일한 드라이브에 배치 합니다. 이 구성 요소를 사용 하기에 파일 요구 사항에 관계 없이 특정 시점에 SQL Server에 대 한 사용 가능한 IOPS의 최대 수를 제공 합니다.

> [!NOTE]  
> 포털에서 SQL Server 가상 컴퓨터를 프로 비전 하는 경우 저장소 구성을 편집 옵션이 있습니다. Azure Stack 구성에 따라 하나 이상의 디스크를 구성합니다. 여러 디스크를 단일 저장소 풀으로 결합 됩니다. 이 구성에서는 데이터 및 로그 파일이 함께 배치됩니다.

- **디스크 스트라이프:** 더 많은 처리량에 대 한 추가 데이터 디스크를 추가할 수 있으며 디스크 스트라이프를 사용할 수 있습니다. 데이터 및 TempDB 파일 및 로그 파일에 대해 필요한 IOPS 및 대역폭 수를 분석 해야 하는 데이터 디스크 수를 결정 합니다. 가상 머신 시리즈 제품군을 기반으로 하며 가상 머신 크기를 기반으로 하지 데이터 디스크당 IOPS 한도 알 수 있습니다. 그러나 네트워크 대역폭 제한, 가상 머신 크기에 기반한 합니다. 테이블을 참조 하세요 [Azure Stack에서 가상 머신 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 자세한 세부 정보에 대 한 합니다. 다음 지침을 사용하세요.

  - Windows Server 2012 이상에서는 사용할 [저장소 공간](https://technet.microsoft.com/library/hh831739.aspx) 다음 지침을 사용 하 여:

    1. 온라인 트랜잭션 처리 (OLTP) 워크 로드 및 256KB (262,144 바이트) 데이터 웨어하우징 워크 로드에 대 한 파티션 잘못 맞춤으로 인해 성능 저하를 방지 하기에 대해 64KB (65,536 바이트)로 인터리빙 (스트라이프 크기)를 설정 합니다. 이는 PowerShell로 설정되어야 합니다.

    2. 열 수를 실제 디스크 수로 설정합니다. 8 개 이상의 디스크 (서버 관리자 UI 아님)를 구성 하는 경우 PowerShell을 사용 합니다.

       예를 들어, 다음 PowerShell 인터리빙 크기가 64KB 이며 2 열 수가 설정 된 새 저장소 풀을 만듭니다.

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- 예상되는 부하에 따라 저장소 풀에 연결되는 디스크 수를 결정합니다. 연결 된 데이터 디스크를 다른 가상 머신 크기의 다른 숫자를 허용 하는 것에 유의 합니다. 자세한 내용은 [Azure Stack에서 지원 되는 가상 머신 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)합니다.
- 지 원하는 데이터 디스크의 최대 수를 추가 하는 것이 좋습니다 데이터 디스크에 대 한 최대 가능한 IOPS를 얻으려면 하 [가상 머신 크기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) 하 고 디스크 스트라이프를 사용 합니다.
- **NTFS 할당 단위 크기:** 데이터 디스크를 포맷할 때 TempDB뿐만 아니라 데이터 및 로그 파일에 대해 64KB 할당 단위 크기를 사용하는 것이 좋습니다.
- **디스크 관리 사례:** 데이터 디스크를 제거 하는 경우 변경 하는 동안 SQL Server 서비스를 중지 합니다. 또한 변경 되지 않습니다 디스크에서 캐시 설정을 모든 성능 향상을 제공 하지 마십시오.

> [!WARNING]  
> 이러한 작업 중 SQL 서비스를 중지 하지 못했습니다 데이터베이스 손상이 될 수 있습니다.

## <a name="io-guidance"></a>I/O 지침

- 초기 파일 할당에 필요한 시간을 줄이기 위해 즉시 파일 초기화를 사용하도록 설정하는 것이 좋습니다. 즉시 파일 초기화를 이용 하려면 SQL Server (MSSQLSERVER) 서비스 계정을 사용 하 여 부여 **SE_MANAGE_VOLUME_NAME** 에 추가 합니다 **볼륨 유지 관리 태스크 수행** 보안 정책입니다. SQL Server 플랫폼 이미지를 Azure에 대 한를 사용 하는 경우 기본 서비스 계정 (**NT Service\MSSQLSERVER**) 추가 되지 않습니다 합니다 **볼륨 유지 관리 태스크 수행** 보안 정책입니다. 즉, 즉시 파일 초기화는 SQL Server Azure 플랫폼 이미지에서 사용하도록 설정되어 있지 않습니다. SQL Server 서비스 계정을 **볼륨 유지 관리 작업 수행** 보안 정책에 추가한 후 SQL Server 서비스를 다시 시작합니다. 이 기능을 사용하기 위한 보안 고려 사항이 있을 수 있습니다. 자세한 내용은 [데이터베이스 파일 초기화](https://msdn.microsoft.com/library/ms175935.aspx)를 참조하세요.
- **자동 증가** 예기치 않은 증가 대 한 대체 됩니다. 일상적으로 자동 증가를 사용하여 사용자 데이터 및 로그 증가를 관리하지 마세요. 자동 증가 사용 하는 경우 사전에 증가 시킵니다 사용 하 여 파일을 **크기** 전환 합니다.
- **자동 축소**를 사용하지 않도록 설정하여 성능에 부정적인 영향을 미칠 수 있는 불필요한 오버헤드를 방지합니다.
- 기본 백업 및 데이터베이스 파일 위치를 설정합니다. 이 문서의 권장 사항을 사용 하 고 서버 속성 창에서 변경 합니다. 지침은 [데이터 및 로그 파일의 기본 위치 보기 또는 변경(SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)을 참조하세요. 다음 스크린샷은 이러한 변경을 수행 하려면 위치를 보여 줍니다.

    > ![기본 위치 보기 또는 변경](./media/sql-server-vm-considerations/image1.png)

- 잠긴 페이지를 사용하도록 설정하여 IO 및 페이징 작업을 줄입니다. 자세한 내용은 [메모리 내 페이지 잠금 옵션 사용(Windows)](https://msdn.microsoft.com/library/ms190730.aspx)을 참조하세요.

- 백업을 비롯 한 Azure Stack의 입/출력을 전송 하는 경우 모든 데이터 파일을 압축 하는 것이 좋습니다.

## <a name="feature-specific-guidance"></a>기능별 지침

더 많은 고급 구성 기술을 사용하면 일부 배포에서 추가적인 성능 이점을 얻을 수 있습니다. 다음은 성능을 향상 시키기 위해 도움이 될 수 있는 몇 가지 SQL Server 기능을 강조 표시 합니다.

- **Azure에 백업** **저장소입니다.** Azure Stack virtual machines에서 실행 중인 SQL server 백업 수행을 하는 경우 SQL Server Backup to URL 사용할 수 있습니다. 이 기능은 SQL Server 2012 SP1 CU2부터 사용할 수 있으며 연결된 데이터 디스크에 백업하는 것이 좋습니다.

    경우에 백업 또는 Azure storage를 사용 하 여 복원에 제공 된 권장 사항을 따릅니다 [SQL Server Backup to URL Best Practices and Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx) 고 [복원에서 백업에에서 저장 된 Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). [Azure Virtual Machines의 SQL Server에 대한 자동화된 백업](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)을 사용하여 이러한 백업을 자동화할 수도 있습니다.

-   **Azure Stack 저장소에 백업 합니다.** Azure Storage에 백업와 비슷한 방식으로 Azure Stack 저장소를 백업할 수 있습니다. SQL Server Management Studio (SSMS) 내에서 백업을 만들 때 구성 정보를 수동으로 입력 해야 합니다. SSMS를 사용 하 여 저장소 컨테이너 또는 공유 액세스 서명을 만들 수 없습니다. SSMS는 Azure Stack 구독 하지 Azure 구독에만 연결 됩니다. 대신, Azure Stack 포털 또는 PowerShell을 사용 하 여 저장소 계정, 컨테이너 및 공유 액세스 서명 만들기 해야 합니다.

    SQL Server 백업 대화 상자에 정보를 배치 하면:

    ![SQL Server 백업](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > 공유 액세스 서명 선행 공백 없이 Azure Stack 포털에서 SAS 토큰은 '?' 문자열입니다. 앞에 오는 삭제 해야 하는 포털에서 복사 함수를 사용 하는 경우 '?' SQL Server 내에서 작동 하도록 토큰입니다.

    한 번 설정 백업을 대상 있고 SQL Server의 구성에 백업할 수 있습니다 다음 Azure Stack blob 저장소에 있습니다.

## <a name="next-steps"></a>다음 단계

[서비스를 사용 하 여 또는 Azure Stack에 대 한 앱 빌드](azure-stack-considerations.md)