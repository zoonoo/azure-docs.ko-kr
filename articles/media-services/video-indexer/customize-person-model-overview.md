---
title: Video Indexer에서 개인 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 개인 모델의 개념과 이를 사용자 지정하는 방법을 간략하게 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283239"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Video Indexer에서 개인 모델 사용자 지정


Video Indexer는 비디오 콘텐츠에 대해 얼굴 감지 및 유명인 인식 기능을 지원합니다. 유명인 인식 기능은 IMDB, Wikipedia, 상위 LinkedIn 영향력 행사자 등 일반적으로 요청되는 데이터 원본을 기준으로 하는 약 1,000,000개의 얼굴을 처리합니다. 유명인 인식 기능으로 인식되지 않는 얼굴은 감지되지만 이름 없이 유지됩니다. Video Indexer에 비디오를 업로드하고 결과를 얻은 후 돌아가서 인식되지 않은 얼굴에 이름을 지정할 수 있습니다. 얼굴에 이름 레이블을 지정하면 얼굴과 이름이 계정의 개인 모델에 추가됩니다. 그러면 Video Indexer가 향후 비디오 및 이전 비디오에서 이 얼굴을 인식합니다.

Video Indexer 웹 사이트 또는 API를 사용하여 다음 항목에 설명된 대로 계정의 비디오에서 감지된 얼굴을 편집할 수 있습니다.

- [API를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)
- [를 사용하여 개인 모델 사용자 지정](customize-person-model-with-website.md)
