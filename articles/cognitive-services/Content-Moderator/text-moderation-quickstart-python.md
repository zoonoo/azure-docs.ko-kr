---
title: '빠른 시작: Python에서 텍스트 콘텐츠 분석 - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Python용 Content Moderator SDK를 사용하여 다양하고 불쾌한 자료에 대한 텍스트 콘텐츠를 분석하는 방법
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 6aace9301050877d452b69bd7d1c741f50dae558
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264096"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>빠른 시작: Python에서 불쾌한 자료에 대한 텍스트 콘텐츠 분석

이 문서에서는 Python용 Content Moderator SDK를 사용하여 시작할 수 있도록 지원하는 정보 및 코드 샘플을 제공합니다. 잠재적으로 불쾌한 자료를 조정할 목적으로 텍스트 콘텐츠의 용어 기반 필터링 및 분류를 실행하는 방법을 배웁니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="prerequisites"></a>필수 조건
- Content Moderator 구독 키. [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Content Moderator를 구독하고 키를 가져옵니다.
- [Python 2.7+ 또는 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 도구

## <a name="get-the-content-moderator-sdk"></a>Content Moderator SDK 가져오기

명령 프롬프트를 열고 다음 명령을 실행하여 Content Moderator Python SDK를 설치합니다.

```
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>모듈 가져오기

_ContentModeratorQS.py_라는 새 Python 스크립트를 만들고 다음 코드를 추가하여 SDK의 필요한 부분을 가져옵니다.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=1-10)]

또한 “pretty print” 함수를 가져와 최종 출력을 처리합니다.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=12)]


## <a name="initialize-variables"></a>변수 초기화

그런 다음, Content Moderator 구독 키 및 엔드포인트 URL에 대한 변수를 추가합니다. `<your subscription key>`를 키 값으로 바꿔야 합니다. 구독 키에 해당하는 지역 식별자를 사용하려면 `endpoint_url` 값을 변경해야 할 수도 있습니다. 평가판 구독 키는 **westus** 지역에 생성됩니다.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=14-16)]


간단히 하기 위해 스크립트에서 직접 텍스트를 분석하겠습니다. 조정할 텍스트 콘텐츠의 새 문자열을 다음과 같이 정의합니다.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=18-21)]


## <a name="query-the-moderator-service"></a>Moderator 서비스 쿼리

구독 키 및 엔드포인트 URL을 사용하여 **ContentModeratorClient** 인스턴스를 만듭니다. 그런 다음, 멤버 **TextModerationOperations** 인스턴스를 사용하여 조정 API를 호출합니다. 호출 방법에 대한 자세한 내용은 **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python#screen-text)** 참조 문서를 참조하세요.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=23-36)]

## <a name="print-the-response"></a>응답 출력

마지막으로 호출이 완료되고 **화면** 인스턴스를 반환했는지 확인합니다. 그런 다음, 반환된 데이터를 콘솔에 인쇄합니다.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=38-39)]


이 빠른 시작에 사용된 샘플 텍스트는 다음 출력을 생성합니다.

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 지정된 텍스트 샘플에 대한 관련 정보를 반환하는 Content Moderator 서비스를 사용하는 간단한 Python 스크립트를 개발했습니다. 다음으로, 다양한 플래그 및 분류가 의미하는 바를 자세히 알아보므로 어떤 데이터가 필요하고 앱에서 데이터를 어떻게 처리해야 하는지 결정할 수 있습니다.

> [!div class="nextstepaction"]
> [텍스트 조정 가이드](text-moderation-api.md)