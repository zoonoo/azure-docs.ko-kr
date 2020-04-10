---
title: Azure 파일 동기화 에이전트에 대한 릴리스 정보 | Microsoft Docs
description: Azure 파일 동기화 에이전트에 대한 릴리스 정보
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 765decb8c65254d63ef5267cbc496d2320f58f6e
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991929"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트에 대한 릴리스 정보
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Windows Server 설치는 Azure 파일 공유의 빠른 캐시로 변환됩니다. 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다(SMB, NFS 및 FTPS 포함). 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure 파일 동기화 에이전트의 지원되는 버전에 대한 릴리스 정보를 제공합니다.

## <a name="supported-versions"></a>지원되는 버전
다음 버전은 Azure 파일 동기화 에이전트에 대해 지원됩니다.

| Milestone | 에이전트 버전 번호 | 릴리스 날짜 | 상태 |
|----|----------------------|--------------|------------------|
| V10 릴리스 - [KB4522359](https://support.microsoft.com/en-us/help/4522409/azure-file-sync-agent-v10-release-march-2020)| 10.0.0.0 | 2020년 4월 9일 | 지원됨 |
| 2019년 12월 업데이트 롤업 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019년 12월 12일 | 지원됨 |
| V9 릴리스 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019년 12월 2일 | 지원됨 |
| V8 릴리스 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019년 10월 8일 | 지원됨 |
| 2019년 7월 업데이트 롤업 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019년 7월 24일 | 지원됨 |
| 2019년 7월 업데이트 롤업 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019년 7월 12일 | 지원됨 |
| V7 릴리스 - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 2019년 6월 19일 | 지원됨 |
| 2019년 6월 업데이트 롤업 - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 2019년 6월 27일 | 지원됨 - 에이전트 버전은 2020년 4월 21일에 만료됩니다. |
| 2019년 6월 업데이트 롤업 - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 2019년 6월 13일 | 지원됨 - 에이전트 버전은 2020년 4월 21일에 만료됩니다. |
| 2019년 5월 업데이트 롤업 - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 2019년 5월 7일 | 지원됨 - 에이전트 버전은 2020년 4월 21일에 만료됩니다. |
| V6 릴리스 - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 2019년 4월 21일 | 지원됨 - 에이전트 버전은 2020년 4월 21일에 만료됩니다. |
| V5 릴리스 | 5.0.2.0 - 5.2.0.0 | N/A | 지원되지 않음 - 에이전트 버전이 2020년 3월 18일에 만료되었습니다. |
| V4 릴리스 | 4.0.1.0 - 4.3.0.0 | N/A | 지원되지 않음 - 에이전트 버전이 2019년 11월 6일에 만료되었습니다. |
| V3 릴리스 | 3.1.0.0 - 3.4.0.0 | N/A | 지원되지 않음 - 에이전트 버전이 2019년 8월 19일에 만료되었습니다. |
| PRE-GA 에이전트 | 1.1.0.0 - 3.0.13.0 | N/A | 미지원 - 2018년 10월 1일에 에이전트 버전 만료 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10000"></a>에이전트 버전 10.0.0.0
다음 릴리스 노트는 Azure 파일 동기화 에이전트의 버전 10.0.0.0(2020년 4월 9일 릴리스)에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 포털에서 향상된 동기화 진행률
    - V10 에이전트 릴리스를 통해 Azure 포털은 곧 실행 중인 동기화 세션 의 유형을 표시하기 시작합니다. 예를 들어 초기 다운로드, 일반 다운로드, 배경 리콜 (빠른 재해 복구 사례) 및 이와 유사한. 

- 향상된 클라우드 계층화 포털 환경
    - 계층 또는 회수에 실패한 파일이 있는 경우 이제 서버 끝점 속성에서 계층화 오류를 볼 수 있습니다.
    - 서버 엔드포인트에 추가 클라우드 계층화 정보를 사용할 수 있습니다.
        - 로컬 캐시 크기
        - 캐시 사용 효율성
        - 클라우드 계층화 정책 세부 정보: 볼륨 크기, 현재 여유 공간 또는 로컬 캐시에서 가장 오래된 파일의 마지막으로 액세스한 시간입니다.
    - 이러한 변경 사항은 초기 V10 에이전트 릴리스 직후 Azure 포털에서 제공됩니다.

- 저장소 동기화 서비스 및/또는 저장소 계정을 다른 Azure Active Directory(AAD) 테넌트로 이동하기 위한 지원
    - 이제 Azure File Sync는 저장소 동기화 서비스 및/또는 저장소 계정을 다른 리소스 그룹, 구독 또는 Azure AD 테넌트로 이동하는 것을 지원합니다.
    
- 평가 도구는 이제 마침표로 끝나는 파일 또는 디렉터리를 식별합니다.
    - 마침표로 끝나는 파일이나 디렉터리를 식별하도록 [평가 도구가](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 업데이트되었습니다. 마침표로 끝나는 파일 또는 디렉터리는 현재 Azure 파일 동기화에서 지원되지 않습니다. [평가 도구의](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)업데이트된 버전을 사용하려면 [Azure PowerShell 모듈의](https://docs.microsoft.com/powershell/azure/install-az-ps)최신 버전을 설치합니다.
 
- 기타 성능 및 안정성 향상
    - 저장소 계정에서 가상 네트워크(VNET) 및 방화벽 규칙을 구성하면 Azure 파일 공유에 대한 변경 검색이 실패할 수 있습니다.
    - 임의 액세스 제어 목록(DACL) 동기화에 대한 2KB 보안 설명자 제한이 더 이상 없습니다.  
    - 리콜과 관련된 메모리 소비 감소. 
    - [Invoke-AzStorageSync검색](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) cmdlet을 사용할 때 성능이 향상되었습니다.
    - 기타 기타 안정성 향상. 
    
### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano 서버 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure File Sync 에이전트가 설치된 서버에서 sysprep을 실행하는 것은 지원되지 않으며 예기치 않은 결과를 초래할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

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
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하려면 [Invoke-AzStorageSyncDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > 클라우드 끝점을 만들 때 저장소 동기화 서비스 및 저장소 계정은 동일한 Azure AD 테넌트에 있어야 합니다. 클라우드 끝점이 만들어지면 저장소 동기화 서비스 및 저장소 계정을 다른 Azure AD 테넌차로 이동할 수 있습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.

## <a name="agent-version-9100"></a>에이전트 버전 9.1.0.0
다음 릴리스 노트는 2019년 12월 12일 릴리스된 Azure File Sync 에이전트의 버전 9.1.0.0에 대한 것입니다. 이 참고 사항은 버전 9.0.0.0에 나열된 릴리스 노트에 추가됩니다.

이 릴리스에서 수정된 문제:  
- Azure File Sync 에이전트 버전 9.0으로 업그레이드한 후 다음 오류 중 하나로 동기화가 실패합니다.
    - 0x8e5e04e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>에이전트 버전 9.0.0.0
다음 릴리스 노트는 Azure 파일 동기화 에이전트의 버전 9.0.0.0(2019년 12월 2일 릴리스)에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 셀프 서비스 복원 지원
    - 이제 사용자는 이전 버전 기능을 사용하여 파일을 복원할 수 있습니다. v9 릴리스 이전에는 클라우드 계층화가 활성화된 볼륨에서는 이전 버전 기능이 지원되지 않았습니다. 이 기능은 각 볼륨에 대해 별도로 활성화되어야 하며, 클라우드 계층화가 활성화된 끝점이 있는 경우. 자세한 내용은 다음을 참조하세요.  
[이전 버전 및 VSS(볼륨 섀도 복사본 서비스)를 통해 셀프 서비스 복원.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service) 
 
- 더 큰 파일 공유 크기 지원 
    - Azure File Sync는 이제 네임스페이스를 동기화하는 단일 파일에서 최대 64TiB 및 1억 개의 파일을 지원합니다.  
 
- 서버 2019의 데이터 중복 제거 지원 
    - 이제 Windows Server 2019에서 사용할 수 있는 클라우드 계층화를 사용하여 데이터 중복 제거가 지원됩니다. 클라우드 계층화를 사용하여 볼륨에서 데이터 중복 제거를 지원하려면 Windows 업데이트 [KB4520062를](https://support.microsoft.com/help/4520062) 설치해야 합니다. 
 
- 계층에 대한 파일의 최소 파일 크기 개선 
    - 이제 파일에서 계층으로의 최소 파일 크기는 파일 시스템 클러스터 크기(파일 시스템 클러스터 크기의 두 배)를 기준으로 합니다. 예를 들어 기본적으로 NTFS 파일 시스템 클러스터 크기는 4KB이며 계층에 대한 파일의 결과 최소 파일 크기는 8KB입니다. 
 
- 네트워크 연결 테스트 cmdlet 
    - Azure 파일 동기화 구성의 일부로 여러 서비스 끝점에 연결해야 합니다. 각각 서버에 액세스할 수 있어야 하는 고유한 DNS 이름이 있습니다. 이러한 URL은 서버가 등록된 지역에도 해당됩니다. 서버가 등록되면 연결 테스트 cmdlet(PowerShell 및 서버 등록 유틸리티)을 사용하여 이 서버와 관련된 모든 URL과의 통신을 테스트할 수 있습니다. 이 cmdlet은 불완전한 통신으로 서버가 Azure File Sync로 완전히 작동하지 못하게 하고 프록시 및 방화벽 구성을 미세 조정하는 데 사용할 수 있는 문제를 해결하는 데 도움이 될 수 있습니다.  
 
        네트워크 연결 테스트를 실행하려면 다음 PowerShell 명령을 실행합니다. 
 
        가져오기 모듈 "C:\프로그램 파일\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        테스트 스토리지싱크네트워크연결
 
- 클라우드 계층화가 활성화된 경우 서버 엔드포인트 개선 제거 
    - 이전과 마찬가지로 서버 끝점을 제거해도 Azure 파일 공유에서 파일이 제거되지는 않습니다. 그러나 로컬 서버의 희원 지점에 대한 동작이 변경되었습니다. 서버 끝점을 제거할 때 리파스 포인트(서버에서 로컬이 아닌 파일에 대한 포인터)가 삭제됩니다. 완전히 캐시된 파일은 서버에 남아 있습니다. 서버 끝점을 제거할 때 [분리계층 파일이](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) 발생하지 않도록 개선되었습니다. 서버 끝점을 다시 만들면 계층화된 파일에 대한 리파스 포인트가 서버에서 다시 만들어집니다.  
 
- 성능과 안정성이 개선되었습니다. 
    - 리콜 실패 감소. 이제 네트워크 대역폭에 따라 리콜 크기가 자동으로 조정됩니다. 
    - 동기화 그룹에 새 서버를 추가할 때 다운로드 성능이 향상되었습니다. 
    - 제약 조건 충돌로 인해 동기화되지 않는 파일이 줄어듭니다. 
    - 서버 끝점 경로가 볼륨 마운트 지점인 경우 파일이 계층화에 실패하거나 특정 시나리오에서 예기치 않게 회수됩니다.
    
### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano 서버 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure File Sync 에이전트가 설치된 서버에서 sysprep을 실행하는 것은 지원되지 않으며 예기치 않은 결과를 초래할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

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
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다. 자세한 내용은 [서버 끝점을 삭제한 후 서버에서 계층화 된 파일에 액세스할 수 없음을](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)참조하십시오.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하려면 [Invoke-AzStorageSyncDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.
- pagefile.sys가 클라우드 계층화가 활성화된 볼륨에 있는 경우 파일이 계층화되지 않을 수 있습니다. pagefile.sys는 클라우드 계층화가 비활성화된 볼륨에 위치해야 합니다.

## <a name="agent-version-8000"></a>에이전트 버전 8.0.0.0
다음 릴리스 노트는 Azure 파일 동기화 에이전트의 버전 8.0.0.0(2019년 10월 8일 릴리스)에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 성능 향상 복원
    - Azure 백업을 통해 수행된 복구에 대한 복구 시간 단축 복원된 파일은 Azure 파일 동기화 서버로 훨씬 빠르게 다시 동기화됩니다. 
- 향상된 클라우드 계층화 포털 환경  
    - 회수에 실패한 계층화 된 파일이 있는 경우 이제 서버 끝점 속성에서 회수 오류를 볼 수 있습니다. 또한 클라우드 계층화 필터 드라이버가 서버에 로드되지 않은 경우 서버 끝점 상태는 오류 및 완화 단계를 표시합니다.
- 에이전트 설치가 간편해
    - Az\AzureRM PowerShell 모듈은 더 이상 서버를 등록할 필요가 없기 때문에 설치가 더 간단하고 빠릅니다.
- 기타 성능 및 안정성 향상

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano 서버 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure File Sync 에이전트가 설치된 서버에서 sysprep을 실행하는 것은 지원되지 않으며 예기치 않은 결과를 초래할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

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
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다. 자세한 내용은 [서버 끝점을 삭제한 후 서버에서 계층화 된 파일에 액세스할 수 없음을](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)참조하십시오.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경된 파일을 즉시 동기화하려면 [Invoke-AzStorageSyncDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.

## <a name="agent-version-7200"></a>에이전트 버전 7.2.0.0
다음 릴리스 노트는 2019년 7월 24일 릴리스된 Azure 파일 동기화 에이전트의 버전 7.2.0.0에 대한 것입니다. 이 참고 사항은 버전 7.0.0.0에 나열된 릴리스 노트에 추가됩니다.

이 릴리스에서 해결된 문제 목록:  
- 프록시 구성이 null이면 저장소 동기화 에이전트(FileSyncSvc)가 충돌합니다.
- 서버의 여러 끝점이 이름이 같은 경우 서버 끝점이 BCDR(오류 0x80c80257 - ECS_E_BCDR_IN_PROGRESS)을 시작합니다.
- 클라우드 계층화 안정성 향상.

## <a name="agent-version-7100"></a>에이전트 버전 7.1.0.0
다음 릴리스 노트는 2019년 7월 12일 릴리스된 Azure 파일 동기화 에이전트의 버전 7.1.0.0에 대한 것입니다. 이 참고 사항은 버전 7.0.0.0에 나열된 릴리스 노트에 추가됩니다.

이 릴리스에서 해결된 문제 목록:  
- Windows Server 2012 R2에서 SMB를 통해 서버 끝점 위치에 액세스하거나 탐색하는 속도가 느립니다. 
- Azure 파일 동기화 v6 에이전트를 설치한 후 CPU 사용률이 증가했습니다.
- 클라우드 계층화 원격 분석 개선.
- 클라우드 계층화 및 동기화의 기타 안정성 개선

## <a name="agent-version-7000"></a>에이전트 버전 7.0.0.0
다음 릴리스 노트는 Azure 파일 동기화 에이전트의 버전 7.0.0.0(2019년 6월 19일 릴리스)에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 더 큰 파일 공유 크기 지원
    - 더 큰 Azure 파일 공유의 미리 보기를 통해 파일 동기화에 대한 지원 한도도 증가하고 있습니다. 이 첫 번째 단계에서 Azure File Sync는 이제 네임스페이스를 동기화하는 단일 단일 에서 최대 25TB 및 5천만 개의 파일을 지원합니다. 대용량 파일 공유 미리 보기를 신청하려면 https://aka.ms/azurefilesatscalesurvey이 양식을 작성합니다. 
- 저장소 계정의 방화벽 및 가상 네트워크 설정 지원
    - Azure File Sync는 이제 저장소 계정의 방화벽 및 가상 네트워크 설정을 지원합니다. 방화벽 및 가상 네트워크 설정으로 작동하도록 배포를 구성하려면 [방화벽 및 가상 네트워크 설정 구성을](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)참조하십시오.
- Azure 파일 공유에서 변경된 파일을 즉시 동기화하는 PowerShell cmdlet
    - Azure 파일 공유에서 변경된 파일을 즉시 동기화하려면 Invoke-AzStorageSyncDetection PowerShell cmdlet을 사용하여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 이 cmdlet은 일부 자동화된 프로세스 유형이 Azure 파일 공유를 변경하거나 관리자가 변경(예: 파일 및 디렉터리를 공유로 이동)하는 시나리오를 위한 것입니다. 최종 사용자 변경의 경우 IaaS VM에 Azure 파일 동기화 에이전트를 설치하고 최종 사용자가 IaaS VM을 통해 파일 공유에 액세스하도록 하는 것이 좋습니다. 이렇게 하면 Invoke-AzStorageSyncIncmdlet을 사용할 필요 없이 모든 변경 내용이 다른 에이전트에 빠르게 동기화됩니다. 자세한 내용은 [Invoke-AzStorageSync검색](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) 문서를 참조하십시오.
- 동기화되지 않은 파일이 발생한 경우 포털 환경이 개선되었습니다.
    - 동기화에 실패한 파일이 있는 경우 이제 포털에서 일시적인 오류와 영구 오류를 구분합니다. 일시적인 오류는 일반적으로 관리자 작업 없이 스스로 해결합니다. 예를 들어 현재 사용 중인 파일은 파일 핸들이 닫힐 때까지 동기화되지 않습니다. 지속적인 오류의 경우 각 오류의 영향을 받는 파일 수가 표시됩니다. 영구 오류 수는 동기화 그룹의 모든 서버 끝점의 열을 동기화하지 않는 파일에도 표시됩니다.
- 향상된 Azure 백업 파일 수준 복원
    - 이제 Azure Backup을 사용하여 복원된 개별 파일이 검색되고 서버 끝점에 더 빠르게 동기화됩니다.
- 향상된 클라우드 계층화 리콜 cmdlet 안정성 향상 
    - 이제 Invoke-StorageSyncFileRecall cmdlet을 사용하면 고객이 로보카피와 유사한 파일 재시도 횟수와 파일 재시도 지연당 을 지정할 수 있습니다. 이전에는 이 cmdlet이 지정된 경로 아래의 모든 계층화 된 파일을 임의의 순서로 회수했습니다. 새 -Order 매개 변수를 사용하면 이 cmdlet은 가장 인기 있는 데이터를 먼저 회수하고 클라우드 계층화 정책을 준수합니다(날짜 정책이 충족되거나 볼륨 사용 여유 공간이 충족되는 경우 리콜 중지, 둘 중 먼저 발생하는 경우).
- TLS 1.2 전용 지원(TLS 1.0 및 1.1은 비활성화됨)
    - Azure 파일 동기화는 이제 TLS 1.0 및 1.1을 사용하지 않도록 설정한 서버에서만 TLS 1.2를 사용할 수 있도록 지원합니다. 이 개선 이전에TLS 1.0 및 1.1이 서버에서 비활성화된 경우 서버 등록이 실패했습니다.
- 동기화 및 클라우드 계층화에 대한 기타 성능 및 안정성 향상
    - 이 릴리스에는 몇 가지 안정성 및 성능 향상이 있습니다. 그 중 일부는 대역폭 제한 일정이 설정된 경우 클라우드 계층화의 효율성을 높이고 Azure File Sync를 전체적으로 더 잘 작동하도록 하는 것을 목표로 합니다.

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano 서버 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure File Sync 에이전트가 설치된 서버에서 sysprep을 실행하는 것은 지원되지 않으며 예기치 않은 결과를 초래할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

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
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.

## <a name="agent-version-6300"></a>에이전트 버전 6.3.0.0
다음 릴리스 노트는 2019년 6월 27일 릴리스된 Azure File Sync 에이전트의 버전 6.3.0.0에 대한 것입니다. 이 참고 사항은 버전 6.0.0.0에 나열된 릴리스 노트에 추가됩니다.

이 릴리스에서 해결된 문제 목록:  
- Windows Server 2012 R2에서 SMB를 통해 서버 끝점 위치에 액세스하거나 탐색하는 속도가 느립니다. 
- Azure 파일 동기화 v6 에이전트를 설치한 후 CPU 사용률 증가
- 클라우드 계층화 원격 분석 개선 사항

## <a name="agent-version-6200"></a>에이전트 버전 6.2.0.0
다음 릴리스 노트는 2019년 6월 13일 릴리스된 Azure File Sync 에이전트의 버전 6.2.0.0에 대한 것입니다. 이 참고 사항은 버전 6.0.0.0에 나열된 릴리스 노트에 추가됩니다.

이 릴리스에서 해결된 문제 목록:  
- 서버 끝점을 만든 후 백그라운드 리콜이 서버에 파일을 다운로드할 때 높은 CPU 사용량이 발생할 수 있습니다.
- 동기화 및 클라우드 계층화 작업은 토큰 만료로 인해 오류 ECS_E_SERVER_CREDENTIAL_NEEDED 실패할 수 있습니다.
- 파일을 다운로드하는 URL에 예약된 문자가 포함된 경우 파일 회수가 실패할 수 있습니다. 

## <a name="agent-version-6100"></a>에이전트 버전 6.1.0.0
다음 릴리스 노트는 2019년 5월 6일 릴리스된 Azure 파일 동기화 에이전트의 버전 6.1.0.0에 대한 것입니다. 이 참고 사항은 버전 6.0.0.0에 나열된 릴리스 노트에 추가됩니다.

이 릴리스에서 해결된 문제 목록:  
- Windows 관리 센터는 Azure File Sync 에이전트 버전 6.0이 설치된 서버에 에이전트 버전 및 서버 끝점 구성을 표시하지 못합니다.

## <a name="agent-version-6000"></a>에이전트 버전 6.0.0.0
다음 릴리스 노트는 Azure 파일 동기화 에이전트의 버전 6.0.0.0(2019년 4월 22일 릴리스)에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 에이전트 자동 업데이트 지원
  - 피드백을 듣고 Azure File Sync 서버 에이전트에 자동 업데이트 기능을 추가했습니다. 자세한 내용은 [Azure 파일 동기화 에이전트 업데이트 정책을](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)참조하십시오.
- Azure 파일 공유 ACL 지원
  - Azure 파일 동기화는 항상 서버 끝점 간에 ACL 동기화를 지원하지만 ACL은 클라우드 끝점(Azure 파일 공유)에 동기화되지 않았습니다. 이 릴리스는 서버와 클라우드 끝점 간에 ACL을 동기화하는 지원을 추가합니다.
- 서버 엔드포인트에 대한 병렬 업로드 및 다운로드 동기화 세션 
  - 이제 서버 끝점이 파일 업로드 및 다운로드를 동시에 지원합니다. 더 이상 다운로드가 완료되기를 기다리지 않으므로 Azure 파일 공유에 파일을 업로드할 수 있습니다. 
- 볼륨 및 계층화 상태를 얻을 수 있는 새로운 클라우드 계층화 cmdlet
  - 이제 두 개의 새로운 서버 로컬 PowerShell cmdlet을 사용하여 클라우드 계층화 및 파일 회수 정보를 얻을 수 있습니다. 서버의 두 이벤트 채널의 로깅 정보를 사용할 수 있습니다.
    - Get-StorageSyncFileTieringResult 계층화되지 않은 모든 파일과 경로를 나열하고 이유를 보고합니다.
    - 받기-StorageSyncFile리콜Result 모든 파일 회수 이벤트를 보고합니다. 그것은 모든 리콜 및 경로 뿐만 아니라 성공 또는 해당 리콜에 대 한 오류를 나열 합니다.
  - 기본적으로 두 이벤트 채널 모두 각각 최대 1MB를 저장할 수 있으며 이벤트 채널 크기를 늘려 보고된 파일의 양을 늘릴 수 있습니다.
- FIPS 모드 지원
  - 이제 Azure 파일 동기화 에이전트가 설치된 서버에서 FIPS 모드 활성화를 지원합니다.
    - 서버에서 FIPS 모드를 사용하도록 설정하기 전에 서버에 Azure 파일 동기화 에이전트 및 [PackageManagement 모듈을](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) 설치합니다. FIPS가 서버에서 이미 활성화되어 있는 경우 [패키지관리 모듈을](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) 서버에 [수동으로 다운로드합니다.](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)
- 클라우드 계층화 및 동기화를 위한 기타 안정성 향상

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Nano 서버 배포 옵션에서 지원되지 않습니다.
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
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.
- SMB 클라이언트에서 파일 속성을 볼 때 오프라인 특성은 파일 메타데이터의 SMB 캐싱으로 인해 잘못 설정되도록 나타날 수 있습니다.

## <a name="agent-version-5200"></a>에이전트 버전 5.2.0.0
다음 릴리스 노트는 2019년 4월 4일 릴리스된 Azure 파일 동기화 에이전트의 버전 5.2.0.0에 대한 것입니다. 이 참고 사항은 버전 5.0.2.0에 나열된 릴리스 노트에 추가됩니다.

이 릴리스에서 해결된 문제 목록:  
- 오프라인 데이터 전송 및 데이터 전송 재개 기능에 대한 안정성 향상
- 원격 분석 개선 동기화

## <a name="agent-version-5100"></a>에이전트 버전 5.1.0.0
다음 릴리스 노트는 2019년 3월 7일 릴리스된 Azure 파일 동기화 에이전트의 버전 5.1.0.0에 대한 것입니다. 이 참고 사항은 버전 5.0.2.0에 나열된 릴리스 노트에 추가됩니다.

이 릴리스에서 해결된 문제 목록:  
- 서버에서 변경 열거가 실패하면 파일이 오류 0x80c8031d(ECS_E_CONCURRENCY_CHECK_FAILED)와 동기화되지 않을 수 있습니다.
- 동기화 세션 또는 파일에 오류가 0x80072f78(WININET_E_INVALID_SERVER_RESPONSE)이 발생하면 동기화가 작업을 다시 시도합니다.
- 파일이 오류 0x80c80203(ECS_E_SYNC_INVALID_STAGED_FILE)과 동기화되지 않을 수 있습니다.
- 파일을 리콜할 때 메모리 사용량이 높을 수 있습니다.
- 클라우드 계층화 원격 분석 개선 사항 

## <a name="agent-version-5020"></a>에이전트 버전 5.0.2.0
다음 릴리스 정보는 2019년 2월 12일 릴리스된 Azure 파일 동기화 에이전트의 버전 5.0.2.0에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- Azure Government 클라우드 지원
  - Azure Government 클라우드에 대한 미리 보기 지원이 추가되었습니다. 이 기능을 이용하려면 허용 목록에 구독을 추가하고 Microsoft에서 특수 에이전트를 다운로드해야 합니다. 미리 보기에 액세스하려면 로 [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)직접 이메일을 보내주십시오.
- 데이터 중복 제거 지원
    - Windows Server 2016 및 Windows Server 2019에서 클라우드 계층화를 사용하면 이제 데이터 중복 제거가 완전히 지원됩니다. 클라우드 계층화가 사용되는 볼륨에서 중복 제거를 사용하면 추가 스토리지를 프로비저닝하지 않고도 더 많은 파일을 온-프레미스에 캐시할 수 있습니다.
- 오프라인 데이터 전송 지원(예: Data Box 사용)
    - 선택한 수단을 통해 대량 데이터를 Azure 파일 동기화로 쉽게 마이그레이션하세요. Azure 데이터 상자, AzCopy 및 타사 마이그레이션 서비스를 선택할 수 있습니다. Data Box의 경우 데이터를 Azure로 가져오기 위해 대량 대역폭을 사용할 필요가 없습니다. 메일로 전송하면 됩니다. 자세한 내용은 [오프라인 데이터 전송 문서](https://aka.ms/AFS/OfflineDataTransfer)를 참조하세요.
- 향상된 동기화 성능
    - 이 릴리스 이전에는 동일한 볼륨에 여러 개의 서버 엔드포인트가 있는 고객의 경우 동기화 성능이 느려질 수 있었습니다. Azure 파일 동기화는 핸들이 열려 있는 파일을 동기화하기 위해 하루 한 번 서버에 임시 VSS 스냅샷을 만듭니다. 이제 VSS 동기화 세션이 활성 상태인 경우 동기화를 통해 볼륨에서 여러 개의 서버 엔드포인트를 동기화할 수 있습니다. 볼륨의 다른 서버 엔드포인트에서 동기화를 다시 시작하기 위해 VSS 동기화 세션이 완료되기를 기다릴 필요가 없습니다.
- 포털의 모니터링 개선
    - 다음을 확인할 수 있도록 스토리지 동기화 서비스 포털에 차트가 추가되었습니다.
        - 동기화된 파일 수
        - 전송된 데이터 크기
        - 동기화되지 않은 파일 수
        - 회수되는 데이터 크기
        - 서버 연결 상태
    - 자세한 내용은 [Azure 파일 동기화 모니터링](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring)을 참조하세요.
- 확장성 및 안정성 개선
    - 한 디렉터리에 포함되는 파일 시스템 개체(디렉터리 및 파일)의 최대 개수가 1,000,000개로 증가했습니다. 이전 한도는 200,000개였습니다.
    - 동기화는 대규모 파일의 전송이 중단될 경우 재전송하지 않고 데이터 전송을 다시 시작합니다. 

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Windows Server Core 또는 Nano Server 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.
- FIPS 모드는 지원되지 않으므로 사용하지 않도록 설정해야 합니다. 

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
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.
- SMB 클라이언트에서 파일 속성을 볼 때 오프라인 특성은 파일 메타데이터의 SMB 캐싱으로 인해 잘못 설정되도록 나타날 수 있습니다.
