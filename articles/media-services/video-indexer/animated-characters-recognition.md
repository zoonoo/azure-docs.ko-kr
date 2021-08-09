---
title: Video Indexer를 사용한 애니메이션 캐릭터 검색
titleSuffix: Azure Media Services
description: 이 항목에서는 Video Indexer에서 애니메이션 캐릭터 검색을 사용하는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854753"
---
# <a name="animated-character-detection-preview"></a>애니메이션 캐릭터 검색(미리 보기)

Azure Media Services Video Indexer는 [Cognitive Services Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)과의 통합을 통해 애니메이션 콘텐츠에서 캐릭터의 검색과 그룹화, 인식을 지원합니다. 이 기능은 포털 및 API를 통해 사용할 수 있습니다.

특정 애니메이션 모델을 사용하여 애니메이션 비디오를 업로드한 후 Video Indexer는 키 프레임을 추출하고, 해당 프레임에서 애니메이션 캐릭터를 검색하며, 비슷한 캐릭터를 그룹화하고, 최상의 샘플을 선택합니다. 그런 다음, 그룹화된 캐릭터를 학습된 모델을 기반으로 캐릭터를 식별하는 Custom Vision으로 보냅니다. 

모델 학습을 시작하기 전에 캐릭터가 이름 없이 검색됩니다. 이름을 추가하고 모델을 학습하면 Video Indexer가 캐릭터를 인식하고 그에 따라 이름을 지정합니다.

## <a name="flow-diagram"></a>흐름 다이어그램

다음 다이어그램에서는 애니메이션 캐릭터 검색 프로세스의 흐름을 보여 줍니다.

![흐름 다이어그램](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>계정

Video Indexer 계정의 유형에 따라 다양한 기능 집합을 사용할 수 있습니다. Azure에 계정을 연결하는 방법에 대한 자세한 내용은 [Azure에 연결된 Video Indexer 계정 만들기](connect-to-azure.md)를 참조하세요.

* 평가판 계정: Video Indexer는 내부 Custom Vision 계정을 사용하여 모델을 만들고 Video Indexer 계정에 연결합니다. 
* 유료 계정: Custom Vision 계정을 Video Indexer 계정에 연결합니다(아직 없는 경우 먼저 계정을 만들어야 함).

### <a name="trial-vs-paid"></a>평가판 및 유료

|기능|평가판|유료|
|---|---|---|
|Custom Vision 계정|Video Indexer를 통해 백그라운드에서 관리됩니다. |Custom Vision 계정이 Video Indexer에 연결됩니다.|
|애니메이션 모델 수|하나|계정당 최대 100개의 모델(Custom Vision 제한 사항).|
|모델 학습|Video Indexer는 기존 캐릭터에 추가된 예제로 새 캐릭터에 대한 모델을 학습시킵니다.|계정 소유자는 변경할 준비가 되면 모델을 학습시킵니다.|
|Custom Vision의 고급 옵션|Custom Vision 포털에 대한 액세스 권한이 없습니다.|모델은 Custom Vision 포털에서 직접 조정할 수 있습니다.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>포털 및 API에서 애니메이션 캐릭터 검색 사용

자세한 내용은 [포털 및 API에서 애니메이션 캐릭터 검색 사용](animated-characters-recognition-how-to.md)을 참조하세요.

## <a name="limitations"></a>제한 사항

* 현재 “애니메이션 식별” 기능은 동아시아 지역에서 지원되지 않습니다.
* 비디오에서 작거나 멀리 보이는 캐릭터는 비디오 품질이 나쁜 경우 제대로 식별되지 않을 수 있습니다.
* 애니메이션 캐릭터 집합당(예: 애니메이션 시리즈당) 하나의 모델을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)