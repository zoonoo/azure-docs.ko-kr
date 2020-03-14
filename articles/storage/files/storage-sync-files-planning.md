---
title: Azure 파일 동기화 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포를 계획할 때 고려할 사항을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255120"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure 파일 동기화 배포에 대한 계획
[Azure Files](storage-files-introduction.md) 는 서버를 사용 하지 않는 azure 파일 공유를 직접 탑재 하거나 Azure File Sync를 사용 하 여 온-프레미스에서 azure 파일 공유를 캐시 하는 두 가지 주요 방법으로 배포할 수 있습니다. 선택 하는 배포 옵션에 따라 배포를 계획할 때 고려해 야 할 사항이 변경 됩니다. 

- **Azure 파일 공유의 직접 탑재**: Azure Files SMB 액세스를 제공 하기 때문에 Windows, Macos 및 Linux에서 제공 되는 표준 SMB 클라이언트를 사용 하 여 온-프레미스 또는 클라우드에서 azure 파일 공유를 탑재할 수 있습니다. Azure 파일 공유는 서버를 사용 하지 않으므로 프로덕션 시나리오를 위해 배포 하는 경우 파일 서버 또는 NAS 장치를 관리할 필요가 없습니다. 즉, 소프트웨어 패치를 적용 하거나 실제 디스크를 교환할 필요가 없습니다. 

- **Azure File Sync를 사용 하 여 Azure 파일 공유 온-프레미스 캐시**: Azure File Sync를 사용 하 여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지 하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure File Sync 온-프레미스 (또는 클라우드) Windows Server를 Azure 파일 공유의 빠른 캐시로 변환 합니다. 

이 문서에서는 Azure File Sync 배포에 대 한 배포 고려 사항을 주로 다룹니다. 온-프레미스 또는 클라우드 클라이언트에서 직접 탑재할 Azure 파일 공유의 배포를 계획 하려면 [Azure Files 배포 계획](storage-files-planning.md)을 참조 하세요.

## <a name="management-concepts"></a>관리 개념
Azure File Sync 배포에는 다음과 같은 세 가지 기본 관리 개체가 있습니다.

- **Azure 파일 공유**: azure 파일 공유는 서버를 사용 하지 않는 클라우드 파일 공유 이며 Azure File Sync 동기화 관계의 *클라우드 끝점* 을 제공 합니다. Azure 파일 공유의 파일은 SMB 또는 FileREST 프로토콜을 사용 하 여 직접 액세스할 수 있지만, Azure 파일 공유를 Azure File Sync와 함께 사용 하는 경우 Windows Server 캐시를 통해 파일에 액세스 하는 것이 좋습니다. 현재 Azure Files Windows Server와 같은 효율적인 변경 검색 메커니즘이 없기 때문입니다. 따라서 Azure 파일 공유에 대 한 변경 내용은 서버 끝점으로 다시 전파 되는 데 시간이 걸립니다.
- **서버 끝점**: Azure 파일 공유에 동기화 되는 Windows Server의 경로입니다. 볼륨 또는 볼륨의 루트에 있는 특정 폴더 일 수 있습니다. 여러 서버 끝점의 네임 스페이스가 겹치지 않는 경우 동일한 볼륨에 있을 수 있습니다.
- **동기화 그룹**: **클라우드 끝점**또는 Azure 파일 공유와 서버 끝점 간의 동기화 관계를 정의 하는 개체입니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 예를 들어 Azure File Sync를 사용 하 여 관리 하려는 2 개의 고유한 파일 집합이 있는 경우 두 개의 동기화 그룹을 만들고 각 동기화 그룹에 다른 끝점을 추가 합니다.

### <a name="azure-file-share-management-concepts"></a>Azure 파일 공유 관리 개념
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync 관리 개념
동기화 그룹은 Azure File Sync와 함께 사용 하기 위해 서버를 등록 하 고 동기화 그룹 관계를 포함 하는 최상위 개체인 **Storage 동기화 서비스**에 배포 됩니다. Storage 동기화 서비스 리소스는 스토리지 계정 리소스의 피어로, Azure 리소스 그룹에 쉽게 배포할 수 있습니다. 저장소 동기화 서비스는 여러 저장소 계정 및 등록 된 여러 Windows 서버에서 Azure 파일 공유를 포함 하는 동기화 그룹을 만들 수 있습니다.

저장소 동기화 서비스에서 동기화 그룹을 만들려면 먼저 저장소 동기화 서비스에 Windows Server를 등록 해야 합니다. 그러면 서버 또는 클러스터와 저장소 동기화 서비스 간의 트러스트 관계를 나타내는 **등록 된 서버** 개체가 만들어집니다. 저장소 동기화 서비스를 등록 하려면 먼저 Azure File Sync 에이전트를 서버에 설치 해야 합니다. 개별 서버 또는 클러스터는 한 번에 하나의 저장소 동기화 서비스에만 등록할 수 있습니다.

동기화 그룹에는 하나의 클라우드 끝점, Azure 파일 공유 및 하나 이상의 서버 끝점이 포함 되어 있습니다. 서버 끝점 개체에는 Azure File Sync의 캐싱 기능을 제공 하는 **클라우드 계층화** 기능을 구성 하는 설정이 포함 되어 있습니다. Azure 파일 공유와 동기화 하기 위해 Azure 파일 공유를 포함 하는 저장소 계정은 저장소 동기화 서비스와 동일한 Azure 지역에 있어야 합니다.

### <a name="management-guidance"></a>관리 지침
Azure File Sync 배포 하는 경우 다음을 수행 하는 것이 좋습니다.

- Windows 파일 공유를 사용 하 여 Azure 파일 공유 1:1 배포 서버 끝점 개체는 동기화 관계의 서버 쪽에서 동기화 토폴로지를 설정 하는 방법에 대 한 상당한 유연성을 제공 합니다. 관리를 간소화 하기 위해 서버 끝점의 경로가 Windows 파일 공유의 경로와 일치 하는지 확인 합니다. 

- 저장소 동기화 서비스를 최대한 적게 사용 합니다. 이렇게 하면 Windows Server를 한 번에 하나의 저장소 동기화 서비스에만 등록할 수 있으므로 여러 서버 끝점을 포함 하는 동기화 그룹이 있는 경우 관리가 간단해 집니다. 

- Azure 파일 공유를 배포할 때 저장소 계정의 IOPS 제한 사항에 주의 합니다. 이상적으로는 파일 공유 1:1을 저장소 계정에 매핑할 수 있지만,이는 조직 및 Azure의 다양 한 제한 및 제한 사항으로 인해 항상 가능 하지는 않습니다. 하나의 저장소 계정에 파일 공유를 하나만 배포할 수 없는 경우 가장 많이 사용한 파일 공유가 동일한 저장소 계정에 저장 되지 않도록 하는 공유와 활성 상태가 되는 공유를 고려해 야 합니다.

## <a name="windows-file-server-considerations"></a>Windows 파일 서버 고려 사항
Windows Server에서 동기화 기능을 사용 하도록 설정 하려면 Azure File Sync 다운로드할 수 있는 에이전트를 설치 해야 합니다. Azure File Sync 에이전트는 두 가지 주요 구성 요소인 `FileSyncSvc.exe`, 서버 끝점의 변경 내용을 모니터링 하 고 동기화 세션을 시작 하는 백그라운드 Windows 서비스, 클라우드 계층화 및 빠른 재해 복구를 지 원하는 파일 시스템 필터 `StorageSync.sys`를 제공 합니다.  

### <a name="operating-system-requirements"></a>운영 체제 요구 사항
Azure File Sync은 다음 버전의 Windows Server에서 지원 됩니다.

| 버전 | 지원되는 SKU | 지원되는 배포 옵션 |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard 및 IoT | 전체 및 코어 |
| Windows Server 2016 | Datacenter, Standard 및 Storage Server | 전체 및 코어 |
| Windows Server 2012 R2 | Datacenter, Standard 및 Storage Server | 전체 및 코어 |

이후 버전의 Windows Server는 출시되면 추가될 예정입니다.

> [!Important]  
> Windows 업데이트의 최신 업데이트를 사용하여 Azure 파일 동기화와 함께 사용되는 모든 서버를 최신 상태로 유지하는 것이 좋습니다. 

### <a name="minimum-system-resources"></a>최소 시스템 리소스
Azure File Sync에는 하나 이상의 CPU와 최소 2 GiB의 메모리를 포함 하는 서버 (실제 또는 가상)가 필요 합니다.

> [!Important]  
> 동적 메모리를 사용 하는 가상 머신에서 서버를 실행 하는 경우 최소 2048 MiB의 메모리로 VM을 구성 해야 합니다.

대부분의 프로덕션 워크 로드의 경우 최소 요구 사항만으로 Azure File Sync Sync 서버를 구성 하지 않는 것이 좋습니다. 자세한 내용은 [권장 시스템 리소스](#recommended-system-resources) 를 참조 하세요.

### <a name="recommended-system-resources"></a>권장 시스템 리소스
서버 기능 또는 응용 프로그램과 마찬가지로 Azure File Sync의 시스템 리소스 요구 사항은 배포 규모에 따라 결정 됩니다. 서버에서 더 큰 배포에는 더 많은 시스템 리소스가 필요 합니다. Azure File Sync의 경우 서버 끝점 전체의 개체 수와 데이터 집합의 변동에 따라 소수 자릿수가 결정 됩니다. 단일 서버는 여러 동기화 그룹에 서버 끝점을 포함할 수 있으며 서버가 연결 된 전체 네임 스페이스에 대 한 다음 테이블 계정에 나열 된 개체 수를 포함할 수 있습니다. 

예를 들어 서버 끝점 A에는 1000만 개의 개체가 있고 서버 끝점 B에는 1000만 objects = 2000만 개체가 있습니다. 이 예의 경우 Cpu 8 개, 안정적인 상태에 대 한 16 GiB의 메모리 및 초기 마이그레이션의 경우 48의 메모리 (가능한 경우)를 권장 합니다.
 
네임 스페이스 데이터는 성능상의 이유로 메모리에 저장 됩니다. 따라서 좋은 성능을 유지 하기 위해 더 큰 네임 스페이스에 더 많은 메모리가 필요 하 고, 더 많은 변동에는 처리 하는 데 더 많은 CPU가 필요 합니다. 
 
다음 표에서는 일반적인 범용 파일 공유의 용량에 대 한 변환 뿐만 아니라 네임 스페이스의 크기를 모두 제공 했습니다. 여기서 평균 파일 크기는 512 KiB입니다. 파일 크기가 더 작은 경우 동일한 용량의 용량에 메모리를 더 추가 하는 것이 좋습니다. 네임 스페이스의 크기에 맞게 메모리 구성을 설정 합니다.

| 네임 스페이스 크기-파일 & 디렉터리 (수백만)  | 일반적인 용량 (TiB)  | CPU 코어 수  | 권장 메모리 (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (초기 동기화)/2 (일반적인 변동)      |
| 5        | 2.3     | 2        | 16 (초기 동기화)/4 (일반적인 변동)    |
| 10       | 4.7     | 4        | 32 (초기 동기화)/8 (일반적인 변동)   |
| 30       | 14.0    | 8        | 48 (초기 동기화)/16 (일반적인 변동)   |
| 50       | 23.3    | 16       | 64 (초기 동기화)/32 (일반적인 변동)  |
| 100*     | 46.6    | 32       | 128 (초기 동기화)/32 (일반적인 변동)  |

1억 개 이상의 파일을 동기화 하는 \*& 디렉터리가 권장 되지 않습니다. 이는 테스트 된 임계값을 기반으로 하는 소프트 제한입니다. 자세한 내용은 [Azure Files 확장성 및 성능 목표](storage-files-scale-targets.md#azure-file-sync-scale-targets)를 참조 하세요.

> [!TIP]
> 네임 스페이스의 초기 동기화는 집약적인 작업 이므로 초기 동기화가 완료 될 때까지 추가 메모리를 할당 하는 것이 좋습니다. 이는 필수는 아니지만 초기 동기화 속도를 높일 수 있습니다. 
> 
> 일반적인 변동은 하루에 변경 되는 네임 스페이스의 0.5%입니다. 더 높은 변동 수준을 위해 CPU를 더 추가 하는 것이 좋습니다. 

- NTFS 파일 시스템으로 포맷되어 로컬로 연결된 볼륨

### <a name="evaluation-cmdlet"></a>Evaluation cmdlet
Azure File Sync를 배포 하기 전에 Azure File Sync evaluation cmdlet을 사용 하 여 시스템과 호환 되는지 여부를 평가 해야 합니다. 이 cmdlet은 지원 되지 않는 문자 또는 지원 되지 않는 운영 체제 버전과 같은 파일 시스템 및 데이터 집합의 잠재적인 문제를 확인 합니다. 해당 검사는 아래에 설명 된 기능 중 일부에 대해서만 적용 됩니다. 배포를 원활 하 게 진행 하려면이 섹션의 나머지 부분을 자세히 읽어 보는 것이 좋습니다. 

[Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-Az-ps)지침에 따라 설치할 수 있는 Az PowerShell module을 설치 하 여 evaluation cmdlet을 설치할 수 있습니다.

#### <a name="usage"></a>사용  
평가 도구는 몇 가지 다른 방법으로 호출할 수 있습니다. 즉 시스템 검사, 데이터 세트 검사 또는 둘 다를 수행할 수 있습니다. 시스템 검사 및 데이터 세트 검사를 모두 수행하려면 다음을 수행합니다. 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

데이터 세트만 테스트하려면 다음을 수행합니다.
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
시스템 요구 사항만 테스트하려면 다음을 수행합니다.
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
결과를 CSV 형식으로 표시하려면 다음을 수행합니다.
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>파일 시스템 호환성
Azure File Sync는 직접 연결 된 NTFS 볼륨 에서만 지원 됩니다. Windows Server의 직접 연결 된 저장소 또는 DAS는 Windows Server 운영 체제에서 파일 시스템을 소유 하 고 있음을 의미 합니다. 파일 서버에 물리적으로 디스크를 연결 하거나, 가상 디스크를 파일 서버 VM (예: Hyper-v에서 호스팅하는 VM) 또는 ISCSI를 통해 연결 하 여 DAS를 제공할 수 있습니다.

NTFS 볼륨만 지원 됩니다. ReFS, FAT, FAT32 및 기타 파일 시스템은 지원 되지 않습니다.

다음 표에서는 NTFS 파일 시스템 기능의 interop 상태를 보여 줍니다. 

| 기능 | 상태 지원 | 메모 |
|---------|----------------|-------|
| ACL(액세스 제어 목록) | 완벽하게 지원 | Windows 스타일의 임의 액세스 제어 목록은 Azure File Sync에 의해 유지 되며 서버 끝점에서 Windows Server에 의해 적용 됩니다. Azure 파일 공유를 직접 탑재 하는 경우 Acl을 강제로 적용할 수도 있지만이 경우 추가 구성이 필요 합니다. 자세한 내용은 [id 섹션](#identity) 을 참조 하세요. |
| 하드 링크 | 건너뜀 | |
| 바로 가기 링크 | 건너뜀 | |
| 탑재 지점 | 부분적으로 지원됨 | 탑재 지점은 서버 엔드포인트의 루트일 수 있지만, 서버 엔드포인트의 네임스페이스에 포함된 경우 건너뜁니다. |
| 분기 동기화 | 건너뜀 | 분산 파일 시스템 DfrsrPrivate 및 DFSRoots 폴더를 예로 들 수 있습니다. |
| 재분석 지점 | 건너뜀 | |
| NTFS 압축 | 완벽하게 지원 | |
| 스파스 파일 | 완벽하게 지원 | 스파스 파일은 동기화되지만(차단되지 않음) 전체 파일로 클라우드와 동기화됩니다. 클라우드(또는 다른 서버)에서 파일 콘텐츠가 변경될 경우 변경 내용이 다운로드되면 파일은 더 이상 스파스 파일이 아닙니다. |
| ADS(대체 데이터 스트림) | 보존되지만 동기화되지 않음 | 예를 들어, 파일 분류 인프라에서 만들어진 분류 태그는 동기화되지 않습니다. 각 서버 엔드포인트의 파일에 대한 기존 분류 태그는 그대로 유지됩니다. |

<a id="files-skipped"></a>또한 Azure File Sync는 특정 임시 파일 및 시스템 폴더도 건너뜁니다.

| 파일/폴더 | 참고 |
|-|-|
| pagefile.sys | 시스템에 특정된 파일 |
| Desktop.ini | 시스템에 특정된 파일 |
| thumbs.db | 썸네일의 임시 파일 |
| ehthumbs.db | 미디어 미리 보기의 임시 파일 |
| ~$\*.\* | Office 임시 파일 |
| \*.tmp | 임시 파일 |
| \*.laccdb | Access DB 잠금 파일|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 내부 동기화 파일|
| \\시스템 볼륨 정보 | 볼륨에 특정된 폴더 |
| $RECYCLE.BIN| 폴더 |
| \\SyncShareState | 동기화할 폴더 |

### <a name="failover-clustering"></a>장애 조치(Failover) 클러스터링
Windows Server 장애 조치(Failover) 클러스터링은 "범용 파일 서버" 배포 옵션의 Azure 파일 동기화에서 지원됩니다. 장애 조치(Failover) 클러스터링은 "애플리케이션 데이터용 스케일 아웃 파일 서버" 또는 "CSV(클러스터된 공유 볼륨)"에서는 지원되지 않습니다.

> [!Note]  
> 동기화가 제대로 작동하려면 장애 조치(Failover) 클러스터의 모든 노드에 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="data-deduplication"></a>데이터 중복 제거
**Windows server 2016 및 Windows server 2019**   
Windows Server 2016 및 Windows Server 2019에서 클라우드 계층화를 사용하면 볼륨의 데이터 중복 제거가 지원됩니다. 클라우드 계층화를 사용 하는 볼륨에서 데이터 중복 제거를 사용 하도록 설정 하면 더 많은 저장소를 프로 비전 하지 않고 온-프레미스에서 더 많은 파일을 캐시할 수 

클라우드 계층화를 사용 하는 볼륨에서 데이터 중복 제거를 사용 하도록 설정 하면 서버 끝점 위치 내의 중복 제거 최적화 파일은 클라우드 계층화 정책 설정에 따라 일반 파일과 비슷하게 계층화 됩니다. 중복 제거 최적화 파일이 계층화 되 면 데이터 중복 제거 가비지 수집 작업이 자동으로 실행 되어 볼륨의 다른 파일에서 더 이상 참조 되지 않는 불필요 한 청크를 제거 하 여 디스크 공간을 회수 합니다.

볼륨 절감은 서버에만 적용 됩니다. Azure 파일 공유의 데이터는 중복 제거 된 되지 않습니다.

> [!Note]  
> Windows Server 2019에서 클라우드 계층화를 사용 하는 볼륨에서 데이터 중복 제거를 지원 하려면 Windows update [KB4520062](https://support.microsoft.com/help/4520062) 가 설치 되어 있어야 하 Azure File Sync 고 에이전트 버전 9.0.0.0 이상이 필요 합니다.

**Windows Server 2012 R2**  
Azure File Sync는 Windows Server 2012 r 2의 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원 하지 않습니다. 볼륨에서 데이터 중복 제거를 사용 하는 경우 클라우드 계층화를 사용 하지 않도록 설정 해야 합니다. 

**참고 사항**
- Azure File Sync 에이전트를 설치 하기 전에 데이터 중복 제거를 설치한 경우 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원 하려면 다시 시작 해야 합니다.
- 클라우드 계층화를 사용 하도록 설정한 후 볼륨에서 데이터 중복 제거를 사용 하는 경우 초기 중복 제거 최적화 작업은 볼륨에서 아직 계층화 되지 않은 파일을 최적화 하 고 클라우드 계층화에 다음과 같은 영향을 미칩니다.
    - 사용 가능한 공간 정책은 열 지도를 사용 하 여 볼륨의 사용 가능한 공간에 따라 파일을 계속 계층 합니다.
    - 날짜 정책은 파일에 액세스 하는 중복 제거 최적화 작업으로 인해 계층화 될 수 있는 파일의 계층화를 건너뜁니다.
- 지속적인 중복 제거 최적화 작업의 경우 파일이 아직 계층화 되지 않은 경우 데이터 중복 제거 [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) 설정에 의해 날짜 정책을 사용한 클라우드 계층화가 지연 됩니다. 
    - 예: MinimumFileAgeDays 설정이 7 일이 고 클라우드 계층화 날짜 정책이 30 일인 경우 날짜 정책은 37 일 후에 파일을 계층화 합니다.
    - 참고: Azure File Sync에 의해 파일이 계층화 되 면 중복 제거 최적화 작업은 파일을 건너뜁니다.
- Azure File Sync 에이전트가 설치 된 Windows Server 2012 r 2를 실행 하는 서버를 Windows Server 2016 또는 Windows Server 2019로 업그레이드 하는 경우 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원 하려면 다음 단계를 수행 해야 합니다.  
    - Windows Server 2012 r 2 용 Azure File Sync 에이전트를 제거 하 고 서버를 다시 시작 합니다.
    - 새 서버 운영 체제 버전 (Windows Server 2016 또는 Windows Server 2019)에 대 한 Azure File Sync 에이전트를 다운로드 합니다.
    - Azure File Sync 에이전트를 설치 하 고 서버를 다시 시작 합니다.  
    
    참고: 에이전트를 제거 하 고 다시 설치 하면 서버의 Azure File Sync 구성 설정이 유지 됩니다.

### <a name="distributed-file-system-dfs"></a>분산 파일 시스템(DFS)
Azure 파일 동기화에서는 DFS-N(DFS 네임스페이스) 및 DFS-R(DFS 복제)과의 상호 작용을 지원합니다.

**DFS 네임 스페이스(DFS-N)** : DFS-N 서버에서 Azure 파일 동기화가 완전히 지원됩니다. 하나 이상의 DFS-N 멤버에 Azure 파일 동기화 에이전트를 설치하면 서버 엔드포인트 및 클라우드 엔드포인트 간에 데이터를 동기화할 수 있습니다. 자세한 내용은 [DFS 네임스페이스 개요](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)를 참조하세요.
 
**DFS 복제 (dfs-r)** : dfs 및 Azure File Sync 모두 복제 솔루션 이기 때문에 대부분의 경우에는 Dfs-r을 Azure File Sync로 바꾸는 것이 좋습니다. 그러나 DFS-R을 사용 하 고 함께 Azure File Sync 하는 몇 가지 시나리오가 있습니다.

- DFS-R 배포에서 Azure 파일 동기화 배포로 마이그레이션하는 중입니다. 자세한 내용은 [DFS 복제(DFS-R) 배포를 Azure 파일 동기화로 마이그레이션](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)을 참조하세요.
- 파일 데이터의 복사본을 필요로 하는 모든 온-프레미스 서버를 인터넷에 직접 연결할 수 있는 것은 아닙니다.
- 분기 서버는 Azure 파일 동기화를 사용할 단일 허브 서버에 데이터를 통합합니다.

Azure File Sync와 DFS가 나란히 작동 하도록 하려면 다음을 수행 합니다.

1. Azure 파일 동기화 클라우드 계층화는 DFS-R 복제 폴더를 포함하는 볼륨에서 사용하지 않도록 설정해야 합니다.
2. 서버 엔드포인트는 DFS-R 읽기 전용 복제 폴더에 대해 구성할 수 없습니다.

자세한 내용은 [DFS 복제 개요](https://technet.microsoft.com/library/jj127250)를 참조하세요.

### <a name="sysprep"></a>Sysprep
Azure File Sync 에이전트가 설치 된 서버에서 sysprep를 사용 하는 것은 지원 되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 에이전트 설치 및 서버 등록은 서버 이미지 배포 및 sysprep 최소 설치 완료 후 발생해야 합니다.

### <a name="windows-search"></a>Windows 검색
클라우드 계층화가 서버 엔드포인트에서 활성화되면 계층화된 파일은 건너뛰고 Windows 검색을 통해 인덱싱되지 않습니다. 계층화되지 않은 파일은 제대로 인덱싱됩니다.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>다른 HSM(계층적 스토리지 관리) 솔루션
다른 HSM 솔루션은 Azure 파일 동기화와 함께 사용하면 안 됩니다.

## <a name="identity"></a>ID
Azure File Sync은 동기화를 설정 하는 것 외에는 특별 한 설정 없이 표준 AD 기반 id로 작동 합니다. Azure File Sync를 사용 하는 경우 대부분의 액세스는 Azure 파일 공유 대신 Azure File Sync 캐싱 서버를 통과 하는 것이 일반적입니다. 서버 끝점은 Windows Server에 있고 Windows Server는 매우 긴 시간 동안 AD 및 Windows 스타일 Acl을 지원 하기 때문에 저장소 동기화 서비스에 등록 된 Windows 파일 서버가 도메인에 가입 되어 있는지 확인 하는 것 외에는 아무 작업도 필요 하지 않습니다. Azure File Sync는 Azure 파일 공유의 파일에 Acl을 저장 하 고 모든 서버 끝점에 복제 합니다.

Azure 파일 공유에 대 한 변경 내용이 동기화 그룹의 서버 끝점과 동기화 하는 데 더 오래 걸리므로 클라우드에서 직접 파일 공유에 대 한 AD 권한을 적용할 수 있는지도 확인할 수 있습니다. 이렇게 하려면 Windows 파일 서버가 도메인에 가입 된 방식과 마찬가지로 저장소 계정을 온-프레미스 AD에 도메인에 가입 시켜야 합니다. 고객 소유 Active Directory에 저장소 계정을 가입 하는 도메인에 대해 자세히 알아보려면 [Azure Files Active Directory 개요](storage-files-active-directory-overview.md)를 참조 하세요.

> [!Important]  
> Azure File Sync를 성공적으로 배포 하기 위해 저장소 계정을 Active Directory에 가입 하는 도메인은 필요 하지 않습니다. 사용자가 Azure 파일 공유를 직접 탑재할 때 Azure 파일 공유에서 온-프레미스 Acl을 적용할 수 있도록 하는 엄격한 선택적 단계입니다.

## <a name="networking"></a>네트워킹
Azure File Sync 에이전트는 Azure File Sync REST 프로토콜 및 FileREST 프로토콜을 사용 하 여 저장소 동기화 서비스 및 Azure 파일 공유와 통신 하며, 둘 다 항상 포트 443을 통해 HTTPS를 사용 합니다. SMB는 Windows 서버와 Azure 파일 공유 간에 데이터를 업로드 하거나 다운로드 하는 데 사용 되지 않습니다. 대부분의 조직에서는 443 포트를 통한 HTTPS 트래픽을 허용 하기 때문에 대부분의 웹 사이트를 방문 하기 위한 요구 사항에 따라 특수 네트워킹 구성은 일반적으로 Azure File Sync를 배포 하는 데 필요 하지 않습니다.

조직의 정책 또는 고유한 규정 요구 사항에 따라 Azure와의 더 제한적인 통신이 필요할 수 있으므로 네트워킹을 구성 하는 여러 가지 메커니즘을 Azure File Sync 있습니다. 요구 사항에 따라 다음을 수행할 수 있습니다.

- Express 경로 또는 Azure VPN을 통해 터널 동기화 및 파일 업로드/다운로드 트래픽 
- 서비스 끝점과 개인 끝점과 같은 Azure Files 및 Azure 네트워킹 기능을 활용 합니다.
- 사용자 환경에서 프록시를 지원 하도록 Azure File Sync를 구성 합니다.
- Azure File Sync에서 네트워크 활동을 제한 합니다.

Azure File Sync 네트워킹 기능을 구성 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
- [Azure 파일 동기화 프록시 및 방화벽 설정](storage-sync-files-firewall-and-proxy.md)
- [Azure File Sync 데이터 센터에서 좋은 환경 인지 확인](storage-sync-files-server-registration.md)

## <a name="encryption"></a>암호화
Azure File Sync를 사용 하는 경우 세 가지 다른 암호화 계층을 고려해 야 합니다. 즉, Windows Server의 미사용 저장소에 대 한 암호화, Azure File Sync 에이전트와 Azure 간의 전송 암호화 및 Azure 파일 공유에서 데이터의 미사용 데이터 암호화를 고려해 야 합니다. 

### <a name="windows-server-encryption-at-rest"></a>미사용 Windows Server 암호화 
일반적으로 Azure File Sync와 함께 작동 하는 Windows Server에서 데이터를 암호화 하는 두 가지 전략이 있습니다. 파일 시스템은 암호화 된 파일 시스템과 파일 시스템에 기록 된 모든 데이터를 암호화 하 고 파일 형식 내에서 암호화 합니다. 이러한 메서드는 함께 사용할 수 없습니다. 암호화 목적이 다르기 때문에 원하는 경우 함께 사용할 수 있습니다.

파일 시스템 아래에서 암호화를 제공 하기 위해 Windows Server는 BitLocker 수신함을 제공 합니다. BitLocker는 Azure File Sync에 완전히 투명 합니다. BitLocker와 같은 암호화 메커니즘을 사용 하는 주된 이유는 디스크를 도용 하 여 온-프레미스 데이터 센터에서 물리적으로 데이터를 필터링 하는 것을 방지 하 고 권한이 없는 OS에서 데이터에 대 한 무단 읽기/쓰기를 수행 하는 것을 방지 하는 것입니다. BitLocker에 대해 자세히 알아보려면 [bitlocker 개요](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)를 참조 하세요.

BitLocker와 유사 하 게 작동 하는 타사 제품 (NTFS 볼륨 아래에 있는 경우)은 Azure File Sync를 사용 하 여 완전히 투명 하 게 작동 해야 합니다. 

데이터를 암호화 하는 다른 주요 방법은 응용 프로그램에서 파일을 저장할 때 파일의 데이터 스트림을 암호화 하는 것입니다. 일부 응용 프로그램은 기본적으로이 작업을 수행할 수 있지만, 일반적으로는 그렇지 않습니다. 파일의 데이터 스트림을 암호화 하는 방법에 대 한 예제는 AIP (Azure Information Protection)/Azure Rights Management 서비스 (Azure RMS)/Active Directory RMS입니다. AIP/RMS와 같은 암호화 메커니즘을 사용 하는 주된 이유는 사용자가 플래시 드라이브와 같은 대체 위치로 데이터를 복사 하거나 권한 없는 사용자에 게 전자 메일로 전송 하 여 파일 공유에서 데이터를 필터링 하는 것을 방지 하는 것입니다. 파일의 데이터 스트림이 파일 형식의 일부로 암호화 되는 경우이 파일은 계속 해 서 Azure 파일 공유에서 암호화 됩니다. 

Azure File Sync ntfs EFS (NTFS 암호화 된 파일 시스템) 또는 파일 시스템 위에 있지만 파일의 데이터 스트림 아래에 있는 타사 암호화 솔루션과 상호 운용 되지 않습니다. 

### <a name="encryption-in-transit"></a>전송 중 암호화
Azure File Sync 에이전트는 Azure File Sync REST 프로토콜 및 FileREST 프로토콜을 사용 하 여 저장소 동기화 서비스 및 Azure 파일 공유와 통신 하며, 둘 다 항상 포트 443을 통해 HTTPS를 사용 합니다. Azure File Sync는 HTTP를 통해 암호화 되지 않은 요청을 보내지 않습니다. 

Azure storage 계정에는 전송 중 암호화가 필요 하기 위한 스위치가 포함 되어 있으며,이는 기본적으로 사용 하도록 설정 되어 있습니다. 저장소 계정 수준의 스위치를 사용할 수 없는 경우에도 Azure 파일 공유에 대 한 암호화 되지 않은 연결이 가능 하다는 것을 의미 하며,이 경우에도 암호화 된 채널을 사용 하 여 파일 공유에 액세스할 Azure File Sync.

저장소 계정에 대 한 전송에서 암호화를 사용 하지 않도록 설정 하는 주된 이유는 Windows Server 2008 R2 또는 이전 버전의 배포와 같은 이전 운영 체제에서 실행 해야 하는 레거시 응용 프로그램을 지원 하 여 Azure 파일 공유와 직접 통신 하는 것입니다. 레거시 응용 프로그램이 파일 공유의 Windows Server 캐시와 통신 하는 경우이 설정을 전환 해도 아무런 영향을 주지 않습니다. 

전송 중인 데이터의 암호화를 사용 하도록 설정 하는 것이 좋습니다.

전송 중 암호화에 대한 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

### <a name="azure-file-share-encryption-at-rest"></a>미사용 Azure 파일 공유 암호화
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>스토리지 계층
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>표준 파일 공유가 최대 100 TiB에 걸쳐 있도록 설정
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>국가별 가용성
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure 파일 동기화 지역 가용성
Azure File Sync은 다음 지역에서 제공 됩니다.

| Azure 클라우드 | 지리적 지역 | Azure 지역 | 지역 코드 |
|-------------|-------------------|--------------|-------------|
| 공용 | 아시아 | 동아시아 | `eastasia` |
| 공용 | 아시아 | 동남아시아 | `southeastasia` |
| 공용 | 오스트레일리아 | 오스트레일리아 동부 | `australiaeast` |
| 공용 | 오스트레일리아 | 오스트레일리아 남동부 | `australiasoutheast` |
| 공용 | 브라질 | 브라질 남부 | `brazilsouth` |
| 공용 | Canada | 캐나다 중부 | `canadacentral` |
| 공용 | Canada | 캐나다 동부 | `canadaeast` |
| 공용 | 유럽 | 북유럽 | `northeurope` |
| 공용 | 유럽 | 서유럽 | `westeurope` |
| 공용 | 프랑스 | 프랑스 중부 | `francecentral` |
| 공용 | 프랑스 | 프랑스 남부 * | `francesouth` |
| 공용 | 인도 | 인도 중부 | `centralindia` |
| 공용 | 인도 | 인도 남부 | `southindia` |
| 공용 | 일본 | 일본 동부 | `japaneast` |
| 공용 | 일본 | 일본 서부 | `japanwest` |
| 공용 | 한국 | 한국 중부 | `koreacentral` |
| 공용 | 한국 | 한국 남부 | `koreasouth` |
| 공용 | 남아프리카 | 남아프리카 북부 | `southafricanorth` |
| 공용 | 남아프리카 | 남아프리카 공화국 * | `southafricawest` |
| 공용 | 아랍에미리트 | 아랍에미리트 중부 * | `uaecentral` |
| 공용 | 아랍에미리트 | 아랍에미리트 북부 | `uaenorth` |
| 공용 | 영국 | 영국 남부 | `uksouth` |
| 공용 | 영국 | 영국 서부 | `ukwest` |
| 공용 | US | 미국 중부 | `centralus` |
| 공용 | US | 미국 동부 | `eastus` |
| 공용 | US | 미국 동부 2 | `eastus2` |
| 공용 | US | 미국 중북부 | `northcentralus` |
| 공용 | US | 미국 중남부 | `southcentralus` |
| 공용 | US | 미국 중서부 | `westcentralus` |
| 공용 | US | 미국 서부 | `westus` |
| 공용 | US | 미국 서부 2 | `westus2` |
| US Gov | US | US Gov 애리조나 | `usgovarizona` |
| US Gov | US | US Gov 텍사스 | `usgovtexas` |
| US Gov | US | US Gov 버지니아 | `usgovvirginia` |

Azure 파일 동기화에서는 Storage 동기화 서비스와 동일한 지역에 있는 Azure 파일 공유와의 동기화만 지원합니다.

별표로 표시 된 영역의 경우 해당 지역에서 Azure Storage에 대 한 액세스를 요청 하려면 Azure 지원에 문의 해야 합니다. 이 프로세스는 [이 문서](https://azure.microsoft.com/global-infrastructure/geographies/)에 설명 되어 있습니다.

## <a name="redundancy"></a>중복
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> 지역 중복 및 지역 중복 저장소에는 보조 지역으로 저장소를 수동으로 장애 조치 (failover) 할 수 있는 기능이 있습니다. 데이터 손실 가능성이 증가 하 여 Azure File Sync를 사용 하는 경우 재해 외부에서이 작업을 수행 하지 않는 것이 좋습니다. 저장소의 수동 장애 조치 (failover)를 시작 하려는 재해가 발생 한 경우 보조 끝점과의 동기화를 다시 시작 하 Azure File Sync 하려면 Microsoft에서 지원 사례를 열어야 합니다.

## <a name="migration"></a>마이그레이션
기존 Windows 파일 서버가 있는 경우 새 서버로 데이터를 이동할 필요 없이 직접 설치할 수 Azure File Sync. Azure File Sync 채택의 일부로 새 Windows 파일 서버로 마이그레이션하려는 경우 다음과 같은 여러 가지 방법으로 데이터를 이동할 수 있습니다.

- 이전 파일 공유 및 새 파일 공유에 대 한 서버 끝점을 만들고 Azure File Sync 서버 끝점 간에 데이터를 동기화 할 수 있습니다. 이 방법의 장점은 Azure File Sync 클라우드 계층화 인식 이므로 새 파일 서버에서 저장소를 매우 쉽게 oversubscribe 수 있다는 것입니다. 준비가 되 면 최종 사용자를 새 서버의 파일 공유로 잘라내거나 이전 파일 공유의 서버 끝점을 제거할 수 있습니다.

- 새 파일 서버에만 서버 끝점을 만들고 `robocopy`를 사용 하 여 이전 파일 공유에서로 데이터를 복사 합니다. 새 서버의 파일 공유에 대 한 토폴로지 (각 볼륨에 있는 공유의 수, 각 볼륨의 사용 가능한 시간 등)에 따라, 이전 서버에서 온-프레미스 데이터 센터 내에 있는 새 서버에 `robocopy` 하는 것이 Azure로 데이터를 이동 하는 Azure File Sync 것 보다 더 빠르게 완료 될 수 있으므로 추가 저장소를 임시로 프로 비전 해야 할 수 있습니다.

또한 Data Box를 사용 하 여 데이터를 Azure File Sync 배포로 마이그레이션할 수 있습니다. 대부분의 경우 고객은 데이터 수집을 위해 Data Box를 사용 하려는 경우 배포 속도가 증가 하거나 제한 된 대역폭 시나리오에 도움이 될 것 이라고 생각 하기 때문에 그렇게 합니다. Data Box를 사용 하 여 Azure File Sync 배포에 데이터를 수집 하면 대역폭 사용률이 낮아지고, 대부분의 시나리오에서 위에 설명 된 방법 중 하나를 통해 온라인 데이터 업로드를 진행 하는 것이 더 빠를 수 있습니다. Data Box를 사용 하 여 Azure File Sync 배포에 데이터를 수집 하는 방법에 대 한 자세한 내용은 [Azure Data Box를 사용 하 여 Azure File Sync으로 데이터 마이그레이션](storage-sync-offline-data-transfer.md)을 참조 하세요.

고객이 새 Azure File Sync 배포로 데이터를 마이그레이션할 때 일반적으로 수행 하는 실수는 Windows 파일 서버가 아닌 Azure 파일 공유에 직접 데이터를 복사 하는 것입니다. Azure File Sync는 Azure 파일 공유에서 모든 새 파일을 식별 하 고 Windows 파일 공유에 다시 동기화 할 수 있지만 일반적으로 Windows 파일 서버를 통해 데이터를 로드 하는 것 보다 훨씬 느립니다. AzCopy와 같은 많은 Azure 복사 도구에는 타임 스탬프 및 Acl과 같은 파일의 중요 한 메타 데이터를 모두 복사 하지 않는 추가 단점이 있습니다.

## <a name="antivirus"></a>바이러스 백신
바이러스 백신은 알려진 악성 코드 파일을 검색하는 방식으로 작동하기 때문에 바이러스 백신 제품은 계층화된 파일의 회수를 발생할 수 있습니다. Azure 파일 동기화 에이전트의 버전 4.0 이상에서 계층화된 파일에는 보안 Windows 특성 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS가 설정되어 있습니다. 이 특성이 설정된 파일 읽기를 건너뛰도록(대부분 자동으로 수행) 솔루션을 구성하는 방법을 소프트웨어 공급업체에 문의하세요. 

Microsoft의 사내 바이러스 백신 솔루션, Windows Defender 및 SCEP(System Center Endpoint Protection) 모두는 이 특성이 설정된 파일 읽기를 자동으로 건너뜁니다. 이러한 솔루션을 테스트하여 하나의 사소한 문제를 확인했습니다. 즉 기존 동기화 그룹에 서버를 추가하면 800바이트보다 작은 파일이 새 서버에서 회수(다운로드)됩니다. 이러한 파일은 새 서버에 남아 있지만 계층화 크기 요구 사항(64kb 초과)을 충족하지 않으므로 계층화되지 않습니다.

> [!Note]  
> 바이러스 백신 공급 업체는 Microsoft 다운로드 센터에서 다운로드할 수 있는 [Azure File Sync 바이러스 백신 호환성 테스트 도구 모음](https://www.microsoft.com/download/details.aspx?id=58322)을 사용 하 여 제품과 Azure File Sync 간의 호환성을 확인할 수 있습니다.

## <a name="backup"></a>Backup 
바이러스 백신 솔루션과 같은 백업 솔루션은 계층화된 파일이 회수되도록 할 수 있습니다. 온-프레미스 백업 제품을 사용하지 않고 클라우드 백업 솔루션을 사용하여 Azure 파일 공유를 백업하는 것이 좋습니다.

온-프레미스 백업 솔루션을 사용 하는 경우 클라우드 계층화를 사용 하지 않는 동기화 그룹의 서버에서 백업을 수행 해야 합니다. 복원을 수행할 때 볼륨 수준 또는 파일 수준 복원 옵션을 사용합니다. 파일 수준 복원 옵션을 사용하여 복원된 파일은 동기화 그룹의 모든 엔드포인트에 동기화되고 기존 파일은 백업에서 복원된 버전으로 대체됩니다.  볼륨 수준 복원은 Azure 파일 공유 또는 기타 서버 엔드포인트의 최신 파일 버전을 대체하지 않습니다.

> [!Note]  
> BMR(완전 복구) 복원은 예기치 않은 결과가 발생할 수 있으며 현재 지원되지 않습니다.

> [!Note]  
> Azure File Sync 에이전트 버전 9를 사용 하는 경우 클라우드 계층화를 사용 하는 볼륨에서 VSS 스냅숏 (이전 버전 탭 포함)이 지원 됩니다. 그러나 PowerShell을 통해 이전 버전 호환성을 사용 하도록 설정 해야 합니다. [방법을 알아보세요](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>다음 단계
* [방화벽 및 프록시 설정 고려](storage-sync-files-firewall-and-proxy.md)
* [Azure 파일 배포에 대한 계획](storage-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
* [Azure 파일 동기화 모니터링](storage-sync-files-monitoring.md)