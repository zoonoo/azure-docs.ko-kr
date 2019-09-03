---
title: Azure Media Services 라이브 이벤트 및 클라우드 DVR | Microsoft Docs
description: 이 문서에서는 라이브 출력의 정의와 클라우드 DVR 사용 방법에 대해 설명합니다.
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
ms.openlocfilehash: a10c76dd7fb4ef1e9a45666ff3a3ca0d937d2c94
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231217"
---
# <a name="using-a-cloud-digital-video-recorder-dvr"></a>클라우드 DVR (디지털 비디오 레코더) 사용

Azure Media Services [라이브 출력](https://docs.microsoft.com/rest/api/media/liveoutputs) 개체는 라이브 스트림을 catch 하 고 Media Services 계정의 자산으로 기록 하는 디지털 비디오 레코더와 비슷합니다. 기록 된 콘텐츠는 [자산](https://docs.microsoft.com/rest/api/media/assets) 리소스로 정의 된 컨테이너에 유지 됩니다. 컨테이너는 계정에 연결 된 Azure Storage 계정에 있습니다. 또한 라이브 출력을 사용 하면 보관 기록에 보관 되는 스트림 양 (예: 클라우드 DVR의 용량) 및 뷰어가 라이브 스트림 감시를 시작할 수 있는지 여부와 같은 나가는 라이브 스트림의 일부 속성을 제어할 수 있습니다. 디스크의 보관 파일은 실시간 출력의 **archiveWindowLength** 속성에 지정 된 양의 내용만 보유 하는 순환 보관 "창"입니다. 이 기간을 벗어나는 콘텐츠는 스토리지 컨테이너에서 자동으로 삭제되며 복구할 수 없습니다. ArchiveWindowLength 값은 DVR의 용량을 지정 하는 ISO-8601 timespan 기간 (예: PTHH: MM: SS)을 나타내며 최소 3 분에서 최대 25 시간까지 설정할 수 있습니다.

라이브 이벤트와 라이브 출력 간의 관계는 기존의 텔레비전 브로드캐스트와 유사 합니다. 즉, 채널 (라이브 이벤트)이 비디오의 일정 스트림을 나타내고 기록 (라이브 출력)의 범위는 특정 시간 세그먼트로 지정 됩니다 (예: 야간 6:30PM부터 7:00PM 까지의 뉴스. 스트림이 라이브 이벤트로 흐르는 경우 자산, 라이브 출력 및 스트리밍 로케이터를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 라이브 출력은 스트림을 보관 하 고 [스트리밍 끝점](https://docs.microsoft.com/rest/api/media/streamingendpoints)을 통해 뷰어에 사용할 수 있도록 합니다. 보관 길이와 설정이 서로 다른 라이브 이벤트에서 여러 라이브 출력 (최대 3 개)을 만들 수 있습니다. 라이브 스트리밍 워크플로에 대 한 자세한 내용은 [일반 단계](live-streaming-overview.md#general-steps) 섹션을 참조 하세요.

## <a name="using-a-dvr-during-an-event"></a>이벤트 중에 DVR 사용 

이 섹션에서는 이벤트 동안 DVR을 사용 하 여 ' 되감기 '에 사용할 수 있는 스트림의 부분을 제어 하는 방법을 설명 합니다.

ArchiveWindowLength 값은 뷰어가 현재 라이브 위치에서 검색할 수 있는 시간을 결정 합니다. 또한 archiveWindowLength 값은 클라이언트 매니페스트가 증가할 수 있는 기간을 결정 합니다.

축구 게임을 스트리밍하는 중 이며 30 분 ArchiveWindowLength 있습니다. 경기 시작 45분 후에 이벤트를 보기 시작한 시청자는 최대 15분 마크까지 이전으로 탐색할 수 있습니다. 게임의 라이브 출력은 라이브 이벤트가 중지 될 때까지 계속 되지만 archiveWindowLength 외부에 있는 콘텐츠는 저장소에서 지속적으로 삭제 되 고 복구할 수 없습니다. 이 예제에서는 이벤트의 시작과 15 분 표시 사이에 있는 비디오가 DVR에서 제거 되었고 자산의 blob storage에 있는 컨테이너에서 제거 되었습니다. 보관 파일은 복구 불가능하며 Azure Blob Storage의 컨테이너에서 제거됩니다.

라이브 이벤트는 동시에 실행 되는 실시간 출력 수를 최대 3 개까지 지원 합니다. 한 라이브 스트림에서 최대 3 개의 기록/아카이브를 동시에 만들 수 있습니다. 따라서 이벤트의 여러 부분을 필요에 따라 게시하고 보관할 수 있습니다. 연중 무휴 라이브 선형 피드를 브로드캐스트하는 것이지만, 하루 동안의 다른 프로그램 "기록"을 만들어 다시 보기 용도의 주문형 콘텐츠를 고객에게 제공하고 싶습니다. 이 시나리오에서는 먼저 보관 기간이 1시간 이하로 짧은 기본 라이브 출력을 만들며, 이는 나중에 시청자가 튜닝할 기본 라이브 스트림이 됩니다. 이 실시간 출력에 대 한 스트리밍 로케이터를 만들고 응용 프로그램 또는 웹 사이트에 "라이브" 피드로 게시 합니다. 라이브 이벤트가 실행 되는 동안 프로그램의 시작 부분에서 프로그래밍 방식으로 두 번째 동시 실시간 출력을 만들 수 있습니다 (또는 나중에 트리밍하는 일부 핸들을 제공 하기 위해 5 분 일찍). 프로그램이 종료 되 고 5 분 후에이 두 번째 라이브 출력이 삭제 될 수 있습니다. 이 두 번째 자산에서는 새 스트리밍 로케이터를 만들어이 프로그램을 응용 프로그램 카탈로그의 주문형 에셋으로 게시할 수 있습니다. 주문형 비디오로 공유 하려는 다른 프로그램 경계 또는 하이라이트에 대해이 프로세스를 여러 번 반복할 수 있지만, 첫 번째 라이브 출력의 "라이브" 피드는 계속 선형 피드를 브로드캐스트합니다. 

## <a name="creating-an-archive-for-on-demand-playback"></a>주문형 재생에 대 한 보관 파일 만들기

라이브 출력이 보관 되는 자산은 라이브 출력이 삭제 될 때 자동으로 주문형 자산이 됩니다. 라이브 이벤트를 중지 하려면 먼저 모든 라이브 출력을 삭제 해야 합니다. 선택적 플래그 [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) 를 사용 하 여 중지 시 자동으로 라이브 출력을 제거할 수 있습니다. 

이벤트를 중지 하 고 삭제 한 후에도 자산을 삭제 하지 않는 한 사용자는 주문형 비디오로 보관 된 콘텐츠를 스트리밍할 수 있습니다. 자산을 이벤트에서 사용 하는 경우 삭제 하면 안 됩니다. 이벤트를 먼저 삭제 해야 합니다.

스트리밍 로케이터를 사용 하 여 라이브 출력의 자산을 게시 한 경우 라이브 이벤트 (DVR 창 길이까지)는 스트리밍 로케이터의 만료 또는 삭제 중 어느 쪽이 든 먼저 도달할 때까지 계속 볼 수 있습니다.

자세한 내용은 다음을 참조하세요.

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)

> [!NOTE]
> 라이브 출력을 삭제 하면 자산의 기본 자산과 콘텐츠가 삭제 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

* [비디오를 하위 클립](subclip-video-rest-howto.md)합니다.
* [자산에 대 한 필터를 정의](filters-dynamic-manifest-rest-howto.md)합니다.
