<properties 
	pageTitle="Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례"
	description="Microsoft Azure VM에서 SQL Server 성능을 최적화하기 위한 모범 사례를 제공합니다."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/24/2015"
	ms.author="jroth"/>

# Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례

## 개요

이 항목에서는 Microsoft Azure 가상 컴퓨터의 SQL Server 성능을 최적화하기 위한 모범 사례를 제공합니다. Azure 가상 컴퓨터에서 SQL Server를 실행하는 동안 온-프레미스 서버 환경의 SQL Server에 적용할 수 있는 동일한 데이터베이스 성능 튜닝 옵션을 계속 사용하는 것이 좋습니다. 그러나 공용 클라우드의 관계형 데이터베이스 성능은 가상 컴퓨터의 크기 및 데이터 디스크의 구성과 같은 많은 요인에 따라 달라집니다.

>[AZURE.NOTE]이 문서는 Azure VM의 SQL Server에 대해 최상의 성능을 얻는 데 초점을 맞춥니다. 작업이 적은 경우 아래 나열된 모든 최적화 사항이 필요하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항 및 작업 패턴을 고려하세요.

이 항목에 대한 도우미 리소스의 경우 [다음 백서를 다운로드](http://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/Performance%20Guidance%20for%20SQL%20Server%20in%20Windows%20Azure%20Virtual%20Machines.docx)할 수 있습니다. 해당 백서의 정보는 일부 지역에서는 오래된 것입니다. 예를 들어 이 백서는 현재 최상의 성능 결과를 위해 권장되는 프리미엄 저장소를 도입하기 전에 작성되었습니다.

## 빠른 검사 목록

다음은 Azure 가상 컴퓨터의 SQL Server 성능을 최적화하기 위한 빠른 검사 목록입니다.

- [프리미엄 저장소](../storage/storage-premium-storage-preview-portal.md)를 사용합니다.

- SQL Enterprise Edition의 경우 DS3 이상, SQL Standard Edition의 경우 DS2 이상의 [VM 크기](virtual-machines-size-specs.md)를 사용합니다.

- 최소 2개의 [P30 디스크](../storage/storage-premium-storage-preview-portal/#scalability-and-performance-targets-whko-KRing-premium-storage)를 사용합니다(로그 파일용 1개, 데이터 파일 및 TempDB용 1개).

- 동일한 지역에 SQL Server VM 및 [저장소 계정](../storage/storage-create-storage-account.md)을 유지합니다.

- 저장소 계정에서 Azure [지역 중복 저장소](../storage/storage-redundancy.md)(지역에서 복제)를 사용하지 않도록 설정합니다.

- 데이터베이스 저장소나 로깅을 위해 운영 체제 또는 임시 디스크를 사용하지 않습니다.

- 데이터 파일 및 TempDB를 호스트하는 디스크에서 읽기 캐싱을 사용하도록 설정합니다.

- 로그 파일을 호스트하는 디스크에서는 캐싱을 사용하도록 설정하지 마세요.

- IO 처리량이 증가하도록 여러 Azure 데이터 디스크를 스트라이프합니다.

- 문서화된 할당 크기로 포맷합니다.

- 데이터베이스 페이지 압축을 사용하도록 설정합니다.

- 데이터 파일에 대해 즉시 파일 초기화를 사용하도록 설정합니다.

- 데이터베이스에서 자동 증가를 제한하거나 사용하지 않도록 설정합니다.

- 데이터베이스에서 자동 축소를 사용하지 않도록 설정합니다.

- 시스템 데이터베이스를 포함하여 모든 데이터베이스를 데이터 디스크로 이동합니다.

- SQL Server 오류 로그 및 추적 파일 디렉터리를 데이터 디스크로 이동합니다.

- SQL Server 성능 픽스를 적용합니다.

- 기본 위치를 설정합니다.

- 잠긴 페이지를 사용하도록 설정합니다.

- Blob 저장소에 직접 백업합니다.

자세한 내용은 다음 하위 섹션에 제공된 지침을 따르세요.

## 가상 컴퓨터 크기 및 저장소 계정 고려 사항

성능이 중요한 응용 프로그램의 경우 다음 가상 컴퓨터 크기를 사용하는 것이 좋습니다.

- **SQL Server Enterprise Edition**: DS3 이상

- **SQL Server Standard Edition**: DS2 이상

지원되는 가상 컴퓨터 크기에 관한 최신 정보는 [가상 컴퓨터의 크기](virtual-machines-size-specs.md)를 참조하세요.

또한 전송 지연을 줄이기 위해 SQL Server 가상 컴퓨터와 동일한 데이터 센터에서 Azure 저장소 계정을 만드는 것이 좋습니다. 저장소 계정을 만들 때 여러 디스크 간에 일관된 쓰기 순서가 보장되지 않기 때문에 지역에서 복제를 사용하지 않도록 설정합니다. 대신 두 Azure 데이터 센터 간에 SQL Server 재해 복구 기술을 구성하는 것이 좋습니다. 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)를 참조하세요.

## 디스크 및 성능 고려 사항

Azure 가상 컴퓨터를 만들 때 플랫폼에서는 운영 체제 디스크에 대한 VM에 하나 이상의 디스크를 연결합니다. 이 디스크는 저장소에 페이지 Blob으로 저장된 VHD입니다. 추가 디스크는 가상 컴퓨터에 데이터 디스크로 연결될 수도 있으며 저장소에 페이지 Blob으로 저장됩니다. Azure 가상 컴퓨터에는 임시 디스크라는 다른 디스크가 있습니다. 이는 스크래치 공간에 사용할 수 있는 노드의 디스크입니다.

### 운영 체제 디스크

운영 체제 디스크는 운영 체제의 실행 버전으로 부팅하고 탑재할 수 있는 VHD이며 **C** 드라이브로 레이블이 지정됩니다.

운영 체제 디스크의 기본 캐싱 정책은 **읽기/쓰기**입니다. 성능이 중요한 응용 프로그램의 경우 운영 체제 디스크 대신에 데이터 디스크를 사용하는 것이 좋습니다. 아래의 데이터 디스크에 관한 섹션을 참조하세요.

### 임시 디스크

**D**: 드라이브로 레이블이 지정된 임시 저장소 드라이브는 Azure Blob 저장소에 유지되지 않습니다. 데이터 또는 로그 파일을 **D**: 드라이브에 저장하지 마세요.

D 시리즈 또는 G 시리즈의 VM(가상 컴퓨터)을 사용할 때 **D** 드라이브에 TempDB 및/또는 버퍼 풀 확장만 저장합니다. 다른 VM 시리즈와는 달리 D 시리즈 및 G 시리즈 VM의 **D** 드라이브는 SSD 기반입니다. 임시 개체를 많이 사용하거나 메모리에 맞지 않는 작업 집합이 있는 작업의 성능을 향상할 수 있습니다. 자세한 내용은 [Azure VM에서 SSD를 사용하여 SQL Server TempDB 및 버퍼 풀 확장 저장](http://blogs.technet.com/b/dataplatforminsider/archive/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions.aspx)을 참조하세요.

### 데이터 디스크

- **데이터 및 로그 파일에 대한 데이터 디스크 수**: 최소 2개의 [P30 디스크](../storage/storage-premium-storage-preview-portal.md)를 사용합니다(로그 파일용 1개, 데이터 파일 및 TempDB용 1개). 더 많은 처리량을 위해 추가 데이터 디스크가 필요할 수 있습니다. 데이터 디스크 수를 결정하려면 데이터 및 로그 디스크에 사용할 수 있는 IOPS 수를 분석해야 합니다. 자세한 내용은 [디스크용 프리미엄 저장소 사용](../storage/storage-premium-storage-preview-portal.md) 문서의 [VM 크기](virtual-machines-size-specs.md) 및 디스크 크기당 IOPS에 관한 표를 참조하세요. 더 많은 대역폭이 필요하면 추가 디스크를 연결하여 디스크 스트라이프를 사용할 수 있습니다. 프리미엄 저장소를 사용하지 않는 경우 해당 [VM 크기](virtual-machines-size-specs.md)로 지원되는 최대 수의 데이터 디스크를 추가하고 디스크 스트라이프를 사용하는 것이 좋습니다. 디스크 스트라이프에 대한 자세한 내용은 아래 관련된 섹션을 참조하세요.

- **캐싱 정책**: 데이터 파일 및 TempDB만 호스트하는 데이터 디스크에서 읽기 캐싱을 사용하도록 설정합니다. 프리미엄 저장소를 사용하지 않는 경우 모든 데이터 디스크에서 모든 캐싱을 사용하도록 설정하지 마세요. 디스크 캐싱 구성에 대한 지침은 [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) 및 [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx) 항목을 참조하세요.

- **NTFS 할당 단위 크기**: 데이터 디스크를 포맷할 때 TempDB뿐만 아니라 데이터 및 로그 파일에 대해 64KB 할당 단위 크기를 사용하는 것이 좋습니다.

- **디스크 스트라이프**: 다음 지침을 따르는 것이 좋습니다.

	- Windows 8/Windows Server 2012 이상의 경우 [저장소 공간](https://technet.microsoft.com/library/hh831739.aspx)을 사용합니다. 파티션 잘못 맞춤으로 인해 성능에 영향을 주지 않으려면 OLTP 작업에는 64KB, 데이터 웨어하우징 작업에는 256KB로 스트라이프 크기를 설정합니다. 또한 열 수를 실제 디스크 수로 설정합니다. 9개 이상의 디스크로 저장소 공간을 구성하려면 PowerShell(서버 관리자 UI 아님)을 사용하여 열 수를 디스크 수와 일치하도록 명시적으로 설정합니다. [저장소 공간](https://technet.microsoft.com/library/hh831739.aspx)을 구성하는 방법에 대한 자세한 내용은 [Windows PowerShell의 저장소 공간 Cmdlet](https://technet.microsoft.com/library/jj851254.aspx)을 참조하세요.
	
	- Windows 2008 R2 또는 이전 버전에서는 동적 디스크(OS 스트라이프 볼륨)를 사용할 수 있으며 스트라이프 크기는 항상 64KB입니다. 이 옵션은 Windows 8/Windows Server 2012부터 사용되지 않습니다. 자세한 내용은 [가상 디스크 서비스가 Windows 저장소 관리 API로 전환](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)에 있는 지원 설명을 참조하세요.
	
	- 작업에 로그가 많지 않고 전용 IOPS를 필요로 하지 않으면 1개의 저장소 풀만 구성할 수 있습니다. 그렇지 않은 경우 2개의 저장소 풀(로그 파일용 1개, 데이터 파일 및 TempDB용 1개)을 만듭니다. 예상되는 부하에 따라 각 저장소 풀에 연결되는 디스크 수를 결정합니다. VM 크기가 다르면 연결된 데이터 디스크 수도 다를 수 있다는 점에 유의하세요. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-size-specs.md)를 참조하세요.

## I/O 성능 고려 사항

- 프리미엄 저장소를 사용하여 최상의 결과를 얻으려면 응용 프로그램과 요청을 병렬 처리합니다. 프리미엄 저장소는 IO 큐 크기가 1보다 큰 시나리오에 대해 설계되었기 때문에 저장소를 많이 사용하더라도 단일 스레드 직렬 요청에 대한 성능 이점이 거의 없거나 전혀 없습니다. 예를 들어 이는 SQLIO와 같은 성능 분석 도구의 단일 스레드 테스트 결과에 영향을 줄 수 있습니다.

- [데이터베이스 페이지 압축](https://msdn.microsoft.com/library/cc280449.aspx)을 사용하면 I/O가 많은 작업의 성능이 향상될 수 있습니다. 그러나 데이터 압축은 데이터베이스 서버의 CPU 사용량을 늘릴 수 있습니다.

- 모든 데이터 파일은 Azure에서 송수신할 때 압축하는 것이 좋습니다.

- 초기 파일 할당에 필요한 시간을 줄이기 위해 즉시 파일 초기화를 사용하도록 설정하는 것이 좋습니다. 즉시 파일 초기화를 이용하려면 SE\_MANAGE\_VOLUME\_NAME으로 SQL Server(MSSQLSERVER) 서비스 계정을 부여하고 이를 **볼륨 유지 관리 작업 수행** 보안 정책에 추가합니다. Azure용 SQL Server 플랫폼 이미지를 사용하면 기본 서비스 계정(NT Service\\MSSQLSERVER)이 **볼륨 유지 관리 작업 수행** 보안 정책에 추가되지 않습니다. 즉, 즉시 파일 초기화는 SQL Server Azure 플랫폼 이미지에서 사용하도록 설정되어 있지 않습니다. SQL Server 서비스 계정을 **볼륨 유지 관리 작업 수행** 보안 정책에 추가한 후 SQL Server 서비스를 다시 시작합니다. 이 기능을 사용하기 위한 보안 고려 사항이 있을 수 있습니다. 자세한 내용은 [데이터베이스 파일 초기화](https://msdn.microsoft.com/library/ms175935.aspx)를 참조하세요.

- **자동 증가**는 예기치 않은 증가에 대한 대책으로만 고려합니다. 일상적으로 자동 증가를 사용하여 사용자 데이터 및 로그 증가를 관리하지 마세요. 자동 증가를 사용하면 Size 스위치를 사용하여 파일을 사전에 증가시킵니다.

- **자동 축소**를 사용하지 않도록 설정하여 성능에 부정적인 영향을 미칠 수 있는 불필요한 오버헤드를 방지합니다.

- SQL Server 2012를 실행 중인 경우 서비스 팩 1 누적 업데이트 10을 설치합니다. 이 업데이트에는 SQL Server 2012에서 임시 테이블에 대한 select 문을 실행할 때 I/O 성능 저하에 대한 픽스가 포함되어 있습니다. 자세한 내용은 [기술 자료 문서](http://support.microsoft.com/kb/2958012)를 참조하세요.

- 성능 향상을 위해 SQL Server의 시스템 데이터베이스(예: msdb 및 TempDB), 백업 및 기본 데이터와 로그 디렉터리를 캐시되지 않은 데이터 디스크로 이동합니다. 다음 작업을 수행합니다.

	- XEvent 및 추적 파일 경로를 조정합니다.
	
	- SQL 오류 로그 경로를 조정합니다.
	
	- 기본 백업 경로를 조정합니다.
	
	- 기본 데이터베이스 위치를 조정합니다.

- 잠긴 페이지를 설정하여 IO 및 페이징 작업을 줄입니다.

## 기능별 성능 고려 사항

더 많은 고급 구성 기술을 사용하면 일부 배포에서 추가적인 성능 이점을 얻을 수 있습니다. 다음 목록에는 성능 개선에 도움이 되는 일부 SQL Server 기능이 나와 있습니다.

- **Azure 저장소에 백업**: Azure 가상 컴퓨터에서 실행 중인 SQL Server의 백업을 수행할 때 [URL에 SQL Server 백업](https://msdn.microsoft.com/library/dn435916.aspx)을 사용할 수 있습니다. 이 기능은 SQL Server 2012 SP1 CU2부터 사용할 수 있으며 연결된 데이터 디스크에 백업하는 것이 좋습니다. Azure 저장소에 백업하거나 Azure 저장소에서 복원할 때 [URL에 SQL Server 백업의 모범 사례와 문제 해결 및 Azure 저장소에 저장된 백업에서 복원](https://msdn.microsoft.com/library/jj919149.aspx)에 제공된 권장 사항을 따르세요. [Azure 가상 컴퓨터의 SQL Server에 대한 자동화된 백업](virtual-machines-sql-server-automated-backup.md)을 사용하여 이러한 백업을 자동화할 수도 있습니다.

	SQL Server 2012 이전 버전에서는 [Azure에 SQL Server 백업 도구](https://www.microsoft.com/download/details.aspx?id=40740)를 사용할 수 있습니다. 이 도구는 여러 백업 스트라이프 대상을 사용하여 백업 처리량을 늘릴 수 있습니다.

- **Azure의 SQL Server 데이터 파일**: SQL Server 2014부터 새로운 기능인 [Azure의 SQL Server 데이터 파일](https://msdn.microsoft.com/library/dn385720.aspx)을 사용할 수 있습니다. Azure에서 데이터 파일로 SQL Server를 실행하면 Azure 데이터 디스크를 사용하는 것과 견줄 만한 성능 특성을 보여 줍니다.

## 다음 단계

SQL Server 및 프리미엄 저장소에 대한 보다 자세한 내용은 문서 [가상 컴퓨터에서 SQL Server와 함께 Azure 프리미엄 저장소 사용](virtual-machines-sql-server-use-premium-storage.md)을 참조하세요.

보안 모범 사례는 [Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항](virtual-machines-sql-server-security-considerations.md)을 참조하세요.

[Azure 가상 컴퓨터의 SQL Server 개요](virtual-machines-sql-server-infrastructure-services.md)에서 다른 SQL Server 가상 컴퓨터 항목을 검토하세요.

<!---HONumber=August15_HO9-->