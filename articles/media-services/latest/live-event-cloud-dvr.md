---
title: 시간 이동 및 라이브 출력을 사용 하 여 주문형 비디오 재생 만들기
titleSuffix: Azure Media Services
description: 시간 이동 및 라이브 출력을 사용 하 여 라이브 스트림을 기록 하 고 주문형 재생을 만드는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: acba251a57f39c07d690d0c55665b8914feaf06c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186238"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>시간 이동 및 라이브 출력을 사용 하 여 주문형 비디오 재생 만들기

Azure Media Services [라이브 출력](https://docs.microsoft.com/rest/api/media/liveoutputs) 개체는 라이브 스트림을 catch 하 고 Media Services 계정의 자산으로 기록 하는 디지털 비디오 레코더와 비슷합니다. 기록 된 콘텐츠는 [자산](https://docs.microsoft.com/rest/api/media/assets) 리소스로 정의 된 컨테이너에 유지 됩니다. 컨테이너는 계정에 연결 된 Azure Storage 계정에 있습니다. 또한 라이브 출력을 사용 하면 보관 기록에 보관 되는 스트림 양 (예: 클라우드 DVR의 용량) 또는 뷰어가 라이브 스트림 감시를 시작할 수 있는 경우와 같이 나가는 라이브 스트림의 일부 속성을 제어할 수 있습니다. 디스크의 보관 파일은 실시간 출력의 **archiveWindowLength** 속성에 지정 된 양의 내용만 보유 하는 순환 보관 "창"입니다. 이 창 외부에 속하는 콘텐츠는 저장소 컨테이너에서 자동으로 삭제 되며 복구할 수 없습니다. ArchiveWindowLength 값은 DVR의 용량을 지정 하는 ISO-8601 timespan 기간 (예: PTHH: MM: SS)을 나타냅니다. 값은 최소 3 분에서 최대 25 시간까지 설정할 수 있습니다.

라이브 이벤트와 라이브 출력 간의 관계는 기존 TV 브로드캐스트와 유사 합니다. 즉, 채널 (라이브 이벤트)은 비디오의 상수 스트림을 나타내고 기록 (라이브 출력)은 특정 시간 세그먼트로 범위가 지정 됩니다 (예:의 저녁 뉴스). 6:30PM ~ 7:00PM). 스트림이 라이브 이벤트로 흐르는 경우 자산, 라이브 출력 및 스트리밍 로케이터를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 라이브 출력은 스트림을 보관하고 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints)를 통해 시청자가 스트림을 사용할 수 있도록 합니다. 보관 길이와 설정이 서로 다른 라이브 이벤트에서 여러 라이브 출력 (최대 3 개)을 만들 수 있습니다. 라이브 스트리밍 워크플로에 대 한 자세한 내용은 [일반 단계](live-streaming-overview.md#general-steps) 섹션을 참조 하세요.

## <a name="using-a-dvr-during-an-event"></a>이벤트 중에 DVR 사용

이 섹션에서는 이벤트 동안 DVR을 사용 하 여 ' 되감기 '에 사용할 수 있는 스트림의 부분을 제어 하는 방법을 설명 합니다.

`archiveWindowLength` 값은 뷰어가 현재 라이브 위치에서 이동할 수 있는 시간을 결정 합니다. 또한 `archiveWindowLength` 값은 클라이언트 매니페스트가 증가할 수 있는 기간을 결정 합니다.

축구 게임을 스트리밍하는 중 이며 30 분의 `ArchiveWindowLength` 있는 경우를 가정 하겠습니다. 경기 시작 45분 후에 이벤트를 보기 시작한 시청자는 최대 15분 마크까지 이전으로 탐색할 수 있습니다. 게임의 라이브 출력은 라이브 이벤트가 중지 될 때까지 계속 됩니다. ArchiveWindowLength 외부에 있는 콘텐츠는 저장소에서 지속적으로 삭제 되며 복구할 수 없습니다. 이 예제에서는 이벤트의 시작과 15 분 표시 사이에 있는 비디오가 DVR에서 제거 되었고 자산의 blob storage에 있는 컨테이너에서 제거 되었습니다. 아카이브는 복구할 수 없으며 Azure blob storage의 컨테이너에서 제거 됩니다.

라이브 이벤트는 동시에 실행 되는 실시간 출력 수를 최대 3 개까지 지원 합니다. 한 라이브 스트림에서 최대 3 개의 기록/아카이브를 동시에 만들 수 있습니다. 이러한 지원을 통해 필요에 따라 이벤트의 다른 부분을 게시 하 고 보관할 수 있습니다. 연중 무휴 라이브 선형 피드를 브로드캐스트하는 것이지만, 하루 동안의 다른 프로그램 "기록"을 만들어 다시 보기 용도의 주문형 콘텐츠를 고객에게 제공하고 싶습니다. 이 시나리오에서는 먼저 1 시간 이내에 짧은 보관 기간을 사용 하 여 기본 실시간 출력을 만듭니다 .이는 뷰어가 튜닝할 주 라이브 스트림입니다. 이 실시간 출력에 대 한 스트리밍 로케이터를 만들고 앱 또는 웹 사이트에 "라이브" 피드로 게시 합니다. 라이브 이벤트가 실행 되는 동안 프로그램의 시작 부분에서 프로그래밍 방식으로 두 번째 동시 실시간 출력을 만들 수 있습니다 (또는 나중에 트리밍하는 일부 핸들을 제공 하기 위해 5 분 일찍). 프로그램이 종료 되 고 5 분 후에이 두 번째 라이브 출력이 삭제 될 수 있습니다. 이 두 번째 자산을 사용 하면 새 스트리밍 로케이터를 만들어이 프로그램을 앱 카탈로그의 주문형 에셋으로 게시할 수 있습니다. 주문형 비디오로 공유 하려는 다른 프로그램 경계 또는 하이라이트에 대해이 프로세스를 여러 번 반복할 수 있지만, 첫 번째 라이브 출력의 "라이브" 피드는 계속 선형 피드를 브로드캐스트합니다.

## <a name="creating-an-archive-for-on-demand-playback"></a>주문형 재생에 대 한 보관 파일 만들기

라이브 출력이 보관 되는 자산은 자동으로 라이브 출력이 삭제 될 때 주문형 자산이 됩니다. 라이브 이벤트를 중지 하려면 먼저 모든 라이브 출력을 삭제 해야 합니다. 선택적 플래그 [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) 를 사용 하 여 중지 시 자동으로 라이브 출력을 제거할 수 있습니다.

이벤트를 중지 하 고 삭제 한 후에도 자산을 삭제 하지 않는 한 사용자는 주문형 비디오로 보관 된 콘텐츠를 스트리밍할 수 있습니다. 자산을 이벤트에서 사용 하는 경우 삭제 해서는 안 됩니다. 이벤트를 먼저 삭제 해야 합니다.

스트리밍 로케이터를 사용 하 여 라이브 출력의 자산을 게시 한 경우 라이브 이벤트 (DVR 창 길이까지)는 스트리밍 로케이터가 만료 또는 삭제 될 때까지 계속 해 서 볼 수 있습니다.

자세한 내용은

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)

> [!NOTE]
> 라이브 출력을 삭제 하면 자산의 기본 자산과 콘텐츠가 삭제 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [비디오를 하위 클립](subclip-video-rest-howto.md)합니다.
* [자산에 대 한 필터를 정의](filters-dynamic-manifest-rest-howto.md)합니다.
