---
title: Azure 파일 동기화 모니터링 | Microsoft Docs
description: Azure 파일 동기화를 모니터링하는 방법을 설명합니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699282"
---
# <a name="monitor-azure-file-sync"></a>Azure 파일 동기화 모니터링

Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure 모니터, 저장소 동기화 서비스 및 Windows 서버를 사용하여 Azure 파일 동기화 배포를 모니터링하는 방법에 대해 설명합니다.

현재 다음 모니터링 옵션을 사용할 수 있습니다.

## <a name="azure-monitor"></a>Azure Monitor

[Azure 모니터를](https://docs.microsoft.com/azure/azure-monitor/overview) 사용하여 메트릭을 보고 동기화, 클라우드 계층화 및 서버 연결에 대한 경고를 구성합니다.  

### <a name="metrics"></a>메트릭

Azure 파일 동기화의 메트릭은 기본적으로 사용하도록 설정되며 15분마다 Azure Monitor로 전송됩니다.

Azure 모니터에서 Azure 파일 동기화 메트릭을 보려면 **저장소 동기화 서비스** 리소스 유형을 선택합니다.

Azure Monitor에서 사용 가능한 Azure 파일 동기화용 메트릭은 다음과 같습니다.

| 메트릭 이름 | 설명 |
|-|-|
| 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드)입니다.<br><br>단위: 바이트<br>집계 유형: 합계<br>적용 가능한 차원: 서버 끝점 이름, 동기화 방향, 그룹 이름 동기화 |
| 클라우드 계층화 회수 | 회수되는 데이터의 크기입니다.<br><br>**참고**: 이 메트릭은 나중에 제거됩니다. 클라우드 계층화 리콜 크기 메트릭을 사용하여 리콜된 데이터의 크기를 모니터링합니다.<br><br>단위: 바이트<br>집계 유형: 합계<br>적용 가능한 차원: 서버 이름 |
| 클라우드 계층화 리콜 크기 | 회수되는 데이터의 크기입니다.<br><br>단위: 바이트<br>집계 유형: 합계<br>적용 가능한 차원: 서버 이름, 그룹 이름 동기화 |
| 애플리케이션별 클라우드 계층화 리콜 크기 | 응용 프로그램에서 회수하는 데이터의 크기입니다.<br><br>단위: 바이트<br>집계 유형: 합계<br>적용 가능한 차원: 응용 프로그램 이름, 서버 이름, 동기화 그룹 이름 |
| 클라우드 계층화 리콜 처리량 | 데이터 회수 처리량의 크기입니다.<br><br>단위: 바이트<br>집계 유형: 합계<br>적용 가능한 차원: 서버 이름, 그룹 이름 동기화 |
| 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일의 수입니다.<br><br>단위: 개수<br>집계 유형: 합계<br>적용 가능한 차원: 서버 끝점 이름, 동기화 방향, 그룹 이름 동기화 |
| 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드)<br><br>단위: 개수<br>집계 유형: 합계<br>적용 가능한 차원: 서버 끝점 이름, 동기화 방향, 그룹 이름 동기화 |
| 서버 온라인 상태 | 서버에서 수신된 하트비트의 수입니다.<br><br>단위: 개수<br>집계 형식: 최대<br>적용 가능한 차원: 서버 이름 |
| 동기화 세션 결과 | 동기화 세션 결과입니다(1=정상 완료된 동기화 세션, 0=실패한 동기화 세션).<br><br>단위: 개수<br>집계 유형: 최대<br>적용 가능한 차원: 서버 끝점 이름, 동기화 방향, 그룹 이름 동기화 |

### <a name="alerts"></a>경고

Azure Monitor에서 경고를 구성하려면 저장소 동기화 서비스를 선택한 다음 경고에 사용할 [Azure 파일 동기화 메트릭을](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) 선택합니다.  

다음 표에는 모니터링할 몇 가지 예제 시나리오와 경고에 사용할 적절한 메트릭이 나열되어 있습니다.

| 시나리오 | 경고에 사용할 메트릭 |
|-|-|
| 포털의 서버 끝점 상태 = 오류 | 동기화 세션 결과 |
| 파일이 서버 또는 클라우드 끝점에 동기화되지 않음 | 동기화 상태가 아닌 파일 |
| 등록된 서버가 저장소 동기화 서비스와 통신하지 못합니다. | 서버 온라인 상태 |
| 클라우드 계층화 리콜 크기가 하루 만에 500GiB를 초과했습니다.  | 클라우드 계층화 리콜 크기 |

Azure 모니터에서 경고 구성에 대해 자세히 알아보려면 [Microsoft Azure의 경고 개요를]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)참조하십시오.

## <a name="storage-sync-service"></a>스토리지 동기화 서비스

등록된 서버 상태, 서버 끝점 상태 및 메트릭을 보려면 Azure 포털의 저장소 동기화 서비스로 이동합니다. 등록된 서버 블레이드 및 **동기화 그룹** 블레이드에서 서버 끝점 상태를 볼 수 **있습니다.**

### <a name="registered-server-health"></a>등록된 서버 상태

- 등록된 **서버** 상태가 **온라인**인 경우 서버가 서비스와 성공적으로 통신하고 있습니다.
- 등록된 **서버** 상태가 **오프라인으로 표시되는**경우 서버의 저장소 동기화 모니터(AzureStorageSyncMonitor.exe) 프로세스가 실행 중인지 확인합니다. 서버가 방화벽이나 프록시 뒤에 있는 경우 [이 문서를](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) 참조하여 방화벽 및 프록시를 구성합니다.

### <a name="server-endpoint-health"></a>서버 엔드포인트 상태

- 포털의 서버 엔드포인트 상태는 서버의 원격 분석 이벤트 로그에 기록되는 동기화 이벤트(ID 9102 및 9302)를 기준으로 합니다. 오류 취소와 같은 일시적인 오류로 인해 동기화 세션이 실패해도 현재 동기화 세션이 진행되는 동안 포털에서 동기화가 정상으로 나타날 수 있습니다. 이벤트 ID 9302는 파일이 적용되는지 확인하는 데 사용됩니다. 자세한 내용은 [동기화 상태](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 및 동기화 [진행 상황을](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)참조하십시오.
- 동기화가 진행되지 않아 포털에 동기화 오류가 표시되면 지침에 대한 [문제 해결 설명서를](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) 참조하세요.

### <a name="metric-charts"></a>메트릭 차트

- 다음 메트릭 차트는 저장소 동기화 서비스 포털에서 볼 수 있습니다.

  | 메트릭 이름 | 설명 | 블레이드 이름 |
  |-|-|-|
  | 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 클라우드 계층화 회수 | 회수되는 데이터 크기 | 등록된 서버 |
  | 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일 수 | 서버 엔드포인트 |
  | 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 서버 온라인 상태 | 서버에서 수신된 하트비트 수 | 등록된 서버 |

- 자세한 내용은 [Azure 모니터](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)를 참조하십시오.

  > [!Note]  
  > 스토리지 동기화 서비스 포털에서 제공하는 차트의 시간 범위는 24시간입니다. 다른 시간 범위 또는 차원을 보려면 Azure Monitor를 사용하세요.

## <a name="windows-server"></a>Windows Server

Windows Server에서 클라우드 계층화, 등록된 서버 및 동기화 상태를 볼 수 있습니다.

### <a name="event-logs"></a>이벤트 로그

서버의 원격 분석 이벤트 로그를 사용하여 등록된 서버, 동기화 및 클라우드 계층화 상태를 모니터링합니다. 원격 분석 이벤트 로그는 *응용 프로그램 및 서비스\Microsoft\FileSync\에이전트*아래의 이벤트 뷰어에 있습니다.

동기화 상태:

- 이벤트 ID 9102는 동기화 세션이 완료된 후 기록됩니다. 이 이벤트를 사용하여 동기화 세션이**성공했는지(HResult = 0)** 및 항목별 동기화 오류가 있는지 확인합니다. 자세한 내용은 동기화 [상태](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 및 [항목별 오류 설명서를](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) 참조하십시오.

  > [!Note]  
  > 경우에 따라 동기화 세션이 전체적으로 실패하거나 0이 아닌 PerItemErrorCount가 있는 경우가 있습니다. 그러나 여전히 진행이 진행되며 일부 파일이 성공적으로 동기화됩니다. 응용 된 파일 카운트, 적용 된 DirCount, 적용 된 툼 스톤 카운트 및 적용 된 SizeBytes 와 같은 응용 된 필드에서 이 것을 볼 수 있습니다. 이 필드는 세션의 성공 정도를 알려줍니다. 여러 동기화 세션이 행에 실패하고 응용 횟수가 증가하는 경우 지원 티켓을 열기 전에 동기화 시간을 다시 시도할 수 있습니다.

- 활성 동기화 세션이 있으면 5~10분마다 이벤트 ID 9302가 기록됩니다. 이 이벤트를 사용하여 현재 동기화 세션이 진행 중인지**확인합니다(응용항목계산 > 0).** 동기화가 진행되지 않으면 동기화 세션이 결국 실패하고 이벤트 ID 9102가 오류로 기록됩니다. 자세한 내용은 동기화 [진행률 설명서를](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)참조하십시오.

등록된 서버 상태:

- 서버가 서비스에 작업을 쿼리하면 30초마다 이벤트 ID 9301이 기록됩니다. GetNextJob 상태가 **= 0으로**완료되면 서버는 서비스와 통신할 수 있습니다. GetNextJob이 오류로 완료되면 [문제 해결 설명서에서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) 지침을 확인하십시오.

클라우드 계층화 상태:

- 서버에서 계층화 활동을 모니터링하려면 응용 프로그램 및 서비스 아래의 이벤트 뷰어에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9003, 9016 및 9029를 *사용합니다\Microsoft\FileSync\Agent.*

  - 이벤트 ID 9003은 서버 엔드포인트에 대한 오류 분포를 제공합니다. 예: 총 오류 수 및 오류 코드입니다. 오류 코드당 하나의 이벤트가 기록됩니다.
  - 이벤트 ID 9016은 볼륨에 대한 고스팅 결과를 제공합니다. 예를 들어 여유 공간 백분율은 세션에서 고스트된 파일 수, 고스트에 실패한 파일 수입니다.
  - 이벤트 ID 9029는 서버 엔드포인트의 고스팅 세션 정보를 제공합니다. 예: 세션에서 시도된 파일 수, 세션에서 계층화된 파일 수 및 이미 계층화된 파일 수입니다.
  
- 서버에서 리콜 활동을 모니터링하려면 *응용 프로그램 및 서비스 아래의*이벤트 뷰어에 있는 원격 분석 이벤트 로그에서 Event ID 9005, 9006, 9009 및 9059를 사용합니다.

  - 9005 이벤트 ID는 서버 엔드포인트에 대한 회수 안정성을 제공합니다. 예: 액세스한 총 고유 파일과 실패한 액세스가 있는 고유 파일 합계입니다.
  - 이벤트 ID 9006은 서버 엔드포인트에 대한 회수 오류 분포를 제공합니다. 예: 총 실패한 요청 및 오류 코드입니다. 오류 코드당 하나의 이벤트가 기록됩니다.
  - 이벤트 ID 9009는 서버 엔드포인트의 회수 세션 정보를 제공합니다. 예: 기간 초, 카운트파일리콜 성공, 카운트파일리콜 실패.
  - 이벤트 ID 9059는 서버 엔드포인트의 애플리케이션 회수 분포를 제공합니다. 예: 공유 Id, 응용 프로그램 이름 및 TotalEgressNetworkBytes입니다.

### <a name="performance-counters"></a>성능 카운터

동기화 활동을 모니터링할 서버에서 Azure 파일 동기화 성능 카운터를 사용합니다.

서버에서 Azure 파일 동기화 성능 카운터를 보려면 성능 모니터(Perfmon.exe)를 엽니다. **전송된 AFS 바이트** 및 **AFS 동기화 작업** 개체 에서 카운터를 찾을 수 있습니다.

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
- [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
- [방화벽 및 프록시 설정 고려](storage-sync-files-firewall-and-proxy.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
- [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)
- [Azure Files 질문과 대답](storage-files-faq.md)
