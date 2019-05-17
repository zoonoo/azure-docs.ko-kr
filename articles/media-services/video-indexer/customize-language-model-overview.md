---
title: Video Indexer에서 언어 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 언어 모델의 개념과 이를 사용자 지정하는 방법을 간략하게 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 516ecd8842e7b673201cc640b283c081a02d2b2f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799554"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Video Indexer로 언어 모델 사용자 지정

Video Indexer는 Microsoft [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/)와 통합을 통해 자동 음성 인식을 지원합니다. 엔진을 적응시킬 어휘가 포함된 도메인의 텍스트인 적응 텍스트를 업로드하여 언어 모델을 사용자 지정할 수 있습니다. 모델을 학습시키면 적응 텍스트에 나타나는 새 단어가 인식되어 기본 발음으로 간주되며 언어 모델은 단어의 새로운 가능한 시퀀스를 학습합니다. 사용자 지정 언어 모델은 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어 (간체), 일본어, 러시아어, 포르투갈어 (브라질), 힌디어, 한국어에 대 한 지원 됩니다. 

예제로 “Kubernetes”(Azure Kubernetes Service의 컨텍스트에서)와 같이 매우 구체적인 단어를 사용해 보겠습니다. 해당 단어는 Video Indexer에 처음 사용되므로 “커뮤니티”로 인식됩니다. 모델을 “Kubernetes”로 인식하려면 모델을 학습시켜야 합니다. 다른 경우에는 단어가 있지만 언어 모델은 이러한 단어가 특정 컨텍스트에서 나타날 것이라고 예상하지 않습니다. 예를 들어 “컨테이너 서비스”는 특수화되지 않은 언어 모델이 특정 단어 세트로 인식하는 2단어 시퀀스가 아닙니다.

텍스트 파일의 목록에서 컨텍스트 없이 단어를 업로드할 수 있습니다. 이는 부분 적응으로 간주합니다. 또는 더 나은 적응을 위해 콘텐츠에 관련된 문서 또는 문장의 텍스트 파일을 업로드할 수 있습니다.

이 항목의 [다음 단계](#next-steps) 섹션에 있는 항목에서 설명한 대로 Video Indexer API 또는 웹 사이트를 사용하여 사용자 지정 언어 모델을 만들고 편집할 수 있습니다.

## <a name="best-practices-for-custom-language-models"></a>사용자 지정 언어 모델에 대한 모범 사례

Video Indexer는 단어 조합의 가능성을 기반으로 학습하므로 가장 잘 학습합니다.

* 문장을 말할 때 충분히 실제적인 문장의 예제를 제공하세요.
* 한 줄에 한 문장만 넣으세요. 그렇지 않으면 시스템이 문장 전체에서 가능성을 학습합니다.
* 한 단어를 문장으로 넣어 다른 단어보다 강조할 수 있지만, 시스템은 전체 문장에서 가장 잘 학습합니다.
* 새 단어나 약어를 도입할 때 가능하면 전체 문장에서 많은 사용 예제를 제공하여 가능한 한 많은 컨텍스트를 시스템에 제공하세요.
* 여러 가지 적응 옵션을 넣고 이러한 옵션의 작동 방식을 확인해 보세요.
* 정확히 동일한 문장을 여러 번 반복하지 마세요. 나머지 입력에 대한 바이어스가 생길 수 있습니다.
* 삭제되므로 특수 기호(~, # @ % &)를 포함하지 마세요. 해당 기호가 나타나는 문장도 삭제됩니다.
* 수십만 개의 문장과 같이 너무 많은 입력을 넣지 마세요. 이렇게 하면 강조 효과가 약화됩니다.

## <a name="next-steps"></a>다음 단계

[API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)

[웹 사이트를 사용하여 언어 모델 사용자 지정](customize-language-model-with-website.md)
