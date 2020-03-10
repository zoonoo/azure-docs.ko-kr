---
title: Azure Media Services 고가용성 인코딩
description: 지역 데이터 센터 중단 또는 오류가 발생 하는 경우 보조 Media Services 계정으로 장애 조치 (failover) 하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78934197"
---
# <a name="media-services-high-availability-encoding"></a>Media Services 고가용성 인코딩 

Azure Media Services encoding service는 지역 일괄 처리 플랫폼 이며 현재 단일 지역 내에서 고가용성을 위해 설계 되지 않았습니다. 기본 구성 요소나 종속 서비스 (예: 저장소, SQL)의 지역 데이터 센터 가동 중단 또는 실패가 발생 하는 경우에는 인코딩 서비스에서 현재 서비스의 즉각적인 장애 조치 (failover)를 제공 하지 않습니다. 이 문서에서는 장애 조치 (failover)를 사용 하 여 고가용성 아키텍처를 유지 관리 하 고 응용 프로그램의 가용성을 최적화 하 Media Services는 방법을 설명 합니다.

이 문서에서 설명 하는 지침 및 모범 사례에 따라, 단일 지역에서 중단이 발생 하는 경우 인코딩 오류, 지연 및 복구 시간을 최소화 하는 위험을 줄일 수 있습니다.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>지역 간 인코딩 시스템을 빌드하는 방법

* Azure Media Services 계정을 두 개 이상 [만듭니다](create-account-cli-how-to.md) .

    두 계정이 서로 다른 지역에 있어야 합니다. 자세한 내용은 [Azure Media Services 서비스를 배포](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)하는 지역을 참조 하세요.
* 작업을 제출할 계획인 동일한 지역에 미디어를 업로드 합니다. 인코딩을 시작 하는 방법에 대 한 자세한 내용은 [HTTPS URL에서 작업 입력 만들기](job-input-from-http-how-to.md) 또는 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조 하세요.

    그런 다음 다른 지역에 [작업](transforms-jobs-concept.md) 을 다시 제출 해야 하는 경우 JobInputHttp를 사용 하거나 [복사-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) 을 사용 하 여 원본 자산 컨테이너의 데이터를 대체 지역의 자산 컨테이너로 복사할 수 있습니다.
* Azure Event Grid를 통해 각 계정에서 JobStateChange 메시지를 구독 합니다. 자세한 내용은 다음을 참조하세요.

    * 몇 가지 이유로 Azure Event Grid 메시지가 지연 되는 경우 대체 (fallback) 추가를 포함 하 Azure Event Grid 작업을 모니터링 하는 방법을 보여 주는 [오디오 분석 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) 입니다.
    * [Media Services 이벤트에 대 한 Azure Event Grid 스키마](media-services-event-schemas.md)
    * [Azure Portal 또는 CLI를 통해 이벤트를 등록](reacting-to-media-services-events.md) 합니다 (Eventgrid Management SDK를 사용 하 여 수행할 수도 있음).
    * [Microsoft Azure EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (Media Services 이벤트를 기본적으로 지원)

    Azure Functions를 통해 Event Grid 이벤트를 사용할 수도 있습니다.
* [작업](transforms-jobs-concept.md)을 만들 때:

    * 현재 사용 되는 계정 목록에서 무작위로 계정을 선택 합니다 .이 목록에는 일반적으로 두 계정이 모두 포함 되지만 문제가 검색 되 면 계정이 하나만 포함 될 수 있습니다. 목록이 비어 있으면 운영자가 조사할 수 있도록 경고를 발생 시킵니다.
    * 일반적인 지침은 [joboutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) 당 하나의 [미디어 예약 단위](media-reserved-units-cli-how-to.md) 를 사용 해야 합니다 ( [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) 를 사용 하지 않는 경우 joboutput 당 미디어 예약 단위 4 개 권장).
    * 선택한 계정에 대 한 Mru (미디어 예약 단위) 수를 가져옵니다. 현재 **미디어 예약 단위** 수가 아직 최대값에 없으면 작업에 필요한 mru의 수를 추가 하 고 서비스를 업데이트 합니다. 작업 전송 속도는 높고 Mru을 자주 쿼리 하는 경우에는 적절 한 시간 제한이 있는 값에 대해 분산 캐시를 사용 합니다.
    * 처리 중인 작업 수의 수를 유지 합니다.

* JobStateChange 처리기가 작업이 예약 된 상태에 도달한 알림을 가져오는 경우 일정 상태와 사용 된 지역/계정에 입력 한 시간을 기록 합니다.
* JobStateChange 처리기가 작업이 처리 상태에 도달 했음을 나타내는 알림을 가져오는 경우 작업의 레코드를 처리 중으로 표시 합니다.
* JobStateChange 처리기가 작업이 완료/오류 발생/취소 됨 상태에 도달 했음을 나타내는 알림을 가져오는 경우 작업의 레코드를 마지막으로 표시 하 고 처리 중인 작업 수를 감소 시킵니다. 선택한 계정에 대 한 미디어 예약 단위 수를 가져오고 현재 MRU 번호를 처리 중인 작업 수와 비교 합니다. 처리 중인 수가 MRU 수보다 작은 경우에는 감소 하 고 서비스를 업데이트 합니다.
* 작업의 레코드를 주기적으로 확인 하는 별도의 프로세스를 포함 합니다.
    
    * 지정 된 지역에 대 한 적절 한 시간 내에 처리 상태로 이동 하지 않는 예약 된 상태의 작업이 있는 경우 현재 사용 된 계정 목록에서 해당 지역을 제거 합니다.  비즈니스 요구 사항에 따라 해당 작업을 바로 취소 하 고 다른 지역으로 다시 전송 하도록 결정할 수 있습니다. 또는 다음 상태로 전환 하는 데 더 많은 시간을 제공할 수 있습니다.
    * 계정에 구성 된 미디어 예약 단위 수와 전송 속도에 따라 큐에 대기 중인 작업의 수에 따라 시스템이 아직 처리 되지 않은 상태가 될 수도 있습니다.  대기 상태의 작업 목록이 한 지역에서 허용 되는 한도를 초과 하는 경우 해당 작업을 취소 하 고 다른 지역에 제출할 수 있습니다.  그러나이는 현재 부하에 대 한 계정에 구성 된 미디어 예약 단위가 충분 하지 않은 증상이 원인일 수 있습니다.  필요한 경우 Azure 지원을 통해 더 높은 미디어 예약 단위 할당량을 요청할 수 있습니다.
    * 지역이 계정 목록에서 제거 된 경우 목록에 다시 추가 하기 전에 복구를 위해 모니터링 합니다.  지역에서 기존 작업 (취소 및 다시 제출 되지 않은 경우)을 통해 지역 상태를 모니터링 하 고, 일정 시간 후에 다시 목록에 계정을 추가 하 고, 영향을 받을 수 있는 작동 중단에 대 한 Azure 통신을 모니터링할 수 있습니다. Azure Media Services.
    
MRU 수가 크게 증가 하는 것을 발견 하면 감소 논리를 정기 작업으로 이동 합니다. Mru를 업데이트 해야 하는지 확인 하기 위해 미리 작업 전송 논리에서 처리 중인를 현재 MRU 수와 비교 합니다.

## <a name="next-steps"></a>다음 단계

* [주문형 비디오 지역 간 스트리밍 빌드](media-services-high-availability-streaming.md)
* [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-media-services) 체크 아웃
