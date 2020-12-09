---
title: Video Indexer를 사용한 애니메이션 문자 검색
titleSuffix: Azure Media Services
description: 이 항목에서는 Video Indexer에서 애니메이션 문자 검색을 사용 하는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854753"
---
# <a name="animated-character-detection-preview"></a>애니메이션 캐릭터 검색(미리 보기)

Azure Media Services Video Indexer는 [Cognitive Services 사용자 지정 비전과](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)의 통합을 통해 애니메이션 내용에서 문자 검색, 그룹화 및 인식을 지원 합니다. 이 기능은 포털 및 API를 통해 사용할 수 있습니다.

특정 애니메이션 모델을 사용 하 여 애니메이션 비디오를 업로드 한 후에는 키 프레임을 추출 하 고, 이러한 프레임에서 애니메이션 문자를 검색 하 고, 비슷한 문자를 그룹화 하 고, 최상의 샘플을 선택 Video Indexer 그런 다음 그룹화 된 문자를 학습 된 모델을 기반으로 문자를 식별 하는 Custom Vision 보냅니다. 

모델에 대 한 학습을 시작 하기 전에 문자가 namelessly 검색 됩니다. 이름을 추가 하 고 모델을 학습 하면 Video Indexer는 문자를 인식 하 고 그에 따라 이름을 적절 하 게 합니다.

## <a name="flow-diagram"></a>흐름 다이어그램

다음 다이어그램에서는 애니메이션 문자 검색 프로세스의 흐름을 보여 줍니다.

![흐름 다이어그램](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>계정

Video Indexer 계정의 유형에 따라 다양 한 기능 집합을 사용할 수 있습니다. Azure에 계정을 연결 하는 방법에 대 한 자세한 내용은 [azure에 연결 된 Video Indexer 계정 만들기](connect-to-azure.md)를 참조 하세요.

* 평가판 계정: Video Indexer 내부 Custom Vision 계정을 사용 하 여 모델을 만들고 Video Indexer 계정에 연결 합니다. 
* 유료 계정: Video Indexer 계정에 Custom Vision 계정을 연결 합니다 (아직 없는 경우 계정을 먼저 만들어야 함).

### <a name="trial-vs-paid"></a>평가판 및 유료

|기능|평가판|유료|
|---|---|---|
|Custom Vision 계정|Video Indexer 하 여 백그라운드에서 관리 됩니다. |Custom Vision 계정이 Video Indexer에 연결 되어 있습니다.|
|애니메이션 모델 수|하나|계정 당 최대 100 모델 (Custom Vision 제한).|
|모델 학습|Video Indexer 새 문자에 대 한 모델을 학습 하 여 기존 문자의 추가 예를 보여 줍니다.|계정 소유자는 변경을 수행할 준비가 되 면 모델을 학습 합니다.|
|Custom Vision 고급 옵션|Custom Vision 포털에 대 한 액세스 권한이 없습니다.|모델은 Custom Vision 포털에서 직접 조정할 수 있습니다.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>포털 및 API에서 애니메이션 문자 검색 사용

자세한 내용은 [포털 및 API에서 애니메이션 문자 검색 사용](animated-characters-recognition-how-to.md)을 참조 하세요.

## <a name="limitations"></a>제한 사항

* 현재 "애니메이션 식별" 기능은 East-Asia 지역에서 지원 되지 않습니다.
* 비디오에서 작거나 멀리 보이는 문자는 비디오 품질이 나쁜 경우 제대로 식별 되지 않을 수 있습니다.
* 애니메이션 효과가 적용 되는 일련의 문자 집합에 따라 모델을 사용 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)