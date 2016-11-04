---
title: SQL Server에 대한 성능 모범 사례 | Microsoft Docs
description: Microsoft Azure VM에서 SQL Server 성능을 최적화하기 위한 모범 사례를 제공합니다.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/07/2016
ms.author: jroth

---
# Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례
## 개요
이 항목에서는 Microsoft Azure 가상 컴퓨터의 SQL Server 성능을 최적화하기 위한 모범 사례를 제공합니다. Azure 가상 컴퓨터에서 SQL Server를 실행하는 동안 온-프레미스 서버 환경의 SQL Server에 적용할 수 있는 동일한 데이터베이스 성능 튜닝 옵션을 계속 사용하는 것이 좋습니다. 그러나 공용 클라우드의 관계형 데이터베이스 성능은 가상 컴퓨터의 크기 및 데이터 디스크의 구성과 같은 많은 요인에 따라 달라집니다.

SQL Server 이미지를 만들 때 [Azure 포털에서 VM을 프로비전하는 것을 고려하세요](virtual-machines-windows-portal-sql-server-provision.md). 리소스 관리자를 사용하여 포털에서 프로비전되는 SQL Server VM은 저장소 구성을 포함하여 이러한 모든 모범 사례를 구현합니다.

이 문서는 Azure VM의 SQL Server에 대해 *최상의* 성능을 얻는 데 중점을 둡니다. 작업이 적은 경우 아래 나열된 모든 최적화 사항이 필요하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항 및 작업 패턴을 고려하세요.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 빠른 검사 목록
다음은 Azure 가상 컴퓨터의 SQL Server 성능을 최적화하기 위한 빠른 검사 목록입니다.

| 영역 | 최적화 |
| --- | --- |
| [VM 크기](#vm-size-guidance) |SQL Enterprise Edition의 경우 [DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series) 이상,<br/><br/> SQL Standard 및 Web Edition의 경우 [DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series) 이상 |
| [저장소](#storage-guidance) |[프리미엄 저장소](../storage/storage-premium-storage.md)를 사용합니다. 표준 저장소는 개발/테스트에만 권장됩니다.<br/><br/>[저장소 계정](../storage/storage-create-storage-account.md)과 SQL Server VM을 동일한 위치에 둡니다.<br/><br/>저장소 계정의 Azure [지역 중복 저장소](../storage/storage-redundancy.md)(지역에서 복제)를 사용하지 않도록 설정합니다. |
| [디스크](#disks-guidance) |최소 2개의 [P30 디스크](../storage/storage-premium-storage.md#scalability-and-performance-targets-whko-KRing-premium-storage)를 사용합니다(로그 파일용 1개, 데이터 파일 및 TempDB용 1개).<br/><br/>데이터베이스 저장소나 로깅을 위해 운영 체제 또는 임시 디스크를 사용하지 않습니다.<br/><br/>데이터 파일 및 TempDB를 호스트하는 디스크에서 읽기 캐싱을 사용하도록 설정합니다.<br/><br/>로그 파일을 호스트하는 디스크에서는 캐싱을 사용하도록 설정하지 마세요.<br/><br/>중요: Azure VM 디스크에 대한 캐시 설정을 변경할 때는 SQL Server 서비스를 중지하세요.<br/><br/>IO 처리량이 증가하도록 여러 Azure 데이터 디스크를 스트라이프합니다.<br/><br/>문서화된 할당 크기로 포맷합니다. |
| [I/O](#io-guidance) |데이터베이스 페이지 압축을 사용하도록 설정합니다.<br/><br/>데이터 파일에 대해 즉시 파일 초기화를 사용하도록 설정합니다.<br/><br/>데이터베이스에서 자동 증가를 제한하거나 사용하지 않도록 설정합니다.<br/><br/>데이터베이스에서 자동 축소를 사용하지 않도록 설정합니다.<br/><br/>시스템 데이터베이스를 포함하여 모든 데이터베이스를 데이터 디스크로 이동합니다.<br/><br/>SQL Server 오류 로그 및 추적 파일 디렉터리를 데이터 디스크로 이동합니다.<br/><br/>기본 백업 및 데이터베이스 파일 위치를 설정합니다.<br/><br/>잠긴 페이지를 사용하도록 설정합니다.<br/><br/>SQL Server 성능 픽스를 적용합니다. |
| [기능 관련](#feature-specific-guidance) |Blob 저장소에 직접 백업합니다. |

이러한 최적화를 수행하는 *방법* 및 *이유*에 대한 자세한 내용은 다음 섹션에 나와 있는 세부 정보 및 지침을 참조하세요.

## VM 크기 지침
성능이 중요한 응용 프로그램의 경우 다음 [가상 컴퓨터 크기](virtual-machines-windows-sizes.md)를 사용하는 것이 좋습니다.

* **SQL Server Enterprise Edition**: DS3 이상
* **SQL Server Standard 및 Web Edition**: DS2 이상

## 저장소 지침
DS 시리즈(DSv2 시리즈 및 GS 시리즈와 함께) VM은 [프리미엄 저장소](../storage/storage-premium-storage.md)를 지원합니다. 프리미엄 저장소는 모든 프로덕션 워크로드에 권장됩니다.

> [!WARNING]
> 표준 저장소는 대기 시간 및 대역폭이 다양하므로 개발/테스트 워크로드에만 권장됩니다. 프로덕션 워크로드에는 프리미엄 저장소를 사용해야 합니다.
> 
> 

또한 전송 지연을 줄이기 위해 SQL Server 가상 컴퓨터와 동일한 데이터 센터에서 Azure 저장소 계정을 만드는 것이 좋습니다. 저장소 계정을 만들 때 여러 디스크 간에 일관된 쓰기 순서가 보장되지 않기 때문에 지역에서 복제를 사용하지 않도록 설정합니다. 대신 두 Azure 데이터 센터 간에 SQL Server 재해 복구 기술을 구성하는 것이 좋습니다. 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)를 참조하세요.

## 디스크 지침
Azure VM의 디스크 유형에는 크게 세 가지가 있습니다.

* **OS 디스크**: Azure 가상 컴퓨터를 만들 때 플랫폼에서는 운영 체제 디스크에 대한 VM에 하나 이상의 디스크(**C**: 드라이브로 레이블이 지정됨)를 연결합니다. 이 디스크는 저장소에 페이지 Blob으로 저장된 VHD입니다.
* **임시 디스크**: Azure 가상 컴퓨터는 임시 디스크(**D**: 드라이브로 레이블이 지정됨)라는 다른 디스크를 포함합니다. 이는 스크래치 공간에 사용할 수 있는 노드의 디스크입니다.
* **데이터 디스크**: 추가 디스크는 가상 컴퓨터에 데이터 디스크로 연결될 수도 있으며 저장소에 페이지 Blob으로 저장됩니다.

다음 섹션에서는 이러한 서로 다른 디스크를 사용하기 위한 권장 사항을 설명합니다.

### 운영 체제 디스크
운영 체제 디스크는 운영 체제의 실행 버전으로 부팅하고 탑재할 수 있는 VHD이며 **C** 드라이브로 레이블이 지정됩니다.

운영 체제 디스크의 기본 캐싱 정책은 **읽기/쓰기**입니다. 성능이 중요한 응용 프로그램의 경우 운영 체제 디스크 대신에 데이터 디스크를 사용하는 것이 좋습니다. 아래의 데이터 디스크에 관한 섹션을 참조하세요.

### 임시 디스크
**D**: 드라이브로 레이블이 지정된 임시 저장소 드라이브는 Azure Blob 저장소에 유지되지 않습니다. 사용자 데이터베이스 파일 또는 사용자 트랜잭션 로그 파일은 **D**: 드라이브에 저장하지 않도록 합니다.

D 시리즈, Dv2 시리즈 및 G 시리즈 VM의 경우 이러한 VM의 임시 드라이브는 SSD를 기반으로 합니다. 작업에 TempDB가 과도하게 사용될 경우(예: 임시 개체 또는 복잡한 조인) **D** 드라이브에 TempDB를 저장하면 TempDB 처리량은 더 높아지고 TempDB 대기 시간은 줄어들 수 있습니다.

프리미엄 저장소를 지원하는 VM(D 시리즈, Dv2 시리즈 및 G 시리즈)의 경우 읽기 캐싱을 사용하도록 설정된 프리미엄 저장소를 지원하는 디스크에 TempDB 및/또는 버퍼 풀 확장을 저장하는 것이 좋습니다. 이 권장 사항에 대한 한 가지 예외가 있습니다. TempDB가 주로 쓰기에 사용되는 경우 이러한 컴퓨터 크기에서 SSD 기반이기도 한, 로컬 **D** 드라이브에 TempDB를 저장하면 더 높은 성능을 실현할 수 있습니다.

### 데이터 디스크
* **데이터 및 로그 파일에 데이터 디스크 사용**: 최소 2개의 프리미엄 저장소 [P30 디스크](../storage/storage-premium-storage.md#scalability-and-performance-targets-whko-KRing-premium-storage)를 사용합니다(로그 파일용 1개, 데이터 파일 및 TempDB용 1개).
* **디스크 스트라이프**: 더 많은 처리량이 필요한 경우 추가 데이터 디스크를 추가하고 디스크 스트라이프를 사용할 수 있습니다. 데이터 디스크 수를 결정하려면 데이터 및 로그 디스크에 사용할 수 있는 IOPS 수를 분석해야 합니다. 자세한 내용은 [디스크용 프리미엄 저장소 사용](../storage/storage-premium-storage.md) 문서의 [VM 크기](virtual-machines-windows-sizes.md) 및 디스크 크기당 IOPS에 관한 표를 참조하세요. 다음 지침을 사용하세요.
  
  * Windows 8/Windows Server 2012 이상의 경우 [저장소 공간](https://technet.microsoft.com/library/hh831739.aspx)을 사용합니다. 파티션 잘못 맞춤으로 인해 성능에 영향을 주지 않으려면 OLTP 작업에는 64KB, 데이터 웨어하우징 작업에는 256KB로 스트라이프 크기를 설정합니다. 또한 열 수를 실제 디스크 수로 설정합니다. 9개 이상의 디스크로 저장소 공간을 구성하려면 PowerShell(서버 관리자 UI 아님)을 사용하여 열 수를 디스크 수와 일치하도록 명시적으로 설정합니다. [저장소 공간](https://technet.microsoft.com/library/hh831739.aspx)을 구성하는 방법에 대한 자세한 내용은 [Windows PowerShell의 저장소 공간 Cmdlet](https://technet.microsoft.com/library/jj851254.aspx)을 참조하세요.
  * Windows 2008 R2 또는 이전 버전에서는 동적 디스크(OS 스트라이프 볼륨)를 사용할 수 있으며 스트라이프 크기는 항상 64KB입니다. 이 옵션은 Windows 8/Windows Server 2012부터 사용되지 않습니다. 자세한 내용은 [가상 디스크 서비스가 Windows 저장소 관리 API로 전환](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)에 있는 지원 설명을 참조하세요.
  * 작업에 로그가 많지 않고 전용 IOPS를 필요로 하지 않으면 1개의 저장소 풀만 구성할 수 있습니다. 그렇지 않은 경우 2개의 저장소 풀(로그 파일용 1개, 데이터 파일 및 TempDB용 1개)을 만듭니다. 예상되는 부하에 따라 각 저장소 풀에 연결되는 디스크 수를 결정합니다. VM 크기가 다르면 연결된 데이터 디스크 수도 다를 수 있다는 점에 유의하세요. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-windows-sizes.md)를 참조하세요.
  * 프리미엄 저장소를 사용하지 않는 경우(개발/테스트 시나리오) 해당 [VM 크기](virtual-machines-windows-sizes.md)로 지원되는 최대 수의 데이터 디스크를 추가하고 디스크 스트라이프를 사용하는 것이 좋습니다.
* **캐싱 정책**: 프리미엄 저장소 데이터 디스크의 경우 데이터 파일 및 TempDB만 호스트하는 데이터 디스크에서 읽기 캐싱을 사용하도록 설정합니다. 프리미엄 저장소를 사용하지 않는 경우 모든 데이터 디스크에서 모든 캐싱을 사용하도록 설정하지 마세요. 디스크 캐싱 구성에 대한 지침은 [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) 및 [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx) 항목을 참조하세요.
  
  > [!WARNING]
  > 데이터베이스 손상 가능성을 방지하려면 Azure VM 디스크의 캐시 설정을 변경할 때 SQL Server 서비스를 중지합니다.
  > 
  > 
* **NTFS 할당 단위 크기**: 데이터 디스크를 포맷할 때 TempDB뿐만 아니라 데이터 및 로그 파일에 대해 64KB 할당 단위 크기를 사용하는 것이 좋습니다.
* **디스크 관리 모범 사례**: 데이터 디스크를 제거하거나 캐시 유형을 변경할 때 변경하는 동안 SQL Server 서비스를 중지합니다. OS 디스크에 대한 캐싱 설정이 변경되면 Azure는 VM을 중지하고, 캐시 유형을 변경하고 VM을 다시 시작합니다. 데이터 디스크의 캐시 설정이 변경될 경우 변경 중에 VM은 중지되지 않지만 데이터 디스크가 VM에서 분리되었다가 다시 연결됩니다.
  
  > [!WARNING]
  > 이러한 작업 중에 SQL Server 서비스를 중지하지 못하면 데이터베이스가 손상될 수 있습니다.
  > 
  > 

## I/O 지침
* 프리미엄 저장소를 사용하여 최상의 결과를 얻으려면 응용 프로그램과 요청을 병렬 처리합니다. 프리미엄 저장소는 IO 큐 크기가 1보다 큰 시나리오에 대해 설계되었기 때문에 저장소를 많이 사용하더라도 단일 스레드 직렬 요청에 대한 성능 이점이 거의 없거나 전혀 없습니다. 예를 들어 이는 SQLIO와 같은 성능 분석 도구의 단일 스레드 테스트 결과에 영향을 줄 수 있습니다.
* [데이터베이스 페이지 압축](https://msdn.microsoft.com/library/cc280449.aspx)을 사용하면 I/O가 많은 작업의 성능이 향상될 수 있습니다. 그러나 데이터 압축은 데이터베이스 서버의 CPU 사용량을 늘릴 수 있습니다.
* 초기 파일 할당에 필요한 시간을 줄이기 위해 즉시 파일 초기화를 사용하도록 설정하는 것이 좋습니다. 즉시 파일 초기화를 이용하려면 SE\_MANAGE\_VOLUME\_NAME으로 SQL Server(MSSQLSERVER) 서비스 계정을 부여하고 이를 **볼륨 유지 관리 작업 수행** 보안 정책에 추가합니다. Azure용 SQL Server 플랫폼 이미지를 사용하면 기본 서비스 계정(NT Service\\MSSQLSERVER)이 **볼륨 유지 관리 작업 수행** 보안 정책에 추가되지 않습니다. 즉, 즉시 파일 초기화는 SQL Server Azure 플랫폼 이미지에서 사용하도록 설정되어 있지 않습니다. SQL Server 서비스 계정을 **볼륨 유지 관리 작업 수행** 보안 정책에 추가한 후 SQL Server 서비스를 다시 시작합니다. 이 기능을 사용하기 위한 보안 고려 사항이 있을 수 있습니다. 자세한 내용은 [데이터베이스 파일 초기화](https://msdn.microsoft.com/library/ms175935.aspx)를 참조하세요.
* **자동 증가**는 예기치 않은 증가에 대한 대책으로만 고려합니다. 일상적으로 자동 증가를 사용하여 사용자 데이터 및 로그 증가를 관리하지 마세요. 자동 증가를 사용하면 Size 스위치를 사용하여 파일을 사전에 증가시킵니다.
* **자동 축소**를 사용하지 않도록 설정하여 성능에 부정적인 영향을 미칠 수 있는 불필요한 오버헤드를 방지합니다.
* 시스템 데이터베이스를 포함하여 모든 데이터베이스를 데이터 디스크로 이동합니다. 자세한 내용은 [시스템 데이터베이스 이동](https://msdn.microsoft.com/library/ms345408.aspx)을 참조하세요.
* SQL Server 오류 로그 및 추적 파일 디렉터리를 데이터 디스크로 이동합니다. SQL Server 구성 관리자에서 SQL Server 인스턴스를 마우스 오른쪽 단추로 클릭하고 속성을 선택하여 이를 수행할 수 있습니다. 오류 로그 및 추적 파일 설정은 **시작 매개 변수** 탭에서 변경할 수 있습니다. 덤프 디렉터리는 **고급** 탭에서 지정합니다. 다음 스크린샷에서는 오류 로그 시작 매개 변수를 찾을 위치를 보여 줍니다.
  
    ![SQL 오류 로그 스크린샷](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)
* 기본 백업 및 데이터베이스 파일 위치를 설정합니다. 이 항목의 권장 사항을 사용하여 서버 속성 창에서 변경합니다. 지침은 [데이터 및 로그 파일의 기본 위치 보기 또는 변경(SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)을 참조하세요. 다음 스크린샷에서는 이러한 변경 작업을 수행하는 위치를 보여 줍니다.
  
    ![SQL 데이터 로그 및 백업 파일](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* 잠긴 페이지를 사용하도록 설정하여 IO 및 페이징 작업을 줄입니다. 자세한 내용은 [메모리 내 페이지 잠금 옵션 사용(Windows)](https://msdn.microsoft.com/library/ms190730.aspx)을 참조하세요.
* SQL Server 2012를 실행 중인 경우 서비스 팩 1 누적 업데이트 10을 설치합니다. 이 업데이트에는 SQL Server 2012에서 임시 테이블에 대한 select 문을 실행할 때 I/O 성능 저하에 대한 픽스가 포함되어 있습니다. 자세한 내용은 [기술 자료 문서](http://support.microsoft.com/kb/2958012)를 참조하세요.
* 모든 데이터 파일은 Azure에서 송수신할 때 압축하는 것이 좋습니다.

## 기능 관련 지침
더 많은 고급 구성 기술을 사용하면 일부 배포에서 추가적인 성능 이점을 얻을 수 있습니다. 다음 목록에는 성능 개선에 도움이 되는 일부 SQL Server 기능이 나와 있습니다.

* **Azure 저장소에 백업**: Azure 가상 컴퓨터에서 실행 중인 SQL Server의 백업을 수행할 때 [URL에 SQL Server 백업](https://msdn.microsoft.com/library/dn435916.aspx)을 사용할 수 있습니다. 이 기능은 SQL Server 2012 SP1 CU2부터 사용할 수 있으며 연결된 데이터 디스크에 백업하는 것이 좋습니다. Azure 저장소에 백업하거나 Azure 저장소에서 복원할 때 [URL에 SQL Server 백업의 모범 사례와 문제 해결 및 Azure 저장소에 저장된 백업에서 복원](https://msdn.microsoft.com/library/jj919149.aspx)에 제공된 권장 사항을 따르세요. [Azure 가상 컴퓨터의 SQL Server에 대한 자동화된 백업](virtual-machines-windows-classic-sql-automated-backup.md)을 사용하여 이러한 백업을 자동화할 수도 있습니다.
  
    SQL Server 2012 이전 버전에서는 [Azure에 SQL Server 백업 도구](https://www.microsoft.com/download/details.aspx?id=40740)를 사용할 수 있습니다. 이 도구는 여러 백업 스트라이프 대상을 사용하여 백업 처리량을 늘릴 수 있습니다.
* **Azure의 SQL Server 데이터 파일**: SQL Server 2014부터 새로운 기능인 [Azure의 SQL Server 데이터 파일](https://msdn.microsoft.com/library/dn385720.aspx)을 사용할 수 있습니다. Azure에서 데이터 파일로 SQL Server를 실행하면 Azure 데이터 디스크를 사용하는 것과 견줄 만한 성능 특성을 보여 줍니다.

## 다음 단계
SQL Server 및 프리미엄 저장소에 대한 보다 자세한 내용은 문서 [가상 컴퓨터에서 SQL Server와 함께 Azure 프리미엄 저장소 사용](virtual-machines-windows-classic-sql-server-premium-storage.md)을 참조하세요.

보안 모범 사례는 [Azure 가상 컴퓨터의 SQL Server에 대한 보안 고려 사항](virtual-machines-windows-sql-security.md)을 참조하세요.

[Azure 가상 컴퓨터의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)에서 다른 SQL Server 가상 컴퓨터 항목을 검토하세요.

<!---HONumber=AcomDC_0907_2016-->