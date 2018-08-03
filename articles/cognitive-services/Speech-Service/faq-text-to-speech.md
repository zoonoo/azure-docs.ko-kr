---
title: Azure의 음성 텍스트 변환 서비스에 대한 질문과 대답 | Microsoft Docs
description: 다음은 음성 텍스트 변환에 대해 가장 자주 묻는 질문에 대한 답변입니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 4a29435c0ace79fc3a5d3a5a42a0e91bdbc8da5e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082827"
---
# <a name="text-to-speech-frequently-asked-questions"></a>텍스트 음성 변환에 대한 질문과 대답

여기 FAQ에서 질문에 대한 답변을 찾을 수 없는 경우에는 [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive)와 [UserVoice](https://cognitive.uservoice.com/)에서 Custom Speech Service 커뮤니티에 문의해 보세요.

## <a name="general"></a>일반

**질문**: 표준 음성 모델과 사용자 지정 음성 모델의 차이는 무엇인가요?

**답변**: 표준 음성 모델(즉, 음성 글꼴)은 Microsoft가 소유한 데이터로 학습이 되었으며 클라우드에 이미 배포되어 있습니다. 사용자 지정 음성 모델을 사용하면 평균 모델을 적응시키고, 화자의 음성 스타일에 따라 음색 및 표현 방식을 전달하거나, 사용자가 준비한 학습 데이터를 기준으로 완전히 새로운 모델을 학습할 수 있습니다. 오늘날 점점 더 많은 고객이 봇에 한 가지 종류의 브랜드 음성을 사용하기 원합니다. 이러한 경우 사용자 지정 음성 빌드 플랫폼이 안성맞춤입니다.

**질문**: 표준 모델을 사용하려면 무엇부터 시작해야 하나요?

**답변**: 45개 이상의 언어로 제공되는 80개 이상의 표준 음성 모델을 HTTP 요청을 통해 사용할 수 있습니다. 우선 [구독 키](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started)를 확보해야 합니다. 미리 배포된 음성 모델에 대해 REST 호출을 수행하려면, [여기에서 자세한 내용](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech)을 참조하세요.

**질문**: 사용자 지정된 음성 모델을 사용하려는 경우 해당 API가 표준 음성 모델의 경우와 동일한가요?

**답변**: 사용자 지정 음성 모델을 만들고 배포한 경우, 모델에 대한 고유한 끝점을 받게 됩니다. 앱에서 해당 음성을 사용해서 말하도록 하기 위해 HTTP 요청에 끝점을 지정해야 합니다. Text-to-Speech 서비스에 대해 REST API를 통해 사용할 수 있는 동일한 기능을 사용자 지정 끝점에도 사용할 수 있습니다. [사용자 지정 끝점을 만들고 사용하는](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint) 방법을 참조하세요.

**질문**: 사용자 지정 음성 모델을 만들기 위한 학습 데이터를 직접 준비해야 하나요?

**답변**: 학습 데이터는 직접 준비해야 합니다. 사용자 지정된 음성 모델을 만들려면 음성 데이터 컬렉션이 필요합니다. 이 컬렉션은 음성 녹음의 오디오 파일 집합과 각 오디오 파일의 기록에 대한 텍스트 파일로 구성됩니다. 디지털 음성의 결과는 학습 데이터의 품질을 크게 의존합니다. 좋은 TTS 음성을 생성하려면 고품질의 스탠딩 마이크를 사용하여 조용한 방에서 녹음하는 것이 중요합니다. 일관된 볼륨, 읽기 속도, 읽기 높낮이 및 음성의 표현 매너리즘 일관성은 훌륭한 디지털 음성을 작성하는 데 필수적입니다. 녹음 스튜디오에서 음성을 녹음할 것을 강력히 권장합니다.
현재, 온라인 녹음 지원을 제공하고 있지 않으며 권장되는 녹음 스튜디오 정보도 제공하고 있지 않습니다. 형식 요구 사항에 대해서는 [녹음 및 기록 준비 방법](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)을 참조하세요.
 
**질문**: 사용자 지정 음성 학습을 위한 음성 데이터를 녹음할 때 어떤 스크립트를 사용해야 하나요? 

**답변**: 음성 녹음용 스크립트에는 제한이 없습니다. 직접 작성한 스크립트를 사용하여 음성을 녹음할 수 있습니다. 음성 데이터의 음성 적용 범위만 충분하면 됩니다. 사용자 지정 음성을 학습하기 위해 처음에는 50개 정도의 다른 문장(읽는 데 3~5분 소요)을 포함하는 소량의 음성 데이터로 시작할 수 있습니다. 더 많은 데이터를 제공할수록 음성이 좀 더 자연스러워집니다. 2,000개가 넘는 문장(읽는 데 약 3~4시간 소요)을 녹음해서 제공하면 전체 음성 글꼴을 학습할 수 있습니다. 고품질의 전체 음성을 얻으려면 6,000개가 넘는 문장(읽는 데 약 8~10시간 소요)을 녹음해서 준비해야 합니다.  
녹음 스크립트를 준비하는 데 도움이 되는 추가 서비스가 제공됩니다. 질문이 있으면 [Custom Voice 고객 지원 센터](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)에 문의하세요.

**질문**: 기본값 또는 포털에 제공되는 값보다 높은 동시성이 필요하면 어떻게 해야 하나요?

**답변**: 모델은 동시 요청 20건씩 강화할 수 있습니다. 확장에 대한 질문이 있으면 [Custom Voice 고객 지원 센터](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)에 문의하세요.

**질문**: 모델을 다운로드하여 로컬에서 실행할 수 있나요?

**답변**: 모델은 로컬에 다운로드하여 실행할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [문제 해결](troubleshooting.md)
* [릴리스 정보](releasenotes.md)
