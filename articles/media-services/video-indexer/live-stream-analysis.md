---
title: 비디오 인덱서를 사용한 실시간 스트림 분석
titleSuffix: Azure Media Services
description: 이 문서에서는 비디오 인덱서를 사용하여 실시간 스트림 분석을 수행하는 방법을 보여 주며, 이 문서에서는 이러한 실시간 스트림 분석을 수행합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185989"
---
# <a name="live-stream-analysis-with-video-indexer"></a>비디오 인덱서를 사용 하 고 라이브 스트림 분석

Azure 미디어 서비스 비디오 인덱러는 오프라인으로 비디오 및 오디오 파일에서 심층적인 통찰력을 추출하도록 설계된 Azure 서비스입니다. 이는 이미 미리 생성된 지정된 미디어 파일을 분석하기 위한 것입니다. 그러나 일부 사용 사례의 경우 가능한 한 빨리 라이브 피드에서 미디어 통찰력을 얻고 운영 및 기타 사용 사례를 제 시간에 압축해제하는 것이 중요합니다. 예를 들어 라이브 스트림의 풍부한 메타데이터를 콘텐츠 제작자가 TV 제작을 자동화하는 데 사용할 수 있습니다.

이 문서에 설명된 솔루션을 사용하면 고객이 라이브 피드에서 거의 실시간으로 비디오 인덱서를 사용할 수 있습니다. 인덱싱되는 데이터의 청크, 입력 해상도, 콘텐츠 유형 및 이 프로세스에 사용되는 컴퓨팅 에 따라 이 솔루션을 사용하여 인덱싱 지연이 최대 4분 정도 지연될 수 있습니다.

![라이브 스트림의 비디오 인덱서 메타데이터](./media/live-stream-analysis/live-stream-analysis01.png)

*그림 1 - 라이브 스트림에 비디오 인덱서 메타데이터를 표시하는 샘플 플레이어*

현재 [스트림 분석 솔루션은](https://aka.ms/livestreamanalysis) Azure Functions 및 두 개의 논리 앱을 사용하여 비디오 인덱서를 사용하여 Azure Media Services의 라이브 채널에서 라이브 프로그램을 처리하고 거의 실시간 결과 스트림을 표시하는 Azure Media Player를 사용하여 결과를 표시합니다.

높은 수준에서, 그것은 두 가지 주요 단계로 구성되어 있습니다. 첫 번째 단계는 60초마다 실행되고 재생된 마지막 60초의 서브클립을 가져와서 저작물을 만들고 비디오 인덱서를 통해 색인을 생성합니다. 그런 다음 인덱싱이 완료되면 두 번째 단계가 호출됩니다. 캡처된 인사이트는 처리되고 Azure Cosmos DB로 전송되고 인덱싱된 하위 클립은 삭제됩니다.

샘플 플레이어는 전용 Azure 함수를 사용하여 라이브 스트림을 재생하고 Azure Cosmos DB에서 통찰력을 얻습니다. 라이브 비디오와 동기화된 메타데이터 및 미리보기 이미지를 표시합니다.

![클라우드에서 매 분마다 라이브 스트림을 처리하는 두 개의 논리 앱](./media/live-stream-analysis/live-stream-analysis02.png)

*그림 2 - 클라우드에서 매분 마다 라이브 스트림을 처리하는 두 개의 논리 앱입니다.*

## <a name="step-by-step-guide"></a>단계별 가이드 

전체 코드 및 결과를 배포하는 단계별 가이드는 비디오 [인덱서를 사용하여 라이브 미디어 분석을 위한 GitHub 프로젝트에서](https://aka.ms/livestreamanalysis)찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)
