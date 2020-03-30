---
title: Video Indexer에서 개인 모델 사용자 지정 - Azure
titleSuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 개인 모델의 개념과 이를 사용자 지정하는 방법을 간략하게 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838307"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Video Indexer에서 개인 모델 사용자 지정

비디오 인덱서는 동영상에서 유명 인사 인식을 지원합니다. 유명인 인식 기능은 IMDB, Wikipedia, 상위 LinkedIn 영향력 행사자 등 일반적으로 요청되는 데이터 원본을 기준으로 하는 약 백만 개의 얼굴을 처리합니다. 비디오 인덱서에서 인식하지 못하는 얼굴은 여전히 감지되지만 이름은 지정되지 않은 상태로 남아 있습니다. 고객은 사용자 지정 Person 모델을 빌드하고 비디오 인덱서를 사용하여 기본적으로 인식되지 않는 얼굴을 인식할 수 있습니다. 고객은 사람의 이름과 얼굴의 이미지 파일을 페어링하여 이러한 사람 모델을 작성할 수 있습니다.  

계정이 다른 사용 사례를 충족시키는 경우 계정당 여러 개인 모델을 만들 수 있다는 이점을 얻을 수 있습니다. 예를 들어 계정의 콘텐츠가 다른 채널로 정렬되어야 하는 경우 각 채널에 대해 별도의 Person 모델을 만들 수 있습니다. 

> [!NOTE]
> 각 Person 모델은 최대 100만 명을 지원하며 각 계정에는 50명 모델로 제한됩니다. 

모델이 생성되면, 비디오를 업로드/인덱싱하거나 다시 인덱싱할 때 특정 개인 모델의 모델 ID를 제공하여 모델을 사용할 수 있습니다. 비디오에 대한 새 얼굴을 교육하고 비디오가 연관된 특정 사용자 지정 모델을 업데이트합니다. 

다중 개인 모델 지원이 필요하지 않은 경우, 업로드/인덱싱하거나 다시 인덱싱할 때 비디오에 개인 모델 ID를 할당하지 마세요. 이 경우 비디오 인덱서에서 계정의 기본 사람 모델을 사용합니다. 

비디오 인덱서 웹 사이트를 사용하여 동영상에서 감지된 얼굴을 편집하고 웹 사이트 주제를 사용하여 사용자 [지정 모델에](customize-person-model-with-website.md) 설명된 대로 계정에서 여러 사용자 지정 사람 모델을 관리할 수 있습니다.  [API를 사용하여 사람 모델 사용자 지정에](customize-person-model-with-api.md)설명된 대로 API를 사용할 수도 있습니다.
