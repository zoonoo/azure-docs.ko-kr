---
title: Video Indexer를 사용한 라이브 스트림 분석
titleSuffix: Azure Media Services
description: 이 문서에서는 Video Indexer를 사용 하 여 라이브 스트림 분석을 수행 하는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 0f34aad4a8590c71f926d12d201f9a614afaa127
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114930"
---
# <a name="live-stream-analysis-with-video-indexer"></a>Video Indexer 사용 하 여 라이브 스트림 분석

Azure Media Services Video Indexer는 비디오 및 오디오 파일에서 상세 정보를 오프 라인으로 추출 하기 위해 설계 된 Azure 서비스입니다. 이는 미리 만든 지정 된 미디어 파일을 분석 하기 위한 것입니다. 그러나 일부 사용 사례의 경우 사용 중인 운영 및 기타 사용 사례의 잠금을 해제 하려면 가능한 빨리 라이브 피드의 미디어 정보를 얻는 것이 중요 합니다. 예를 들어 라이브 스트림에서 이러한 풍부한 메타 데이터를 사용 하 여 TV 프로덕션을 자동화할 수 있습니다. 예를 들어 [Endemol](https://customers.microsoft.com/story/esg-media-telecommunications-azure)은 매스컴의 저널리스트가 콘텐츠를 기반으로 notification services를 빌드하기 위해 실시간 피드를 검색 하는 것을 말합니다.

이 문서에서 설명 하는 솔루션을 통해 고객은 실시간 피드에서 거의 실시간 해상도로 Video Indexer을 사용할 수 있습니다. 인덱싱 지연에는이 솔루션을 사용 하는 데 4 분 정도 걸릴 수 있습니다 .이 솔루션은 인덱싱되는 데이터의 청크, 입력 해상도, 콘텐츠 형식 및이 프로세스에 사용 되는 계산의 양에 따라 달라 집니다.

![라이브 스트림의 Video Indexer 메타 데이터](./media/live-stream-analysis/live-stream-analysis01.png)

*그림 1-라이브 스트림에 Video Indexer 메타 데이터를 표시 하는 샘플 플레이어*

현재 [스트림 분석 솔루션](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/blob/master/media-functions-for-logic-app/LiveStreamAnalysis.md) 은 Azure Functions 및 두 개의 Logic Apps를 사용 하 여 Video Indexer를 사용 하 여 Azure Media Services 라이브 채널에서 라이브 프로그램을 처리 하 고 거의 실시간으로 생성 된 스트림을 보여 주는 Azure Media Player 결과를 표시 합니다.

상위 수준에서는 두 개의 주요 단계로 구성 됩니다. 첫 번째 단계는 60 초 마다 실행 되 고, 지난 60 초의 하위 클립을 가져와, 자산을 만들고 Video Indexer를 통해 인덱싱합니다. 그런 다음 인덱싱이 완료 되 면 두 번째 단계가 호출 됩니다. 캡처한 정보는 처리 되어 Azure Cosmos DB 전송 되 고 인덱스는 삭제 됩니다.

샘플 플레이어는 라이브 스트림을 재생 하 고 전용 Azure 함수를 사용 하 여 Azure Cosmos DB에서 정보를 가져옵니다. 라이브 비디오와 동기화 된 메타 데이터 및 미리 보기를 표시 합니다.

![클라우드에서 1 분 마다 라이브 스트림을 처리 하는 두 개의 논리 앱](./media/live-stream-analysis/live-stream-analysis02.png)

*그림 2-클라우드에서 1 분 마다 라이브 스트림을 처리 하는 두 개의 논리 앱입니다.*

## <a name="step-by-step-guide"></a>단계별 가이드 

결과를 배포 하기 위한 전체 코드와 단계별 가이드는 [Video Indexer 있는 라이브 미디어 분석에 대 한 GitHub 프로젝트](https://aka.ms/livestreamanalysis)에서 찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)
