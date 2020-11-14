---
title: Azure 파일 동기화 에이전트에 대한 릴리스 정보 | Microsoft Docs
description: Azure Files에서 조직의 파일 공유를 중앙 집중화 하는 데 사용할 수 있는 Azure File Sync 에이전트에 대 한 릴리스 정보를 참조 하세요.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 11/5/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 0b85bbbbd69338f54f9e698d9d47058f78c32057
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630076"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트에 대한 릴리스 정보
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Windows Server 설치는 Azure 파일 공유의 빠른 캐시로 변환됩니다. 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다(SMB, NFS 및 FTPS 포함). 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure 파일 동기화 에이전트의 지원되는 버전에 대한 릴리스 정보를 제공합니다.

## <a name="supported-versions"></a>지원되는 버전
지원 되는 Azure File Sync 에이전트 버전은 다음과 같습니다.

| Milestone | 에이전트 버전 번호 | 릴리스 날짜 | 상태 |
|----|----------------------|--------------|------------------|
| V 11.1 릴리스- [KB4539951](https://support.microsoft.com/en-us/help/4539951)| 11.1.0.0 | 2020 년 11 월 4 일 | 지원 됨-Flighting |
| V 10.1 릴리스- [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 6 월 5 일, 2020 | 지원됨 |
| 2020년 5월 업데이트 롤업 - [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 2020년 5월 19일 | 지원됨 |
| V10 릴리스 - [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 2020년 4월 9일 | 지원됨 |
| 2019년 12월 업데이트 롤업 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019년 12월 12일 | 지원 됨-에이전트 버전이 2021 년 2 월 16 일에 만료 됩니다. |
| V9 릴리스 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019년 12월 2일 | 지원 됨-에이전트 버전이 2021 년 2 월 16 일에 만료 됩니다. |
| V8 릴리스 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019년 10월 8일 | 지원 됨-에이전트 버전이 2021 년 1 월 12 일에 만료 됩니다. |

## <a name="unsupported-versions"></a>지원 되지 않는 버전
다음 Azure File Sync 에이전트 버전은 만료 되었으며 더 이상 지원 되지 않습니다.

| Milestone | 에이전트 버전 번호 | 릴리스 날짜 | 상태 |
|----|----------------------|--------------|------------------|
| V7 릴리스 | 7.0.0.0-7.2.0.0 | 해당 없음 | 지원 되지 않음-2020 년 9 월 1 일에 에이전트 버전이 만료 됨 |
| V6 릴리스 | 6.0.0.0 - 6.3.0.0 | 해당 없음 | 미지원 - 2020년 4월 21일에 에이전트 버전 만료 |
| V5 릴리스 | 5.0.2.0 - 5.2.0.0 | 해당 없음 | 미지원 - 2020년 3월 18일에 에이전트 버전 만료 |
| V4 릴리스 | 4.0.1.0 - 4.3.0.0 | 해당 없음 | 미지원 - 2019년 11월 6일에 에이전트 버전 만료 |
| V3 릴리스 | 3.1.0.0 - 3.4.0.0 | 해당 없음 | 미지원 - 2019년 8월 19일에 에이전트 버전 만료 |
| GA 이전 에이전트 | 1.1.0.0 - 3.0.13.0 | 해당 없음 | 미지원 - 2018년 10월 1일에 에이전트 버전 만료 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11100"></a>에이전트 버전 11.1.0.0
다음 릴리스 정보는 Azure File Sync 에이전트의 버전 11.1.0.0 (11 월 4 2020 일 출시)를 위한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제
- 초기 다운로드 및 자동 관리 회수를 제어 하는 새로운 클라우드 계층화 모드
    - 초기 다운로드 모드: 이제 새 서버 끝점에 파일을 처음으로 다운로드 하려는 방법을 선택할 수 있습니다. 마지막으로 수정한 타임 스탬프를 통해 모든 파일의 계층화 된 파일 또는 가능한 많은 파일을 서버에 다운로드 하려면 어떻게 해야 하나요? 이 작업을 수행할 수 있습니다. 클라우드 계층화를 사용할 수 없습니까? 이제 시스템에서 계층화 된 파일을 사용 하지 않도록 선택할 수 있습니다. 자세한 내용은 배포 Azure File Sync 설명서에서 [서버 끝점 만들기](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) 섹션을 참조 하세요.
    - 자동 관리 회수 모드: 파일이 만들어지거나 수정 될 때마다 동일한 동기화 그룹 내에서 지정 하는 서버로 사전에 회수할 수 있습니다. 이렇게 하면 지정한 각 서버에서 파일을 쉽게 사용할 수 있습니다. 전 세계 팀이 동일한 데이터를 사용 하 고 있나요? 팀이 다음 아침에 도착할 때 다른 표준 시간대의 팀에서 업데이트 한 모든 파일이 다운로드 되 고 이동할 수 있도록 자동 관리 회수를 사용 하도록 설정 합니다. 자세한 내용은 배포 Azure File Sync 설명서의 [Azure 파일 공유에서 새로운 파일 및 변경 된 파일 사전 회수](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) 섹션을 참조 하세요.

- 클라우드 계층화 마지막 액세스 시간 추적에서 응용 프로그램 제외 이제 마지막 액세스 시간 추적에서 응용 프로그램을 제외할 수 있습니다. 응용 프로그램에서 파일에 액세스 하는 경우 파일에 대 한 마지막 액세스 시간이 클라우드 계층화 데이터베이스에서 업데이트 됩니다. 바이러스 방지와 같은 파일 시스템을 검색 하는 응용 프로그램은 모든 파일이 파일을 계층화 할 때 영향을 주는 동일한 마지막 액세스 시간을 갖습니다.

    마지막 액세스 시간 추적에서 응용 프로그램을 제외 하려면 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 아래에 있는 HeatTrackingProcessNameExclusionList 레지스트리 설정에 프로세스 이름을 추가 합니다.

    예: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > 데이터 중복 제거 및 FSRM (파일 서버 리소스 관리자) 프로세스는 기본적으로 제외 되 고 (하드 코드 됨) 프로세스 제외 목록은 5 분 마다 새로 고쳐집니다.

- 기타 성능 및 안정성 개선
    - Azure 파일 공유에서 변경 된 파일을 검색 하기 위해 변경 검색 성능이 향상 되었습니다.
    - 동기화 업로드 성능이 개선 되었습니다.
    - 이제 초기 업로드가 VSS 스냅숏에서 수행 되어 항목당 오류 및 동기화 세션 오류가 줄어듭니다.
    - 특정 i/o 패턴에 대 한 안정성 향상을 동기화 합니다.
    - 장애 조치 (failover)가 발생할 때 동기화 데이터베이스가 장애 조치 (failover) 클러스터에서 백오프 하지 않도록 하는 버그를 수정 했습니다.
    - 계층화 된 파일에 액세스할 때 회수 성능이 개선 되었습니다.

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](./storage-sync-files-planning.md#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano Server 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다. 자세한 내용은 [추천 시스템 리소스](./storage-sync-files-planning.md#recommended-system-resources)를 참조하세요.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 실행은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](storage-sync-files-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하기 위해 [AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 저장소 동기화 서비스 및/또는 저장소 계정을 다른 리소스 그룹, 구독 또는 Azure AD 테 넌 트로 이동할 수 있습니다. 저장소 동기화 서비스 또는 저장소 계정을 이동한 후에는 Microsoft.storagesync 응용 프로그램에 저장소 계정에 대 한 액세스 권한을 부여 해야 합니다 ( [저장소 계정에 액세스할 수 있는지 확인 Azure File Sync](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)참조).

    > [!Note]  
    > 클라우드 엔드포인트를 만들 때 스토리지 동기화 서비스 및 스토리지 계정이 동일한 Azure AD 테넌트에 있어야 합니다. 클라우드 엔드포인트를 만든 후에는 스토리지 동기화 서비스 및 스토리지 계정을 다른 Azure AD 테넌트로 이동할 수 있습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.

## <a name="agent-version-10100"></a>에이전트 버전 10.1.0.0
다음 릴리스 정보는 2020 년 6 월 5 일에 릴리스된 Azure File Sync 에이전트의 버전 10.1.0.0에 대 한 것입니다. 이러한 메모는 버전 10.0.0.0 및 10.0.2.0에 대해 나열 된 릴리스 정보에 추가 되었습니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- Azure 개인 끝점 지원
    - 이제 저장소 동기화 서비스에 대 한 동기화 트래픽을 개인 끝점으로 보낼 수 있습니다. 이를 통해 Express 경로 또는 VPN 연결을 통해 터널링을 사용할 수 있습니다. 자세히 알아보려면 [Azure File Sync 네트워크 끝점 구성](./storage-sync-files-networking-endpoints.md)을 참조 하세요.
- 이제 동기화 된 메트릭 파일은 끝이 아닌, 대량 동기화가 실행 되는 동안 진행률을 표시 합니다.
- 에이전트 설치, 클라우드 계층화, 동기화 및 원격 분석에 대 한 기타 안정성 향상

## <a name="agent-version-10020"></a>에이전트 버전 10.0.2.0
다음 릴리스 정보는 2020년 5월 19일 릴리스된 Azure 파일 동기화 에이전트의 버전 10.0.2.0에 대한 것입니다. 이러한 정보는 버전 10.0.0.0에 대해 나열된 릴리스 정보에 대한 추가 사항입니다.

이 릴리스에서 해결된 이슈:  
- 스토리지 동기화 에이전트(FileSyncSvc)는 Azure 파일 동기화 v10 에이전트를 설치한 후에 자주 충돌합니다.

> [!Note]  
>이 릴리스는 새 버전을 사용할 수 있게 되면 자동으로 업데이트되도록 구성된 서버로 플라이트되지 않았습니다. 이 업데이트를 설치하려면 Microsoft 업데이트 또는 Microsoft 업데이트 카탈로그를 사용합니다(설치 지침은 [KB4522412](https://support.microsoft.com/help/4522412) 참조).

## <a name="agent-version-10000"></a>에이전트 버전 10.0.0.0
다음 릴리스 정보는 2020년 4월 9일 릴리스된 Azure 파일 동기화 에이전트의 버전 10.0.0.0에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 포털에서 동기화 진행률 개선
    - V10 에이전트 릴리스를 사용하여 Azure Portal은 실행 중인 동기화 세션 유형을 표시하기 시작합니다. 예를 들어 초기 다운로드, 일반 다운로드, 백그라운드 회수(신속한 재해 복구 사례) 등이 있습니다. 

- 향상된 클라우드 계층화 포털 환경
    - 계층화 또는 회수에 실패한 파일이 있는 경우 이제 서버 엔드포인트 속성에서 계층화 오류를 볼 수 있습니다.
    - 서버 엔드포인트에 다음과 같은 추가 클라우드 계층화 정보를 사용할 수 있습니다.
        - 로컬 캐시 크기
        - 캐시 사용 효율성
        - 클라우드 계층화 정책 세부 정보: 볼륨 크기, 현재 사용 가능한 공간 또는 로컬 캐시에서 가장 오래된 파일의 마지막 액세스 시간
    - 이러한 변경 내용은 초기 V10 에이전트 릴리스 직후에 Azure Portal에 제공됩니다.

- 저장소 동기화 서비스 및/또는 저장소 계정을 다른 Azure Active Directory 테 넌 트로 이동 하는 기능 지원
    - 이제 Azure File Sync에서는 스토리지 동기화 서비스 및/또는 스토리지 계정을 다른 리소스 그룹, 구독 또는 Azure AD 테넌트로 이동할 수 있습니다.
    
- 기타 성능 및 안정성 개선
    - 스토리지 계정에 VNET(가상 네트워크) 및 방화벽 규칙이 구성된 경우 Azure 파일 공유의 변경 검색이 실패할 수 있습니다.
    - 회수와 관련된 메모리 소비가 감소했습니다. 
    - [AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) cmdlet을 사용하는 경우 성능이 향상됩니다.
    - 기타 안정성 개선 
    
### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](./storage-sync-files-planning.md#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano Server 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 실행은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](storage-sync-files-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하기 위해 [AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 저장소 동기화 서비스 및/또는 저장소 계정을 다른 리소스 그룹, 구독 또는 Azure AD 테 넌 트로 이동할 수 있습니다. 저장소 동기화 서비스 또는 저장소 계정을 이동한 후에는 Microsoft.storagesync 응용 프로그램에 저장소 계정에 대 한 액세스 권한을 부여 해야 합니다 ( [저장소 계정에 액세스할 수 있는지 확인 Azure File Sync](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)참조).

    > [!Note]  
    > 클라우드 엔드포인트를 만들 때 스토리지 동기화 서비스 및 스토리지 계정이 동일한 Azure AD 테넌트에 있어야 합니다. 클라우드 엔드포인트를 만든 후에는 스토리지 동기화 서비스 및 스토리지 계정을 다른 Azure AD 테넌트로 이동할 수 있습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.

## <a name="agent-version-9100"></a>에이전트 버전 9.1.0.0
다음 릴리스 정보는 2019년 12월 12일 릴리스된 Azure 파일 동기화 에이전트의 버전 9.1.0.0에 대한 것입니다. 이러한 정보는 버전 9.0.0.0에 대해 나열된 릴리스 정보에 대한 추가 사항입니다.

이 릴리스에서 해결된 이슈:  
- Azure 파일 동기화 에이전트 버전 9.0으로 업그레이드한 후 다음 오류 중 하나를 나타내며 동기화가 실패합니다.
    - 0x8e5e044e(JET_errWriteConflict)
    - 0x8e5e0450(JET_errInvalidSesid)
    - 0x8e5e0442(JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>에이전트 버전 9.0.0.0
다음 릴리스 정보는 2019년 12월 2일 릴리스된 Azure 파일 동기화 에이전트의 버전 9.0.0.0에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 셀프 서비스 복원 지원
    - 사용자는 이제 이전 버전 기능을 사용하여 파일을 복원할 수 있습니다. V9 릴리스 전에는 클라우드 계층화를 사용하도록 설정한 볼륨에서 이전 버전 기능이 지원되지 않았습니다. 클라우드 계층화를 사용하는 엔드포인트가 있는 경우 각 볼륨에 대해 이 기능을 별도로 사용하도록 설정해야 합니다. 자세한 내용은 다음을 참조하세요.  
[이전 버전 및 VSS(볼륨 섀도 복사본 서비스)를 통한 셀프 서비스 복원](./storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- 더 큰 파일 공유 크기 지원 
    - Azure 파일 동기화는 이제 단일 동기화 네임스페이스에서 최대 64TiB 및 1억개 파일을 지원합니다.  
 
- Server 2019에서 데이터 중복 제거 지원 
    - Windows Server 2019에서 클라우드 계층화를 사용하면 이제 데이터 중복 제거가 지원됩니다. 클라우드 계층화를 사용하는 볼륨에서 데이터 중복 제거를 지원하려면 Windows 업데이트 [KB4520062](https://support.microsoft.com/help/4520062)를 설치해야 합니다. 
 
- 계층화할 파일에 대한 최소 파일 크기 개선 
    - 계층화할 파일에 대한 최소 파일 크기는 이제 파일 시스템 클러스터 크기(이중 파일 시스템 클러스터 크기)를 기반으로 합니다. 예를 들어 기본적으로 NTFS 파일 시스템 클러스터 크기는 4KB이며, 파일 계층에 대한 결과 최소 파일 크기는 8KB입니다. 
 
- 네트워크 연결 테스트 cmdlet 
    - Azure 파일 동기화 구성의 일부로 여러 서비스 엔드포인트에 연결해야 합니다. 각 서버에는 서버에서 액세스할 수 있어야 하는 자체 DNS 이름이 있습니다. 이러한 URL은 서버가 등록된 지역에 대해서도 고유합니다. 서버를 등록한 후에는 연결 테스트 cmdlet(PowerShell 및 서버 등록 유틸리티)을 사용하여 이 서버와 관련된 모든 URL과의 통신을 테스트할 수 있습니다. 이 cmdlet은 불완전한 통신으로 인해 서버에서 Azure 파일 동기화가 완전히 작동하지 못할 때 문제를 해결하는 데 유용하며, 프록시 및 방화벽 구성을 미세 조정하는 데도 사용할 수 있습니다.  
 
        네트워크 연결 테스트를 실행하려면 다음 PowerShell 명령을 실행합니다. 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- 클라우드 계층화를 사용하는 경우 서버 엔드포인트 제거 기능 개선 
    - 이전과 마찬가지로 서버 엔드포인트를 제거해도 Azure 파일 공유에서 파일이 제거되지 않습니다. 그러나 로컬 서버의 구문 재분석 지점 관련 동작이 변경되었습니다. 이제 서버 엔드포인트를 제거할 때 구문 재분석 지점(서버에서 로컬이 아닌 파일에 대한 포인터)이 삭제됩니다. 완전히 캐시된 파일은 서버에 유지됩니다. 이러한 향상된 기능은 서버 엔드포인트를 제거할 때 [분리된 계층화된 파일](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 방지하기 위해 고안되었습니다. 서버 엔드포인트를 다시 만들 경우 계층화된 파일의 구문 재분석 지점은 서버에서 다시 만들어집니다.  
 
- 성능과 안정성이 개선되었습니다. 
    - 회수 오류가 감소합니다. 이제 회수 크기가 네트워크 대역폭에 따라 자동으로 조정됩니다. 
    - 동기화 그룹에 새 서버를 추가할 때 다운로드 성능이 개선되었습니다. 
    - 제약 조건 충돌로 인해 동기화되지 않는 파일이 줄어듭니다. 
    - 서버 엔드포인트 경로가 볼륨 탑재 지점인 경우 특정 시나리오에서 파일이 계층화되지 못하거나 예기치 않게 회수됩니다.
    
### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](./storage-sync-files-planning.md#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano Server 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 실행은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](storage-sync-files-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 2KB보다 큰 경우 보안 설명자의 DACL(임의 액세스 제어 목록) 부분입니다. (이 문제는 단일 항목에 약 40개 이상의 ACE(액세스 제어 항목)가 있는 경우에만 적용됩니다.)
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다. 자세한 내용은 [서버 엔드포인트를 삭제한 후 서버에서 계층화된 파일에 액세스할 수 없음](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 참조하세요.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하기 위해 [AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.
- pagefile.sys가 클라우드 계층화를 사용하도록 설정한 볼륨에 있는 경우 파일이 계층화되지 못할 수 있습니다. pagefile.sys는 클라우드 계층화를 사용하지 않도록 설정한 볼륨에 있어야 합니다.

## <a name="agent-version-8000"></a>에이전트 버전 8.0.0.0
다음 릴리스 정보는 2019년 10월 8일 릴리스된 Azure 파일 동기화 에이전트의 버전 8.0.0.0에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 복원 성능 개선
    - Azure Backup를 통해 수행되는 복구의 복구 시간이 단축되었습니다. 복원된 파일은 Azure 파일 동기화 서버와 훨씬 더 빠르게 다시 동기화됩니다. 
- 향상된 클라우드 계층화 포털 환경  
    - 회수에 실패한 계층화된 파일이 있는 경우 이제 서버 엔드포인트 속성에서 회수 오류를 볼 수 있습니다. 또한 서버에 클라우드 계층화 필터 드라이버가 로드되지 않은 경우 이제 서버 엔드포인트 상태가 오류 및 완화 단계를 표시합니다.
- 보다 간편해진 에이전트 설치
    - 서버를 등록하는 데 Az\AzureRM PowerShell 모듈이 더 이상 필요하지 않으므로 설치가 더 간편하고 빨라졌습니다.
- 기타 성능 및 안정성 개선

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](./storage-sync-files-planning.md#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano Server 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 실행은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](storage-sync-files-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 2KB보다 큰 경우 보안 설명자의 DACL(임의 액세스 제어 목록) 부분입니다. (이 문제는 단일 항목에 약 40개 이상의 ACE(액세스 제어 항목)가 있는 경우에만 적용됩니다.)
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다. 자세한 내용은 [서버 엔드포인트를 삭제한 후 서버에서 계층화된 파일에 액세스할 수 없음](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 참조하세요.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하기 위해 [AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.