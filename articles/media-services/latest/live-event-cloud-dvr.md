---
title: 타임 시프팅을 사용하여 주문형 비디오 재생 만들기
description: 이 문서에서는 타임 시프팅 및 라이브 출력을 사용하여 라이브 스트림을 기록하고 주문형 재생을 만드는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a2bb876c164f0df56a8b7f3c4a3666ff306e9416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955939"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>타임 시프팅 및 라이브 출력을 사용하여 주문형 비디오 재생 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services에서 [라이브 출력](/rest/api/media/liveoutputs) 개체는 라이브 스트림을 캐치하고 Media Services 계정의 자산에 기록하는 디지털 비디오 레코더와 비슷합니다. 기록된 콘텐츠는 [자산](/rest/api/media/assets) 리소스에서 정의한 컨테이너에서도 유지됩니다(컨테이너는 계정에 연결된 Azure Storage 계정에 있음). 또한 라이브 출력을 사용하면 보관 기록에 유지되는 스트림의 양(예: 클라우드 DVR의 용량), 시청자가 라이브 스트림을 보기 시작할 수 있는 시기 등 나가는 라이브 스트림의 일부 속성을 제어할 수 있습니다. 디스크의 보관은 라이브 출력의 **archiveWindowLength** 속성에 지정된 양의 콘텐츠만 보유하는 순환식 보관 '기간'입니다. 이 기간을 벗어나는 콘텐츠는 스토리지 컨테이너에서 자동으로 삭제되며 복구할 수 없습니다. archiveWindowLength 값은 DVR의 용량을 지정하는 ISO-8601 기간(예: PTHH:MM:SS)을 나타냅니다. 이 값은 최소 1분에서 최대 25시간까지 설정할 수 있습니다.

라이브 이벤트와 라이브 출력 간의 관계는 기존 TV 방송과 비슷하므로 채널(라이브 이벤트)은 일정한 비디오 스트림을 나타내고 녹음/녹화(라이브 출력)는 특정 시간 세그먼트의 범위로 지정됩니다(예: 오후 6시 30분부터 오후 7시까지의 저녁 뉴스). 라이브 이벤트로 들어오는 스트림이 있으면 자산, 라이브 출력 및 스트리밍 로케이터를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 라이브 출력은 스트림을 보관하고 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints)를 통해 시청자가 스트림을 사용할 수 있도록 합니다. 라이브 이벤트에서 보관 기간 및 설정이 서로 다른 여러 개의 라이브 출력(최대 3개)을 만들 수 있습니다. 라이브 스트리밍 워크플로에 대한 자세한 내용은 [일반 단계](live-streaming-overview.md#general-steps) 섹션을 참조하세요.

## <a name="using-a-dvr-during-an-event"></a>이벤트 동안 DVR 사용

이 섹션에서는 이벤트 동안 DVR을 사용하여 '되감기'에 사용할 수 있는 스트림 부분을 제어하는 방법을 설명합니다.

`archiveWindowLength` 값은 시청자가 현재 라이브 위치에서 탐색하기 위해 되돌아갈 수 있는 시간을 결정합니다. 또한 `archiveWindowLength` 값은 클라이언트 매니페스트가 증가할 수 있는 길이를 결정합니다.

축구 경기를 스트리밍하는 중이며 `ArchiveWindowLength`가 단지 30분이라고 가정하겠습니다. 경기 시작 45분 후에 이벤트를 보기 시작한 시청자는 최대 15분 마크까지 이전으로 탐색할 수 있습니다. 경기 라이브 출력은 라이브 이벤트가 중지될 때까지 계속됩니다. archiveWindowLength를 벗어나는 콘텐츠는 스토리지에서 지속적으로 삭제되며 복구할 수 없습니다. 이 예제에서 이벤트 시작과 15분 마크 사이의 비디오 부분은 DVR은 물론, 자산용 Blob Storage의 컨테이너에서도 삭제되었을 것입니다. 보관 파일은 복구 불가능하며 Azure Blob Storage의 컨테이너에서 제거됩니다.

라이브 이벤트는 동시에 실행되는 최대 세 개의 라이브 출력을 지원합니다(하나의 라이브 스트림에서 최대 3개의 녹화/보관 파일을 동시에 만들 수 있음). 따라서 이벤트의 여러 부분을 필요에 따라 게시하고 보관할 수 있습니다. 연중 무휴 라이브 선형 피드를 브로드캐스트하는 것이지만, 하루 동안의 다른 프로그램 "기록"을 만들어 다시 보기 용도의 주문형 콘텐츠를 고객에게 제공하고 싶습니다. 이 시나리오에서는 먼저 보관 기간이 1시간 이하로 짧은 기본 라이브 출력을 만들며, 이는 나중에 시청자가 선택할 기본 라이브 스트림이 됩니다. 이 라이브 출력에 대한 스트리밍 로케이터를 만들어 앱 또는 웹 사이트에 '라이브' 피드로 게시합니다. 라이브 이벤트가 실행되는 동안 프로그램의 시작 부분에서(또는 나중에 잘라내기 핸들을 제공하기 위해 예정보다 5분 먼저) 두 번째 동시 라이브 출력을 프로그래밍 방식으로 만들 수 있습니다. 이 두 번째 라이브 출력은 프로그램 종료 5분 후에 삭제할 수 있습니다. 이 두 번째 자산을 통해 새 스트리밍 로케이터를 만들어 앱의 카탈로그에서 이 프로그램을 주문형 자산으로 게시할 수 있습니다. 첫 번째 라이브 출력의 "라이브" 피드가 선형 피드를 계속 브로드캐스트하는 동안 주문형 비디오로 공유하려는 다른 프로그램 경계 또는 하이라이트에 대해 이 프로세스를 여러 번 반복할 수 있습니다.

## <a name="creating-an-archive-for-on-demand-playback"></a>주문형 재생을 위한 보관 파일 만들기

라이브 출력이 보관되는 자산은 라이브 출력이 삭제되면 자동으로 주문형 자산이 됩니다. 라이브 이벤트를 중지하려면 먼저 모든 라이브 출력을 삭제해야 합니다. 선택적 플래그 [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body)을 사용하여 중지 시 자동으로 라이브 출력을 제거할 수 있습니다.

이벤트를 중지하고 삭제한 후에도 자산을 삭제하지 않는 한 사용자는 주문형 비디오로 보관된 콘텐츠를 스트리밍할 수 있습니다. 자산을 이벤트에서 사용하는 경우 자산을 삭제할 수 없습니다. 먼저 이벤트를 삭제해야 합니다.

스트리밍 로케이터를 사용하여 라이브 출력 자산을 게시한 경우 스트리밍 로케이터가 만료 또는 삭제될 때까지 라이브 이벤트를 계속 시청할 수 있습니다(DVR 기간까지).

자세한 내용은 다음을 참조하세요.

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)

> [!NOTE]
> 라이브 출력을 삭제해도 기본 자산과 자산의 콘텐츠는 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [비디오 서브클립](subclip-video-rest-howto.md).
* [자산에 대한 필터 정의](filters-dynamic-manifest-rest-howto.md)
