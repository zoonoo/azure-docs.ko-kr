---
title: Azure 파일 동기화 모니터링 | Microsoft Docs
description: Azure 파일 동기화를 모니터링하는 방법을 설명합니다.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 5a0d02768b0fbd23e33d13c5e5c3fe84a41cdc52
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243657"
---
# <a name="monitor-azure-file-sync"></a>Azure 파일 동기화 모니터링

Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure Portal 및 Windows Server를 사용하여 Azure 파일 동기화 배포를 모니터링하는 방법을 설명합니다.

현재 사용 가능한 모니터링 옵션은 다음과 같습니다.

## <a name="azure-portal"></a>Azure portal

Azure Portal에서는 등록된 서버 상태, 서버 엔드포인트 상태(동기화 상태) 및 메트릭을 확인할 수 있습니다.

### <a name="storage-sync-service"></a>저장소 동기화 서비스

등록된 서버 상태, 서버 엔드포인트 상태 및 메트릭을 확인하려면 Azure Portal의 스토리지 동기화 서비스로 이동합니다. 등록된 서버 상태는 등록된 서버 블레이드에서 확인할 수 있습니다. 서버 엔드포인트 상태는 동기화 그룹 블레이드에서 확인할 수 있습니다.

등록된 서버 상태
- 등록된 서버 상태가 온라인이면 서버가 서비스와 정상적으로 통신하고 있는 것입니다.
- 등록된 서버 상태가 오프라인으로 나타남이면 서버에서 스토리지 동기화 모니터(AzureStorageSyncMonitor.exe) 프로세스가 실행되고 있는지 확인합니다. 서버가 방화벽 또는 프록시로 보호되는 경우에는 [설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)의 내용에 따라 방화벽과 프록시를 구성합니다.

서버 엔드포인트 상태
- 포털의 서버 엔드포인트 상태는 서버의 원격 분석 이벤트 로그에 기록되는 동기화 이벤트(ID 9102 및 9302)를 기준으로 합니다. 취소 오류 등의 일시적인 오류로 인해 동기화 세션이 실패하더라도 현재 동기화 세션이 진행되고 있다면 포털에서는 동기화가 정상 상태로 표시될 수 있습니다. 이벤트 ID 9302를 사용하여 파일이 적용되고 있는지 여부를 확인합니다. 자세한 내용은 [동기화 상태](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [동기화 진행률](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session) 설명서를 참조하세요.
- 동기화가 진행되지 않아 포털에 동기화 오류가 표시되는 경우 [문제 해결 설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)에서 지침을 확인합니다.

메트릭
- 다음 메트릭은 스토리지 동기화 서비스 포털에서 볼 수 있습니다.

  | 메트릭 이름 | 설명 | 포털 블레이드 | 
  |-|-|-|
  | 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 클라우드 계층화 회수 | 회수되는 데이터 크기 | 등록된 서버 |
  | 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일 수 | 서버 엔드포인트 |
  | 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 서버 온라인 상태 | 서버에서 수신된 하트비트 수 | 등록된 서버 |

- 자세한 내용은 [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor) 섹션을 참조하세요. 

  > [!Note]  
  > 스토리지 동기화 서비스 포털에서 제공하는 차트의 시간 범위는 24시간입니다. 다른 시간 범위 또는 차원을 보려면 Azure Monitor를 사용하세요.


### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)를 사용하여 동기화, 클라우드 계층화 및 서버 연결을 모니터링합니다. Azure 파일 동기화의 메트릭은 기본적으로 사용하도록 설정되며 15분마다 Azure Monitor로 전송됩니다.

Azure Monitor에서 Azure 파일 동기화 메트릭을 확인하려면 스토리지 동기화 서비스 리소스 종류를 선택합니다.

Azure Monitor에서 사용 가능한 Azure 파일 동기화용 메트릭은 다음과 같습니다.

| 메트릭 이름 | 설명 |
|-|-|
| 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드)입니다.<br><br>단위: 바이트<br>집계 유형: 합계<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 클라우드 계층화 회수 | 회수되는 데이터의 크기입니다.<br><br>단위: 바이트<br>집계 유형: 합계<br>해당하는 차원: 서버 이름 |
| 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일의 수입니다.<br><br>단위: 개수<br>집계 유형: 합계<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드)<br><br>단위: 개수<br>집계 유형: 합계<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 서버 온라인 상태 | 서버에서 수신된 하트비트의 수입니다.<br><br>단위: 개수<br>집계 유형: 최대<br>해당하는 차원: 서버 이름 |
| 동기화 세션 결과 | 동기화 세션 결과입니다(1=정상 완료된 동기화 세션, 0=실패한 동기화 세션).<br><br>단위: 개수<br>집계 형식: 최대<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |

## <a name="windows-server"></a>Windows Server

Windows Server에서 클라우드 계층화, 등록된 서버 및 동기화 상태를 확인할 수 있습니다.

### <a name="event-logs"></a>이벤트 로그

서버의 원격 분석 이벤트 로그를 사용하여 등록된 서버, 동기화 및 클라우드 계층화 상태를 모니터링합니다. 원격 분석 이벤트 로그는 Applications and Services\Microsoft\FileSync\Agent in Event Viewer에 있습니다.

동기화 상태
- 동기화 시스템이 완료되면 이벤트 ID 9102가 기록됩니다. 동기화 세션이 정상적으로 완료되었는지(HResult = 0) 아니면 항목별 동기화 오류가 발생했는지를 확인하려면 이 이벤트를 사용해야 합니다. 자세한 내용은 [상태 동기화](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [항목별 오류](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) 설명서를 참조하세요.

  > [!Note]  
  > 동기화 세션이 전체적으로 실패하거나 PerItemErrorCount의 값이 0이 아니지만, 여전히 동기화가 계속 진행되고 일부 파일이 성공적으로 동기화되는 경우가 가끔 있습니다. 세션에서 정상적으로 완료되는 부분이 표시되는 적용됨 필드(AppliedFileCount, AppliedDirCount, AppliedTombstoneCount, AppliedSizeBytes)에서 이러한 현상을 확인할 수 있습니다. 여러 동기화 세션이 연속으로 실패하는데 적용됨 횟수는 증가하는 경우 지원 티켓을 개설하기 전에 동기화 시간을 지정하고 다시 시도해 보세요.

- 활성 동기화 세션이 있으면 5~10분마다 이벤트 ID 9302가 기록됩니다. 현재 동기화 세션이 진행되고 있는지(AppliedItemCount 값이 0 이상) 여부를 확인하려면 이 이벤트를 사용해야 합니다. 동기화가 진행되지 않는 경우 동기화 세션은 실패하며, 이벤트 ID 9102가 기록되고 오류가 발생합니다. 자세한 내용은 [동기화 진행률](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session) 설명서를 참조하세요.

등록된 서버 상태
- 서버가 서비스에 작업을 쿼리하면 30초마다 이벤트 ID 9301이 기록됩니다. GetNextJob 완료 시의 상태가 0이면 서버가 서비스와 통신할 수 있는 것입니다. GetNextJob 완료 시 오류가 발생하면 [문제 해결 설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)에서 지침을 참조하세요.

클라우드 계층화 상태
- 서버의 계층화 작업을 모니터링하려면 이벤트 뷰어의 Applications and Services\Microsoft\FileSync\Agent에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9003, 9016 및 9029를 사용합니다.

  - 이벤트 ID 9003은 서버 엔드포인트에 대한 오류 분포를 제공합니다. 총 오류 수, ErrorCode 등을 예로 들 수 있습니다. 한 이벤트는 오류 코드별로 기록됩니다.
  - 이벤트 ID 9016은 볼륨에 대한 고스팅 결과를 제공합니다. 사용 가능한 공간 비율, 세션에서 고스팅된 파일 수, 고스팅에 실패한 파일 수 등을 예로 들 수 있습니다.
  - 이벤트 ID 9029는 서버 엔드포인트의 고스팅 세션 정보를 제공합니다. 세션에서 시도된 파일 수, 세션에서 계층화된 파일 수, 이미 계층화된 파일 수 등을 예로 들 수 있습니다.
  
- 서버의 회수 작업을 모니터링하려면 이벤트 뷰어의 Applications and Services\Microsoft\FileSync\Agent에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9005, 9006,9009 및 9059를 사용합니다.

  - 9005 이벤트 ID는 서버 엔드포인트에 대한 회수 안정성을 제공합니다. 액세스된 고유한 파일 수, 액세스에 실패한 고유한 파일 수 등을 예로 들 수 있습니다.
  - 이벤트 ID 9006은 서버 엔드포인트에 대한 회수 오류 분포를 제공합니다. 실패한 요청 수, ErrorCode 등을 예로 들 수 있습니다. 한 이벤트는 오류 코드별로 기록됩니다.
  - 이벤트 ID 9009는 서버 엔드포인트의 회수 세션 정보를 제공합니다. DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed 등을 예로 들 수 있습니다.
  - 이벤트 ID 9059는 서버 엔드포인트의 애플리케이션 회수 분포를 제공합니다. ShareId, Application Name, TotalEgressNetworkBytes 등을 예로 들 수 있습니다.

### <a name="performance-counters"></a>성능 카운터

동기화 활동을 모니터링할 서버에서 Azure 파일 동기화 성능 카운터를 사용합니다.

서버에서 Azure 파일 동기화 성능 카운터를 확인하려면 성능 모니터(Perfmon.exe)를 시작합니다. 그러면 AFS Bytes Transferred 및 AFS Sync Operations 개체 아래에서 카운터를 확인할 수 있습니다.

성능 모니터에서 사용 가능한 Azure 파일 동기화용 성능 카운터는 다음과 같습니다.

| 성능 개체\카운터 이름 | 설명 |
|-|-|
| AFS Bytes Transferred\Downloaded Bytes/sec | 초당 다운로드한 바이트 수입니다. |
| AFS Bytes Transferred\Uploaded Bytes/sec | 초당 업로드한 바이트 수입니다. |
| AFS Bytes Transferred\Total Bytes/sec | 초당 총 바이트 수(업로드/다운로드)입니다. |
| AFS Sync Operations\Downloaded Sync Files/sec | 초당 다운로드한 파일 수입니다. |
| AFS Sync Operations\Uploaded Sync Files/sec | 초당 업로드한 파일 수입니다. |
| AFS Sync Operations\Total Sync File Operations/sec | 동기화된 파일의 총 수(업로드/다운로드)입니다. |

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [방화벽 및 프록시 설정 고려](storage-sync-files-firewall-and-proxy.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
- [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)
- [Azure Files 질문과 대답](storage-files-faq.md)
