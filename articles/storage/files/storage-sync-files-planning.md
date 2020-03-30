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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255120"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure 파일 동기화 배포에 대한 계획
[Azure 파일은](storage-files-introduction.md) 서버가 없는 Azure 파일 공유를 직접 탑재하거나 Azure File Sync를 사용하여 온-프레미스에 Azure 파일 공유를 캐싱하는 두 가지 주요 방법으로 배포할 수 있습니다. 어떤 배포 옵션을 선택하든 배포를 계획할 때 고려해야 할 사항을 변경합니다. 

- **Azure 파일 공유의 직접 마운트**: Azure Files는 SMB 액세스를 제공하기 때문에 Windows, macOS 및 Linux에서 사용할 수 있는 표준 SMB 클라이언트를 사용하여 온-프레미스 또는 클라우드에 Azure 파일 공유를 탑재할 수 있습니다. Azure 파일 공유는 서버가 없기 때문에 프로덕션 시나리오에 배포할 때 파일 서버 또는 NAS 장치를 관리할 필요가 없습니다. 즉, 소프트웨어 패치를 적용하거나 실제 디스크를 교체할 필요가 없습니다. 

- **Azure 파일 동기화를 사용하여 온-프레미스에 Azure 파일 공유 캐시**: Azure File Sync를 사용하면 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure 파일에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure File Sync는 온-프레미스(또는 클라우드) Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. 

이 문서에서는 주로 Azure File Sync 배포에 대한 배포 고려 사항을 다룹니다. 온-프레미스 또는 클라우드 클라이언트에서 Azure 파일 공유를 직접 탑재할 Azure 파일 공유 배포를 계획하려면 [Azure Files 배포 계획을](storage-files-planning.md)참조하십시오.

## <a name="management-concepts"></a>관리 개념
Azure 파일 동기화 배포에는 세 가지 기본 관리 개체가 있습니다.

- **Azure 파일 공유**: Azure 파일 공유는 Azure 파일 동기화 관계의 *클라우드 끝점을* 제공하는 서버없는 클라우드 파일 공유입니다. Azure 파일 공유의 파일은 SMB 또는 FileREST 프로토콜을 사용하여 직접 액세스할 수 있지만 Azure 파일 공유를 Azure 파일 동기화와 함께 사용할 때 주로 Windows Server 캐시를 통해 파일에 액세스하는 것이 좋습니다. 이는 현재 Azure 파일에 Windows Server와 같은 효율적인 변경 검색 메커니즘이 없기 때문에 Azure 파일 공유를 직접 변경하면 서버 끝점으로 다시 전파하는 데 시간이 걸리기 때문입니다.
- **서버 끝점:** Azure 파일 공유에 동기화되는 Windows 서버의 경로입니다. 볼륨의 특정 폴더 또는 볼륨의 루트일 수 있습니다. 네임스페이스가 겹치지 않는 경우 동일한 볼륨에 여러 서버 끝점이 존재할 수 있습니다.
- **동기화 그룹**: **클라우드 끝점**또는 Azure 파일 공유와 서버 끝점 간의 동기화 관계를 정의하는 개체입니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 예를 들어 Azure File Sync를 사용하여 관리하려는 두 개의 고유한 파일 집합이 있는 경우 두 개의 동기화 그룹을 만들고 각 동기화 그룹에 다른 끝점을 추가합니다.

### <a name="azure-file-share-management-concepts"></a>Azure 파일 공유 관리 개념
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure 파일 동기화 관리 개념
동기화 그룹은 Azure File Sync에 사용할 서버를 등록하고 동기화 그룹 관계를 포함하는 최상위 개체인 **Storage Sync Services에**배포됩니다. Storage 동기화 서비스 리소스는 스토리지 계정 리소스의 피어로, Azure 리소스 그룹에 쉽게 배포할 수 있습니다. 저장소 동기화 서비스는 여러 저장소 계정과 등록된 여러 Windows 서버에서 Azure 파일 공유를 포함하는 동기화 그룹을 만들 수 있습니다.

저장소 동기화 서비스에서 동기화 그룹을 만들려면 먼저 Windows 서버를 저장소 동기화 서비스에 등록해야 합니다. 이렇게 하면 **등록된 서버** 개체가 생성되며, 이는 서버 또는 클러스터와 저장소 동기화 서비스 간의 트러스트 관계를 나타냅니다. 저장소 동기화 서비스를 등록하려면 먼저 서버에 Azure 파일 동기화 에이전트를 설치해야 합니다. 개별 서버 또는 클러스터는 한 번에 하나의 저장소 동기화 서비스로만 등록할 수 있습니다.

동기화 그룹에는 하나의 클라우드 끝점 또는 Azure 파일 공유와 하나 이상의 서버 끝점이 포함됩니다. 서버 끝점 개체에는 Azure File Sync의 캐싱 기능을 제공하는 **클라우드 계층화** 기능을 구성하는 설정이 포함되어 있습니다. Azure 파일 공유와 동기화하려면 Azure 파일 공유를 포함하는 저장소 계정이 저장소 동기화 서비스와 동일한 Azure 지역에 있어야 합니다.

### <a name="management-guidance"></a>경영 지침
Azure 파일 동기화를 배포할 때 다음을 권장합니다.

- Azure 파일 배포는 Windows 파일 공유와 함께 1:1을 공유합니다. 서버 끝점 개체는 동기화 관계의 서버 쪽에서 동기화 토폴로지를 설정하는 방법에 대한 유연성을 크게 제공합니다. 관리를 단순화하려면 서버 끝점의 경로를 Windows 파일 공유 경로와 일치시도록 합니다. 

- 가능한 한 적은 수의 저장소 동기화 서비스를 사용하십시오. 이렇게 하면 Windows 서버가 한 번에 하나의 저장소 동기화 서비스에만 등록할 수 있으므로 여러 서버 끝점을 포함하는 동기화 그룹이 있는 경우 관리가 간소화됩니다. 

- Azure 파일 공유를 배포할 때 저장소 계정의 IOPS 제한에 주의합니다. 이상적으로는 저장소 계정으로 파일 공유를 1:1로 매핑하는 것이 이상적이지만 조직과 Azure의 다양한 제한 및 제한으로 인해 항상 가능하지않을 수 있습니다. 하나의 저장소 계정에 하나의 파일 공유만 배포할 수 없는 경우 가장 인기 있는 파일 공유가 동일한 저장소 계정에 함께 배치되지 않도록 활성 상태가 매우 높은 공유와 덜 활성화된 공유를 고려합니다.

## <a name="windows-file-server-considerations"></a>Windows 파일 서버 고려 사항
Windows Server에서 동기화 기능을 사용하려면 Azure 파일 동기화 다운로드 가능한 에이전트를 설치해야 합니다. Azure File Sync 에이전트는 서버 `FileSyncSvc.exe`끝점의 변경 내용을 모니터링하고 동기화 세션을 시작할 수 있는 백그라운드 Windows `StorageSync.sys`서비스 및 클라우드 계층화 및 빠른 재해 복구를 가능하게 하는 파일 시스템 필터의 두 가지 주요 구성 요소를 제공합니다.  

### <a name="operating-system-requirements"></a>운영 체제 요구 사항
Azure 파일 동기화는 다음 버전의 Windows 서버에서 지원됩니다.

| 버전 | 지원되는 SKU | 지원되는 배포 옵션 |
|---------|----------------|------------------------------|
| Windows Server 2019 | 데이터 센터, 표준 및 IoT | 전체 및 코어 |
| Windows Server 2016 | 데이터 센터, 표준 및 스토리지 서버 | 전체 및 코어 |
| Windows Server 2012 R2 | 데이터 센터, 표준 및 스토리지 서버 | 전체 및 코어 |

이후 버전의 Windows Server는 출시되면 추가될 예정입니다.

> [!Important]  
> Windows 업데이트의 최신 업데이트를 사용하여 Azure 파일 동기화와 함께 사용되는 모든 서버를 최신 상태로 유지하는 것이 좋습니다. 

### <a name="minimum-system-resources"></a>최소 시스템 리소스
Azure File Sync에는 CPU가 하나 이상 있고 최소 2GiB의 메모리가 있는 서버가 물리적 또는 가상 서버가 필요합니다.

> [!Important]  
> 동적 메모리가 활성화된 가상 컴퓨터에서 서버가 실행 중인 경우 VM은 최소 2048MiB메모리로 구성해야 합니다.

대부분의 프로덕션 워크로드의 경우 최소 요구 사항만 있는 Azure File Sync 동기화 서버를 구성하는 것은 권장되지 않습니다. 자세한 내용은 [권장 시스템 리소스를](#recommended-system-resources) 참조하십시오.

### <a name="recommended-system-resources"></a>권장 시스템 리소스
모든 서버 기능 이나 응용 프로그램 처럼 Azure 파일 동기화에 대 한 시스템 리소스 요구 사항은 배포의 규모에 따라 결정 됩니다. 서버에서 대규모 배포를 하려면 더 큰 시스템 리소스가 필요합니다. Azure File Sync의 경우 배율은 서버 끝점의 개체 수와 데이터 집합의 변동에 따라 결정됩니다. 단일 서버에는 여러 동기화 그룹에 서버 끝점이 있을 수 있으며 다음 표에 나열된 개체 수는 서버가 연결된 전체 네임스페이스를 차지합니다. 

예를 들어 1,000만 개의 개체가 있는 서버 끝점 A + 1,000만 개의 개체가 있는 서버 끝점 B = 2,000만 개의 개체가 있는 경우입니다. 이 예제 배포의 경우 8개의 CPU, 정상 상태에 대한 메모리 16GiB 및 초기 마이그레이션을 위한 메모리(가능한 경우)의 48GiB를 권장합니다.
 
네임스페이스 데이터는 성능상의 이유로 메모리에 저장됩니다. 따라서 네임스페이스가 클수록 성능이 향상되려면 더 많은 메모리가 필요하고 변동이 많을수록 처리하려면 더 많은 CPU가 필요합니다. 
 
다음 표에서는 네임스페이스의 크기와 평균 파일 크기가 512 KiB인 일반적인 범용 파일 공유용량으로변환을 모두 제공했습니다. 파일 크기가 작으면 동일한 용량에 대해 메모리를 추가하는 것이 좋습니다. 네임스페이스 크기에 따라 메모리 구성을 기반으로 합니다.

| 네임스페이스 크기 - 파일 & 디렉토리(수백만 개)  | 일반 용량(TiB)  | CPU 코어 수  | 권장 메모리(GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8(초기 동기화)/ 2(일반적인 이탈)      |
| 5        | 2.3     | 2        | 16(초기 동기화)/ 4(일반적인 이탈)    |
| 10       | 4.7     | 4        | 32(초기 동기화)/ 8(일반적인 이탈)   |
| 30       | 14.0    | 8        | 48(초기 동기화)/ 16(일반적인 이탈)   |
| 50       | 23.3    | 16       | 64(초기 동기화)/ 32(일반적인 이탈)  |
| 100*     | 46.6    | 32       | 128(초기 동기화)/ 32(일반적인 이탈)  |

\*디렉터리& 1억 개 이상의 파일을 동기화하는 것은 현재 권장되지 않습니다. 이는 테스트된 임계값에 따라 소프트 제한입니다. 자세한 내용은 [Azure 파일 확장성 및 성능 목표를](storage-files-scale-targets.md#azure-file-sync-scale-targets)참조하십시오.

> [!TIP]
> 네임스페이스의 초기 동기화는 집중적인 작업이며 초기 동기화가 완료될 때까지 더 많은 메모리를 할당하는 것이 좋습니다. 이 필수는 아니지만 초기 동기화 속도가 빨라질 수 있습니다. 
> 
> 일반적인 변동은 하루에 변경되는 네임스페이스의 0.5%입니다. 이탈 수준이 높은 경우 CPU를 더 추가하는 것이 좋습니다. 

- NTFS 파일 시스템으로 포맷되어 로컬로 연결된 볼륨

### <a name="evaluation-cmdlet"></a>평가 cmdlet
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 cmdlet을 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 cmdlet은 지원되지 않는 문자 또는 지원되지 않는 운영 체제 버전과 같은 파일 시스템 및 데이터 집합의 잠재적인 문제를 확인합니다. 그 검사는 아래에 언급 된 기능의 대부분을 커버하지만, 모든; 배포가 원활하게 진행되도록 이 섹션의 나머지 부분을 주의 깊게 읽는 것이 좋습니다. 

평가 cmdlet은 Az PowerShell 모듈을 설치하여 설치할 수 있으며, 여기에 있는 [지침에](https://docs.microsoft.com/powershell/azure/install-Az-ps)따라 설치할 수 있습니다.

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
Azure 파일 동기화는 직접 연결된 NTFS 볼륨에서만 지원됩니다. Windows Server에 직접 연결된 저장소 또는 DAS는 Windows Server 운영 체제가 파일 시스템을 소유하고 있음을 의미합니다. DAS는 파일 서버에 디스크를 물리적으로 연결하거나, 가상 디스크를 파일 서버 VM(예: Hyper-V에서 호스팅하는 VM)에 연결하거나 ISCSI를 통해 제공할 수 있습니다.

NTFS 볼륨만 지원됩니다. ReFS, FAT, FAT32 및 기타 파일 시스템은 지원되지 않습니다.

다음 표에서는 NTFS 파일 시스템 피처의 중간 상태를 보여 주십습니다. 

| 기능 | 상태 지원 | 메모 |
|---------|----------------|-------|
| ACL(액세스 제어 목록) | 완벽하게 지원 | Windows 스타일의 임의 액세스 제어 목록은 Azure 파일 동기화에 의해 유지되며 서버 끝점에 있는 Windows Server에서 적용됩니다. Azure 파일 공유를 직접 탑재할 때 ACL을 적용할 수도 있지만 추가 구성이 필요합니다. 자세한 내용은 [ID 섹션을](#identity) 참조하십시오. |
| 하드 링크 | 건너뜀 | |
| 바로 가기 링크 | 건너뜀 | |
| 탑재 지점 | 부분적으로 지원됨 | 탑재 지점은 서버 엔드포인트의 루트일 수 있지만, 서버 엔드포인트의 네임스페이스에 포함된 경우 건너뜁니다. |
| 분기 동기화 | 건너뜀 | 분산 파일 시스템 DfrsrPrivate 및 DFSRoots 폴더를 예로 들 수 있습니다. |
| 재분석 지점 | 건너뜀 | |
| NTFS 압축 | 완벽하게 지원 | |
| 스파스 파일 | 완벽하게 지원 | 스파스 파일은 동기화되지만(차단되지 않음) 전체 파일로 클라우드와 동기화됩니다. 클라우드(또는 다른 서버)에서 파일 콘텐츠가 변경될 경우 변경 내용이 다운로드되면 파일은 더 이상 스파스 파일이 아닙니다. |
| ADS(대체 데이터 스트림) | 보존되지만 동기화되지 않음 | 예를 들어, 파일 분류 인프라에서 만들어진 분류 태그는 동기화되지 않습니다. 각 서버 엔드포인트의 파일에 대한 기존 분류 태그는 그대로 유지됩니다. |

<a id="files-skipped"></a>Azure 파일 동기화는 특정 임시 파일 및 시스템 폴더도 건너뜁니다.

| 파일/폴더 | 참고 |
|-|-|
| pagefile.sys | 시스템에 특정된 파일 |
| Desktop.ini | 시스템에 특정된 파일 |
| thumbs.db | 썸네일의 임시 파일 |
| ehthumbs.db | 미디어 썸네일용 임시 파일 |
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
**윈도우 서버 2016 및 윈도우 서버 2019**   
Windows Server 2016 및 Windows Server 2019에서 클라우드 계층화를 사용하면 볼륨의 데이터 중복 제거가 지원됩니다. 클라우드 계층화를 사용하도록 설정한 볼륨에서 데이터 중복 제거를 사용하도록 설정하면 더 많은 저장소를 프로비저닝하지 않고도 온-프레미스에서 더 많은 파일을 캐시할 수 있습니다. 

클라우드 계층화를 사용하도록 설정한 볼륨에서 데이터 중복 제거가 활성화되면 서버 끝점 위치 내의 중복 제거 최적화된 파일은 클라우드 계층화 정책 설정에 따라 일반 파일과 유사하게 계층화됩니다. 중복 제거 최적화 된 파일이 계층화되면 데이터 중복 제거 가비지 수집 작업이 자동으로 실행되어 볼륨의 다른 파일에서 더 이상 참조되지 않는 불필요한 청크를 제거하여 디스크 공간을 회수합니다.

볼륨 절감은 서버에만 적용됩니다. Azure 파일 공유의 데이터는 제거되지 않습니다.

> [!Note]  
> Windows Server 2019에서 클라우드 계층화를 사용하도록 설정한 볼륨에서 데이터 중복 제거를 지원하려면 Windows 업데이트 [KB4520062를](https://support.microsoft.com/help/4520062) 설치해야 하며 Azure 파일 동기화 에이전트 버전 9.0.0.0 이상이 필요합니다.

**윈도우 서버 2012 R2**  
Azure 파일 동기화는 Windows Server 2012 R2에서 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원하지 않습니다. 볼륨에서 데이터 중복 제거를 사용하도록 설정한 경우 클라우드 계층화를 사용하지 않도록 설정해야 합니다. 

**참고**
- Azure File Sync 에이전트를 설치하기 전에 데이터 중복 제거가 설치된 경우 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원하려면 다시 시작해야 합니다.
- 클라우드 계층화를 사용하도록 설정한 후 볼륨에서 데이터 중복 제거가 활성화된 경우 초기 중복 제거 최적화 작업은 아직 계층화되지 않은 볼륨에서 파일을 최적화하고 클라우드 계층화에 다음과 같은 영향을 미칩니다.
    - 사용 가능한 공간 정책은 히트맵을 사용하여 볼륨의 사용 가능한 공간에 따라 파일을 계층화합니다.
    - 날짜 정책은 파일에 액세스하는 중복 제거 최적화 작업으로 인해 계층화할 수 있는 파일의 계층화를 건너뜁니다.
- 진행 중인 중복 제거 최적화 작업의 경우 파일이 아직 계층화되지 않은 경우 날짜 정책이 있는 클라우드 계층화가 데이터 중복 [제거 MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) 설정으로 지연됩니다. 
    - 예: MinimumFileAgeDays 설정이 7일이고 클라우드 계층화 날짜 정책이 30일인 경우 날짜 정책은 37일 후에 파일을 계층화합니다.
    - 참고: Azure 파일 동기화에 의해 파일 계층화가 완료되면 중복 제거 최적화 작업이 파일을 건너뜁니다.
- 설치된 Azure File Sync 에이전트를 사용하여 Windows Server 2012 R2를 실행하는 서버가 Windows Server 2016 또는 Windows Server 2019로 업그레이드된 경우 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원하려면 다음 단계를 수행해야 합니다.  
    - Windows Server 2012 R2용 Azure 파일 동기화 에이전트를 제거하고 서버를 다시 시작합니다.
    - 새 서버 운영 체제 버전(Windows Server 2016 또는 Windows Server 2019)에 대한 Azure 파일 동기화 에이전트를 다운로드합니다.
    - Azure 파일 동기화 에이전트를 설치하고 서버를 다시 시작합니다.  
    
    참고: 에이전트를 제거하고 다시 설치할 때 서버의 Azure 파일 동기화 구성 설정이 유지됩니다.

### <a name="distributed-file-system-dfs"></a>분산 파일 시스템(DFS)
Azure 파일 동기화에서는 DFS-N(DFS 네임스페이스) 및 DFS-R(DFS 복제)과의 상호 작용을 지원합니다.

**DFS 네임 스페이스(DFS-N)**: DFS-N 서버에서 Azure 파일 동기화가 완전히 지원됩니다. 하나 이상의 DFS-N 멤버에 Azure 파일 동기화 에이전트를 설치하면 서버 엔드포인트 및 클라우드 엔드포인트 간에 데이터를 동기화할 수 있습니다. 자세한 내용은 [DFS 네임스페이스 개요](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)를 참조하세요.
 
**DFS 복제(DFS-R)**: DFS-R 및 Azure 파일 동기화는 모두 복제 솔루션이므로 대부분의 경우 DFS-R을 Azure 파일 동기화로 대체하는 것이 좋습니다. 그러나 DFS-R 및 Azure 파일 동기화를 함께 사용하려는 몇 가지 시나리오가 있습니다.

- DFS-R 배포에서 Azure 파일 동기화 배포로 마이그레이션하는 중입니다. 자세한 내용은 [DFS 복제(DFS-R) 배포를 Azure 파일 동기화로 마이그레이션](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)을 참조하세요.
- 파일 데이터의 복사본이 필요한 모든 온-프레미스 서버를 인터넷에 직접 연결할 수 있는 것은 아닙니다.
- 분기 서버는 Azure 파일 동기화를 사용할 단일 허브 서버에 데이터를 통합합니다.

Azure 파일 동기화 및 DFS-R이 나란히 작동하려면 다음을 수행하십시오.

1. Azure 파일 동기화 클라우드 계층화는 DFS-R 복제 폴더를 포함하는 볼륨에서 사용하지 않도록 설정해야 합니다.
2. 서버 엔드포인트는 DFS-R 읽기 전용 복제 폴더에 대해 구성할 수 없습니다.

자세한 내용은 [DFS 복제 개요](https://technet.microsoft.com/library/jj127250)를 참조하세요.

### <a name="sysprep"></a>Sysprep
Azure File Sync 에이전트가 설치된 서버에서 sysprep을 사용하면 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 에이전트 설치 및 서버 등록은 서버 이미지 배포 및 sysprep 최소 설치 완료 후 발생해야 합니다.

### <a name="windows-search"></a>Windows 검색
클라우드 계층화가 서버 엔드포인트에서 활성화되면 계층화된 파일은 건너뛰고 Windows 검색을 통해 인덱싱되지 않습니다. 계층화되지 않은 파일은 제대로 인덱싱됩니다.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>다른 HSM(계층적 스토리지 관리) 솔루션
다른 HSM 솔루션은 Azure 파일 동기화와 함께 사용하면 안 됩니다.

## <a name="identity"></a>Identity
Azure File Sync는 동기화 설정 외에 특별한 설정 없이 표준 AD 기반 ID와 함께 작동합니다. Azure File Sync를 사용하는 경우 대부분의 액세스가 Azure 파일 공유를 통하지 않고 Azure 파일 동기화 캐싱 서버를 통과해야 합니다. 서버 끝점은 Windows Server에 있고 Windows Server는 오랫동안 AD 및 Windows 스타일 ACL을 지원했기 때문에 저장소 동기화 서비스에 등록된 Windows 파일 서버가 도메인에 가입되도록 하는 것 외에는 아무것도 필요하지 않습니다. Azure 파일 동기화는 Azure 파일 공유의 파일에 ACL을 저장하고 모든 서버 끝점에 복제합니다.

Azure 파일 공유에 직접 변경하면 동기화 그룹의 서버 끝점에 동기화하는 데 시간이 더 오래 걸리지만 클라우드에서 직접 파일 공유에 대한 AD 권한을 적용할 수도 있습니다. 이렇게 하려면 Windows 파일 서버가 도메인에 가입되는 방식과 마찬가지로 저장소 계정을 온-프레미스 AD에 도메인으로 가입해야 합니다. 고객 소유 Active Directory에 저장소 계정을 조인하는 도메인에 대해 자세히 알아보려면 [Azure Files Active Directory 개요를](storage-files-active-directory-overview.md)참조하십시오.

> [!Important]  
> 저장소 계정을 Active Directory에 조인하는 도메인은 Azure File Sync를 성공적으로 배포할 필요가 없습니다. 이는 사용자가 Azure 파일 공유를 직접 탑재할 때 Azure 파일 공유가 온-프레미스 ACL을 적용할 수 있도록 하는 엄격하게 선택 사항 단계입니다.

## <a name="networking"></a>네트워킹
Azure 파일 동기화 에이전트는 Azure 파일 동기화 REST 프로토콜 및 FileREST 프로토콜을 사용하여 저장소 동기화 서비스 및 Azure 파일 공유와 통신하며, 이 두 프로토콜은 항상 포트 443을 통해 HTTPS를 사용합니다. SMB는 Windows 서버와 Azure 파일 공유 간에 데이터를 업로드하거나 다운로드하는 데 사용되지 않습니다. 대부분의 조직에서는 대부분의 웹 사이트를 방문하기 위한 요구 사항으로 포트 443을 통해 HTTPS 트래픽을 허용하므로 Azure File Sync를 배포하기 위해 특수 네트워킹 구성이 필요하지 않습니다.

조직의 정책 또는 고유한 규정 요구 사항에 따라 Azure와의 보다 제한적인 통신이 필요할 수 있으므로 Azure File Sync는 네트워킹을 구성하는 데 몇 가지 메커니즘을 제공합니다. 요구 사항에 따라 다음을 수행할 수 있습니다.

- ExpressRoute 또는 Azure VPN을 통해 터널 동기화 및 파일 업로드/다운로드 트래픽. 
- 서비스 끝점 및 개인 끝점과 같은 Azure 파일 및 Azure 네트워킹 기능을 사용합니다.
- 사용자 환경에서 프록시를 지원하도록 Azure 파일 동기화를 구성합니다.
- Azure 파일 동기화에서 네트워크 활동을 제한합니다.

Azure File Sync의 네트워킹 기능 구성에 대해 자세히 알아보려면 다음을 참조하십시오.
- [Azure 파일 동기화 프록시 및 방화벽 설정](storage-sync-files-firewall-and-proxy.md)
- [Azure 파일 동기화가 데이터 센터의 좋은 이웃이 되도록 보장](storage-sync-files-server-registration.md)

## <a name="encryption"></a>암호화
Azure File Sync를 사용하는 경우 고려해야 할 세 가지 암호화 계층이 있습니다: Windows Server의 미사용 저장소에 대한 암호화, Azure File Sync 에이전트와 Azure 간에 전송 중인 암호화, Azure 파일 공유의 나머지 데이터 에서 암호화. 

### <a name="windows-server-encryption-at-rest"></a>미사용 윈도우 서버 암호화 
Azure File Sync에서 일반적으로 작동하는 Windows Server의 데이터를 암호화하기 위한 두 가지 전략이 있습니다. 이러한 메서드는 상호 배타적이지 않습니다. 암호화의 목적이 다르기 때문에 원하는 경우 함께 사용할 수 있습니다.

파일 시스템 아래에 암호화를 제공하기 위해 Windows Server는 BitLocker 받은 편지함을 제공합니다. BitLocker는 Azure 파일 동기화에 완전히 투명합니다. BitLocker와 같은 암호화 메커니즘을 사용하는 주된 이유는 디스크를 훔치는 사람이 온-프레미스 데이터 센터에서 데이터를 물리적으로 유출하지 못하도록 하고 승인되지 않은 OS를 사이드로딩하여 데이터에 대한 무단 읽기/쓰기를 수행하지 못하도록 하기 위해서입니다. BitLocker에 대한 자세한 내용은 [BitLocker 개요를](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)참조하십시오.

BitLocker와 유사하게 작동하는 타사 제품은 NTFS 볼륨 아래에 있다는 점에서 Azure File Sync를 사용하여 완전히 투명하게 작동해야 합니다. 

데이터를 암호화하는 다른 주요 방법은 응용 프로그램이 파일을 저장할 때 파일의 데이터 스트림을 암호화하는 것입니다. 일부 응용 프로그램은 기본적으로 이 작업을 수행할 수 있지만 일반적으로 그렇지 않습니다. 파일의 데이터 스트림을 암호화하는 방법의 예로는 Azure 정보 보호(AIP) /Azure 권한 관리 서비스(Azure RMS) /활성 디렉터리 RMS가 있습니다. AIP/RMS와 같은 암호화 메커니즘을 사용하는 주된 이유는 플래시 드라이브와 같이 다른 위치로 복사하거나 권한이 없는 사람에게 이메일을 보내파일 공유에서 데이터가 유출되는 것을 방지하기 위해서입니다. 파일의 데이터 스트림이 파일 형식의 일부로 암호화되면 이 파일은 Azure 파일 공유에서 계속 암호화됩니다. 

Azure 파일 동기화는 NTFS 암호화 된 파일 시스템 (NTFS EFS) 또는 파일 시스템 위에 있지만 파일의 데이터 스트림 아래에 있는 타사 암호화 솔루션과 상호 운용되지 않습니다. 

### <a name="encryption-in-transit"></a>전송 중 암호화
Azure File Sync 에이전트는 Azure 파일 동기화 REST 프로토콜 및 FileREST 프로토콜을 사용하여 저장소 동기화 서비스 및 Azure 파일 공유와 통신하며, 이 두 프로토콜은 항상 포트 443을 통해 HTTPS를 사용합니다. Azure 파일 동기화HTTP를 통해 암호화되지 않은 요청을 보내지 않습니다. 

Azure 저장소 계정에는 기본적으로 활성화된 전송 중인 암호화를 요구하는 스위치가 포함되어 있습니다. 저장소 계정 수준에서 전환이 비활성화되어 Azure 파일 공유에 대한 암호화되지 않은 연결이 가능하더라도 Azure File Sync는 암호화된 채널만 사용하여 파일 공유에 액세스할 수 있습니다.

저장소 계정에 대해 전송 중인 암호화를 사용하지 않도록 설정하는 주된 이유는 Windows Server 2008 R2 또는 이전 Linux 배포판과 같은 이전 운영 체제에서 실행되어야 하는 레거시 응용 프로그램을 지원하여 Azure 파일 공유와 직접 대화하는 것입니다. 레거시 응용 프로그램이 파일 공유의 Windows Server 캐시와 대화하는 경우 이 설정을 전환해도 아무런 효과가 없습니다. 

전송 중 데이터의 암호화를 사용하도록 설정하는 것이 좋습니다.

전송 중 암호화에 대한 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

### <a name="azure-file-share-encryption-at-rest"></a>미사용 Azure 파일 공유 암호화
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>스토리지 계층
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>표준 파일 공유를 최대 100TiB까지 사용할 수 있습니다.
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>국가별 가용성
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure 파일 동기화 지역 가용성
Azure 파일 동기화는 다음 지역에서 사용할 수 있습니다.

| Azure 클라우드 | 지역 | Azure 지역 | 지역 코드 |
|-------------|-------------------|--------------|-------------|
| Public | 아시아 | 동아시아 | `eastasia` |
| Public | 아시아 | 동남아시아 | `southeastasia` |
| Public | 오스트레일리아 | 오스트레일리아 동부 | `australiaeast` |
| Public | 오스트레일리아 | 오스트레일리아 남동부 | `australiasoutheast` |
| Public | 브라질 | 브라질 남부 | `brazilsouth` |
| Public | Canada | 캐나다 중부 | `canadacentral` |
| Public | Canada | 캐나다 동부 | `canadaeast` |
| Public | 유럽 | 북유럽 | `northeurope` |
| Public | 유럽 | 서유럽 | `westeurope` |
| Public | 프랑스 | 프랑스 중부 | `francecentral` |
| Public | 프랑스 | 프랑스 남부* | `francesouth` |
| Public | 인도 | 인도 중부 | `centralindia` |
| Public | 인도 | 인도 남부 | `southindia` |
| Public | 일본 | 일본 동부 | `japaneast` |
| Public | 일본 | 일본 서부 | `japanwest` |
| Public | 한국 | 한국 중부 | `koreacentral` |
| Public | 한국 | 한국 남부 | `koreasouth` |
| Public | 남아프리카 | 남아프리카 북부 | `southafricanorth` |
| Public | 남아프리카 | 남아프리카 공화국 서부* | `southafricawest` |
| Public | 아랍에미리트 | 아랍에미리트 중부* | `uaecentral` |
| Public | 아랍에미리트 | 아랍에미리트 북부 | `uaenorth` |
| Public | 영국 | 영국 남부 | `uksouth` |
| Public | 영국 | 영국 서부 | `ukwest` |
| Public | US | 미국 중부 | `centralus` |
| Public | US | 미국 동부 | `eastus` |
| Public | US | 미국 동부 2 | `eastus2` |
| Public | US | 미국 중북부 | `northcentralus` |
| Public | US | 미국 중남부 | `southcentralus` |
| Public | US | 미국 중서부 | `westcentralus` |
| Public | US | 미국 서부 | `westus` |
| Public | US | 미국 서부 2 | `westus2` |
| US Gov | US | US Gov 애리조나 | `usgovarizona` |
| US Gov | US | US Gov 텍사스 | `usgovtexas` |
| US Gov | US | US Gov 버지니아 | `usgovvirginia` |

Azure 파일 동기화에서는 Storage 동기화 서비스와 동일한 지역에 있는 Azure 파일 공유와의 동기화만 지원합니다.

별표로 표시된 리전의 경우 Azure 지원에 문의하여 해당 리전의 Azure 저장소에 대한 액세스를 요청해야 합니다. 프로세스는 이 [문서에](https://azure.microsoft.com/global-infrastructure/geographies/)설명되어 있습니다.

## <a name="redundancy"></a>중복
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> 지리적 중복 및 지리적 영역 중복 저장소에는 보조 영역으로 의 저장소를 수동으로 장애 조치(failover)하는 기능이 있습니다. Azure File Sync를 사용할 때는 데이터 손실 가능성이 증가하므로 재해 이외에는 이 작업을 수행하는 것이 좋습니다. 저장소의 수동 장애 조치(failover)를 시작하려는 재해가 발생하면 Microsoft에서 지원 사례를 열어 Azure File Sync를 사용하여 보조 끝점과의 동기화를 다시 시작해야 합니다.

## <a name="migration"></a>마이그레이션
기존 Windows 파일 서버가 있는 경우 데이터를 새 서버로 이동할 필요 없이 Azure File Sync를 직접 설치할 수 있습니다. Azure File Sync를 채택하는 일환으로 새 Windows 파일 서버로 마이그레이션하려는 경우 데이터를 이전하는 몇 가지 방법이 있습니다.

- 이전 파일 공유 및 새 파일 공유에 대한 서버 끝점을 만들고 Azure File Sync가 서버 끝점 간에 데이터를 동기화하도록 합니다. 이 방법의 장점은 Azure File Sync가 클라우드 계층화를 인식하므로 새 파일 서버의 저장소를 과다 구독하기가 매우 쉽다는 것입니다. 준비가 되면 최종 사용자를 새 서버의 파일 공유로 잘라내고 이전 파일 공유의 서버 끝점을 제거할 수 있습니다.

- 새 파일 서버에서만 서버 끝점을 만들고 을 사용하여 `robocopy`이전 파일 공유에서 데이터를 복사합니다. 새 서버의 파일 공유 토폴로지(각 볼륨에 있는 공유 수, 각 볼륨의 사용 가능 시간 등)에 따라 이전 `robocopy` 서버에서 온-프레미스 데이터 센터 내의 새 서버로 데이터를 Azure로 이동하는 것보다 더 빠르게 완료될 것으로 예상되는 경우 추가 저장소를 일시적으로 프로비저닝해야 할 수 있습니다.

데이터 상자를 사용하여 Azure 파일 동기화 배포로 데이터를 마이그레이션할 수도 있습니다. 대부분의 경우 고객이 데이터 상자를 사용하여 데이터를 수집하려는 경우 배포 속도가 빨라지거나 제한된 대역폭 시나리오에 도움이 될 수 있다고 생각하기 때문에 데이터 상자를 사용하려고 합니다. 데이터 상자를 사용하여 Azure File Sync 배포에 데이터를 수집하면 대역폭 사용률이 감소하지만 대부분의 시나리오에서는 위에서 설명한 방법 중 하나를 통해 온라인 데이터 업로드를 더 빠르게 진행할 수 있습니다. 데이터 상자를 사용하여 Azure File Sync 배포에 데이터를 인더링하는 방법에 대해 자세히 알아보려면 Azure 데이터 [상자를 사용하여 Azure 파일 동기화로 데이터를 마이그레이션합니다.](storage-sync-offline-data-transfer.md)

새 Azure File Sync 배포로 데이터를 마이그레이션할 때 고객이 일반적으로 실수하는 것은 Windows 파일 서버가 아닌 Azure 파일 공유에 직접 데이터를 복사하는 것입니다. Azure File Sync는 Azure 파일 공유의 모든 새 파일을 식별하고 Windows 파일 공유에 다시 동기화하지만 일반적으로 Windows 파일 서버를 통해 데이터를 로드하는 것보다 훨씬 느립니다. AzCopy와 같은 많은 Azure 복사 도구에는 타임스탬프 및 ACL과 같은 파일의 중요한 메타데이터를 모두 복사하지 않는 추가 단점이 있습니다.

## <a name="antivirus"></a>바이러스 백신
바이러스 백신은 알려진 악성 코드 파일을 검색하는 방식으로 작동하기 때문에 바이러스 백신 제품은 계층화된 파일의 회수를 발생할 수 있습니다. Azure 파일 동기화 에이전트의 버전 4.0 이상에서 계층화된 파일에는 보안 Windows 특성 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS가 설정되어 있습니다. 이 특성이 설정된 파일 읽기를 건너뛰도록(대부분 자동으로 수행) 솔루션을 구성하는 방법을 소프트웨어 공급업체에 문의하세요. 

Microsoft의 사내 바이러스 백신 솔루션, Windows Defender 및 SCEP(System Center Endpoint Protection) 모두는 이 특성이 설정된 파일 읽기를 자동으로 건너뜁니다. 이러한 솔루션을 테스트하여 하나의 사소한 문제를 확인했습니다. 즉 기존 동기화 그룹에 서버를 추가하면 800바이트보다 작은 파일이 새 서버에서 회수(다운로드)됩니다. 이러한 파일은 새 서버에 남아 있지만 계층화 크기 요구 사항(64kb 초과)을 충족하지 않으므로 계층화되지 않습니다.

> [!Note]  
> 바이러스 백신 공급업체는 Microsoft 다운로드 센터에서 다운로드할 수 있는 [Azure 파일 동기화 바이러스 백신 호환성 테스트 도구 모음을](https://www.microsoft.com/download/details.aspx?id=58322)사용하여 제품과 Azure 파일 동기화 간의 호환성을 확인할 수 있습니다.

## <a name="backup"></a>Backup 
바이러스 백신 솔루션과 같은 백업 솔루션은 계층화된 파일이 회수되도록 할 수 있습니다. 온-프레미스 백업 제품을 사용하지 않고 클라우드 백업 솔루션을 사용하여 Azure 파일 공유를 백업하는 것이 좋습니다.

온-프레미스 백업 솔루션을 사용하는 경우 클라우드 계층화가 비활성화된 동기화 그룹의 서버에서 백업을 수행해야 합니다. 복원을 수행할 때 볼륨 수준 또는 파일 수준 복원 옵션을 사용합니다. 파일 수준 복원 옵션을 사용하여 복원된 파일은 동기화 그룹의 모든 엔드포인트에 동기화되고 기존 파일은 백업에서 복원된 버전으로 대체됩니다.  볼륨 수준 복원은 Azure 파일 공유 또는 기타 서버 엔드포인트의 최신 파일 버전을 대체하지 않습니다.

> [!Note]  
> BMR(완전 복구) 복원은 예기치 않은 결과가 발생할 수 있으며 현재 지원되지 않습니다.

> [!Note]  
> Azure File Sync 에이전트의 버전 9를 사용하면 클라우드 계층화가 활성화된 볼륨에서 VSS 스냅숏(이전 버전 탭 포함)이 지원됩니다. 그러나 PowerShell을 통해 이전 버전 호환성을 사용하도록 설정해야 합니다. [방법을 알아보세요](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>다음 단계
* [방화벽 및 프록시 설정 고려](storage-sync-files-firewall-and-proxy.md)
* [Azure Files 배포에 대한 계획](storage-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
* [Azure 파일 동기화 모니터링](storage-sync-files-monitoring.md)