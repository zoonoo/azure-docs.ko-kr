---
title: Azure Media Services LiveEvent 및 클라우드 DVR | Microsoft Docs
description: 이 문서는 LiveOutput의 정의와 클라우드 DVR 사용 방법에 대해 설명합니다.
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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 5de7496d73ebe1c89ce27ef27df73b197f34e7c7
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52636988"
---
# <a name="using-a-cloud-dvr"></a>클라우드 DVR 사용

[LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs)을 통해 기록되는 스트림 양(예: 클라우드 DVR의 용량), 시청자가 라이브 스트림을 보기 시작할 수 있는지 여부 등, 보내는 라이브 스트림의 속성을 제어할 수 있습니다. **LiveEvent**와 **LiveOutput** 간 관계는 채널(**LiveEvent**)이 일정한 비디오 스트림을 나타내고 기록(**LiveOutput**)이 특정 시간 구간으로 한정(예: 오후 6시 30분부터 7시까지 저녁 뉴스)된다는 점에서 기존 TV 방송과 유사합니다. 디지털 비디오 레코더(DVR)를 사용하여 TV를 기록할 수 있으며, LiveEvent에서는 이와 동등한 기능이 ArchiveWindowLength 속성을 통해 관리됩니다. 이 속성은 DVR의 용량을 지정하는 ISO-8601 타임스팬 기간(예: PTHH:MM:SS)이며, 최소 3분에서 최대 25시간까지 설정할 수 있습니다.

## <a name="liveoutput"></a>LiveOutput

**ArchiveWindowLength** 값은 시청자가 현재 라이브 위치에서 탐색하기 위해 되돌아갈 수 있는 시간을 결정합니다.  또한 **ArchiveWindowLength**는 클라이언트 매니페스트가 증가할 수 있는 길이를 결정합니다.

축구 경기를 스트리밍하고 있고 **ArchiveWindowLength**가 단 30분이라고 가정해 보세요. 경기 시작 45분 후에 이벤트를 보기 시작한 시청자는 최대 15분 마크까지 이전으로 탐색할 수 있습니다. 경기의 **LiveOutput**은 **LiveEvent**가 멈출 때까지 계속되지만, **ArchiveWindowLength**를 벗어난 콘텐츠는 스토리지에서 연속적으로 폐기되며 복구 불가능합니다. 이 예제에서 이벤트 시작과 15분 마크 사이의 동영상 부분은 DVR은 물론, [자산](https://docs.microsoft.com/rest/api/media/assets)용 Blob Storage의 컨테이너에서도 삭제되었을 것입니다. 보관 파일은 복구 불가능하며 Azure Blob Storage의 컨테이너에서 제거됩니다.

각 **LiveOutput**은 비디오를 Azure Blob Storage의 컨테이너로 기록하는 데 사용하는 **자산**과 연결되어 있습니다. LiveOutput을 게시하려면 해당 **자산**에 대한 **StreamingLocator**를 만들어야 합니다. [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만든 후에는 시청자에게 제공할 수 있는 스트리밍 URL을 빌드할 수 있습니다.

**LiveEvent**는 동시 실행 **LiveOutput**을 최대 세 개까지 지원하므로 하나의 라이브 스트림에서 최대 3개의 기록/보관 파일을 만들 수 있습니다. 따라서 이벤트의 여러 부분을 필요에 따라 게시하고 보관할 수 있습니다. 연중 무휴 라이브 선형 피드를 브로드캐스트하는 것이지만, 하루 동안의 다른 프로그램 "기록"을 만들어 다시 보기 용도의 주문형 콘텐츠를 고객에게 제공하고 싶습니다. 이 시나리오에서는 먼저 시청자가 이 기본 라이브 스트림으로 튜닝할 수 있도록 1시간 이하의 짧은 보관 창을 사용하여 기본 LiveOutput을 만듭니다. 이 **LiveOutput**용 **StreamingLocator**를 만들어 애플리케이션 또는 웹 사이트에 “라이브” 피드로 게시합니다. **LiveEvent**가 실행되는 동안 쇼 시작 부분에 두 번째 동시 **LiveOutput**을 프로그래밍 방식으로 만들 수 있습니다(또는 나중에 잘라낼 핸들을 제공하기 위해 5분 일찍). 이 두 번째 **LiveOutput**은 프로그램 종료 5분 후에 삭제할 수 있습니다. 이 두 번째 **자산**을 통해 새 **StreamingLocator**를 만들어 애플리케이션의 카탈로그에서 주문형 자산으로 이 프로그램을 게시할 수 있습니다. 첫 번째 **LiveOutput**의 “라이브” 피드가 선형 피드를 계속 브로드캐스트하는 동안, 주문형 비디오로 공유하려는 다른 프로그램 경계 또는 하이라이트에 대해 이 프로세스를 여러 번 반복할 수 있습니다. 

> [!NOTE]
> **LiveOutput**은 생성 시 시작하고 삭제되면 중지합니다. **LiveOutput**을 삭제할 경우 기본 **자산**과 자산의 콘텐츠는 삭제되지 않습니다.  

## <a name="next-steps"></a>다음 단계

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)

