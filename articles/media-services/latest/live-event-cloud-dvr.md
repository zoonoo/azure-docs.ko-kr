---
title: 시간 이동 및 라이브 출력을 사용하여 주문형 비디오 재생
titleSuffix: Azure Media Services
description: 이 문서에서는 시간 이동 및 라이브 출력을 사용하여 라이브 스트림을 기록하고 주문형 재생을 만드는 방법에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899789"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>시간 이동 및 라이브 출력을 사용하여 주문형 비디오 재생

Azure Media 서비스에서 [라이브 출력](https://docs.microsoft.com/rest/api/media/liveoutputs) 개체는 라이브 스트림을 Catch하고 미디어 서비스 계정의 자산에 기록하는 디지털 비디오 레코더와 같습니다. 기록된 콘텐츠는 [자산](https://docs.microsoft.com/rest/api/media/assets) 리소스에 의해 정의된 컨테이너에 유지됩니다(컨테이너는 계정에 연결된 Azure Storage 계정에 있음). 또한 라이브 출력을 사용하면 아카이브 레코딩에 보관되는 스트림의 양(예: 클라우드 DVR 용량)이나 시청자가 라이브 스트림을 시청할 수 있는 경우와 같이 나가는 라이브 스트림의 일부 속성을 제어할 수 있습니다. 디스크의 아카이브는 라이브 출력의 **archiveWindowLength** 속성에 지정된 콘텐츠의 양만 보유하는 순환 아카이브 "창"입니다. 이 창 외부에 있는 콘텐츠는 저장소 컨테이너에서 자동으로 삭제되며 복구할 수 없습니다. archiveWindowLength 값은 DVR의 용량을 지정하는 ISO-8601 시간 기간(예: PTHH:MM:SS)을 나타냅니다. 값을 최소 3분에서 최대 25시간으로 설정할 수 있습니다.

라이브 이벤트와 라이브 출력 간의 관계는 채널(Live Event)이 일정한 비디오 스트림을 나타내고 녹화(라이브 출력)가 특정 시간 세그먼트(예: 저녁 뉴스)로 범위가 조정된다는 점에서 기존 TV 방송과 유사합니다. 오후 6시 30분 ~ 오후 7:00). 스트림이 라이브 이벤트로 유입되면 에셋, 라이브 출력 및 스트리밍 로케이터를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 라이브 출력은 스트림을 보관하고 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints)를 통해 시청자가 스트림을 사용할 수 있도록 합니다. 라이브 이벤트에서 보관 기간 및 설정이 서로 다른 여러 개의 라이브 출력(최대 3개)을 만들 수 있습니다. 라이브 스트리밍 워크플로우에 대한 자세한 내용은 [일반 단계](live-streaming-overview.md#general-steps) 섹션을 참조하세요.

## <a name="using-a-dvr-during-an-event"></a>이벤트 중 DVR 사용

이 섹션에서는 이벤트 중에 DVR을 사용하여 '되감기'에 사용할 수 있는 스트림 부분을 제어하는 방법에 대해 설명합니다.

이 `archiveWindowLength` 값은 뷰어가 현재 라이브 위치에서 얼마나 멀리 까지 갈 수 있는지를 결정합니다. 또한 `archiveWindowLength` 이 값은 클라이언트 매니페스트가 증가할 수 있는 기간도 결정합니다.

축구 경기를 스트리밍하고 있으며 30분밖에 `ArchiveWindowLength` 되지 않는 것으로 가정해 보시면 됩니다. 경기 시작 45분 후에 이벤트를 보기 시작한 시청자는 최대 15분 마크까지 이전으로 탐색할 수 있습니다. 라이브 이벤트가 중지될 때까지 게임에 대한 라이브 출력이 계속됩니다. archiveWindowLength 외부에 있는 콘텐츠는 저장소에서 계속 삭제되며 복구할 수 없습니다. 이 예제에서는 이벤트 시작과 15분 마크 사이의 비디오가 DVR과 자산에 대한 Blob 저장소의 컨테이너에서 제거되었을 것입니다. 아카이브는 복구할 수 없으며 Azure Blob 저장소의 컨테이너에서 제거됩니다.

라이브 이벤트는 동시에 실행되는 라이브 출력을 최대 3개까지 지원합니다(동시에 하나의 라이브 스트림에서 최대 3개의 레코딩/아카이브를 만들 수 있음). 이 지원을 통해 필요에 따라 이벤트의 다른 부분을 게시하고 보관할 수 있습니다. 연중 무휴 라이브 선형 피드를 브로드캐스트하는 것이지만, 하루 동안의 다른 프로그램 "기록"을 만들어 다시 보기 용도의 주문형 콘텐츠를 고객에게 제공하고 싶습니다. 이 시나리오에서는 먼저 1시간 이하의 짧은 아카이브 기간이 있는 기본 라이브 출력을 만들면 시청자가 조정하는 기본 라이브 스트림입니다. 이 라이브 출력에 대한 스트리밍 로케이터를 만들고 앱 또는 웹 사이트에 "라이브" 피드로 게시합니다. 라이브 이벤트가 실행되는 동안 프로그램의 시작 부분에서(또는 나중에 잘라내기 핸들을 제공하기 위해 예정보다 5분 먼저) 두 번째 동시 라이브 출력을 프로그래밍 방식으로 만들 수 있습니다. 이 두 번째 라이브 출력은 프로그램 종료 5분 후에 삭제할 수 있습니다. 이 두 번째 에셋을 사용하면 새 스트리밍 로케이터를 만들어 이 프로그램을 앱 카탈로그에 온디맨드 에셋으로 게시할 수 있습니다. 첫 번째 라이브 출력의 "라이브" 피드가 선형 피드를 계속 브로드캐스트하는 동안 주문형 비디오로 공유하려는 다른 프로그램 경계 또는 하이라이트에 대해 이 프로세스를 여러 번 반복할 수 있습니다.

## <a name="creating-an-archive-for-on-demand-playback"></a>주문형 재생을 위한 아카이브 만들기

라이브 출력이 삭제될 때 라이브 출력이 자동으로 보관되는 자산은 주문형 자산이 됩니다. 라이브 이벤트를 중지하려면 먼저 모든 라이브 출력을 삭제해야 합니다. 선택적 플래그 제거를 사용할 수 [있습니다OutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) 중지시 라이브 출력을 자동으로 제거합니다.

이벤트를 중지하고 삭제한 후에도 사용자는 에셋을 삭제하지 않는 한 보관된 콘텐츠를 주문형 비디오로 스트리밍할 수 있습니다. 이벤트에서 자산을 사용하는 경우 자산을 삭제하면 안 됩니다. 먼저 이벤트를 삭제해야 합니다.

스트리밍 로케이터를 사용하여 라이브 출력의 에셋을 게시한 경우 스트리밍 로케이터의 만료 또는 삭제(중 먼저)가 발생할 때까지 라이브 이벤트(DVR 창 길이까지)를 계속 볼 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)

> [!NOTE]
> 라이브 출력을 삭제하면 저작물의 기본 저작물 및 콘텐츠를 삭제하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [비디오를 서브클립 .](subclip-video-rest-howto.md)
* [자산에 대한 필터를 정의합니다.](filters-dynamic-manifest-rest-howto.md)
