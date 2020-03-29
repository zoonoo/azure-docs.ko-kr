---
title: Azure 미디어 서비스 고가용성 인코딩
description: 지역 데이터 센터 가동 중단 또는 오류가 발생하는 경우 보조 Media Services 계정으로 장애 조치(failover)하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934197"
---
# <a name="media-services-high-availability-encoding"></a>미디어 서비스 고가용성 인코딩 

Azure Media Services 인코딩 서비스는 지역 일괄 처리 플랫폼이며 현재 단일 리전 내에서 고가용성을 위해 설계되지 않았습니다. 인코딩 서비스는 현재 지역 데이터 센터 가동 중단 또는 기본 구성 요소 또는 종속 서비스(예: 저장소, SQL)의 실패가 있는 경우 서비스의 즉각적인 장애 조치(failover)를 제공하지 않습니다. 이 문서에서는 장애 조치(failover)를 통해 고가용성 아키텍처를 유지하고 응용 프로그램에 대한 최적의 가용성을 보장하기 위해 Media Services를 배포하는 방법을 설명합니다.

이 문서에 설명된 지침및 모범 사례를 따르면 인코딩 실패, 지연의 위험을 낮추고 단일 지역에서 중단이 발생하는 경우 복구 시간을 최소화할 수 있습니다.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>지역 간 인코딩 시스템을 구축하는 방법

* 두 개 이상의 Azure 미디어 서비스 계정을 [만듭니다.](create-account-cli-how-to.md)

    두 계정은 서로 다른 지역에 있어야 합니다. 자세한 내용은 [Azure 미디어 서비스 서비스가 배포된 지역을](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)참조하십시오.
* 작업을 제출할 계획인 동일한 지역에 미디어를 업로드합니다. 인코딩을 시작하는 방법에 대한 자세한 내용은 [HTTPS URL에서 작업 입력 만들기](job-input-from-http-how-to.md) 또는 로컬 [파일에서 작업 입력 만들기를](job-input-from-local-file-how-to.md)참조하십시오.

    그런 다음 [작업을](transforms-jobs-concept.md) 다른 리전으로 다시 제출해야 하는 경우 JobInputHttp를 사용하거나 [복사-Blob을](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) 사용하여 원본 자산 컨테이너의 데이터를 대체 지역의 자산 컨테이너로 복사할 수 있습니다.
* Azure 이벤트 그리드를 통해 각 계정의 JobStateChange 메시지에 대한 구독을 합니다. 자세한 내용은 다음을 참조하세요.

    * 어떤 이유로 Azure 이벤트 그리드 메시지가 지연되는 경우 대체 를 추가하는 것을 포함하여 Azure Event Grid로 작업을 모니터링하는 방법을 보여 주려는 [오디오 분석 샘플입니다.](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
    * [Media Services 이벤트에 대한 Azure Event Grid 스키마](media-services-event-schemas.md)
    * [Azure 포털 또는 CLI를 통해 이벤트에](reacting-to-media-services-events.md) 등록(EventGrid 관리 SDK로 도모할 수도 있음)
    * [Microsoft.Azure.EventGrid SDK(기본적으로](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) 미디어 서비스 이벤트를 지원).

    Azure 함수를 통해 이벤트 그리드 이벤트를 사용할 수도 있습니다.
* [작업을](transforms-jobs-concept.md)만들 때 :

    * 현재 사용 중인 계정 목록에서 계정을 임의로 선택합니다(이 목록에는 일반적으로 두 계정이 모두 포함되지만 문제가 감지되면 하나의 계정만 포함될 수 있음). 목록이 비어 있으면 운영자가 조사할 수 있도록 경고를 발생시면 됩니다.
    * 일반적인 지침은 [작업 출력당](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) 하나의 미디어 예약 단위가 필요합니다(작업 출력당 3개의 [미디어 예약 단위가](media-reserved-units-cli-how-to.md) 권장되는 [VideoAnalyzerPreset를](analyzing-video-audio-files-concept.md) 사용하지 않는 경우).
    * 선택한 계정에 대한 미디어 예약 단위(Mrus)의 수를 가져옵니다. 현재 **미디어 예약 단위** 수가 아직 최대값에 있지 않은 경우 작업에 필요한 Mrus 수를 추가하고 서비스를 업데이트합니다. 작업 제출 률이 높고 RMRA를 자주 쿼리하여 최대값을 찾으면 적절한 시간 지정이 있는 값에 분산 캐시를 사용합니다.
    * 기내 작업 수를 유지합니다.

* JobStateChange 처리기가 작업이 예약된 상태에 도달했다는 알림을 받으면 해당 작업이 일정 상태로 들어가는 시간과 사용된 지역/계정을 기록합니다.
* JobStateChange 처리기가 작업이 처리 상태에 도달했다는 알림을 받으면 해당 작업의 레코드를 처리로 표시합니다.
* JobStateChange 처리기가 작업이 완료/오류/취소 상태에 도달했다는 알림을 받으면 해당 작업의 레코드를 최종 으로 표시하고 기내 작업 수를 감소시입니다. 선택한 계정에 대한 미디어 예약 단위 수를 얻고 현재 MRU 번호를 기내 작업 수와 비교합니다. 기내 수가 MRU 개수보다 적으면 이를 줄이고 서비스를 업데이트합니다.
* 작업 레코드를 주기적으로 보는 별도의 프로세스가 있어야 합니다.
    
    * 지정된 지역에 대해 적절한 시간 내에 처리 상태로 진행되지 않은 예약된 상태의 작업이 있는 경우 현재 사용 중인 계정 목록에서 해당 지역을 제거합니다.  비즈니스 요구 사항에 따라 해당 작업을 즉시 취소하고 다른 지역으로 다시 제출할 수 있습니다. 또는 다음 상태로 이동하는 데 더 많은 시간을 할애할 수 있습니다.
    * 계정에 구성된 미디어 예약 단위 수와 제출 속도에 따라 시스템이 아직 처리를 위해 선택하지 않은 큐에 있는 작업도 있을 수 있습니다.  큐에 대기중인 상태의 작업 목록이 리전에서 허용되는 제한을 초과하면 해당 작업을 취소하고 다른 리전에 제출할 수 있습니다.  그러나 이 현상은 현재 부하에 대해 계정에 충분한 미디어 예약 장치가 구성되지 않은 증상일 수 있습니다.  필요한 경우 Azure 지원을 통해 더 높은 미디어 예약 단위 할당량을 요청할 수 있습니다.
    * 계정 목록에서 영역이 제거된 경우 목록에 다시 추가하기 전에 복구를 모니터링합니다.  지역 상태는 해당 지역의 기존 작업(취소 및 다시 제출되지 않은 경우), 기간을 거쳐 목록에 계정을 다시 추가하고, 영향을 줄 수 있는 중단에 대한 Azure 통신을 모니터링하는 운영자를 통해 모니터링할 수 있습니다. Azure 미디어 서비스.
    
MRU 카운트가 많이 위아래로 스래싱되는 것을 발견하면 감소 논리를 주기적인 작업으로 이동합니다. 작업 전 제출 논리를 현재 MRU 수와 기내 수를 비교하여 Mru를 업데이트해야 하는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [주문형 비디오 교차 리전 스트리밍 구축](media-services-high-availability-streaming.md)
* 코드 [샘플](https://docs.microsoft.com/samples/browse/?products=azure-media-services) 확인
