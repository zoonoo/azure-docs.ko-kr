---
title: '빠른 시작: Python에서 텍스트 콘텐츠 분석 - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Python용 Content Moderator SDK를 사용하여 다양하고 불쾌한 자료에 대한 텍스트 콘텐츠를 분석하는 방법
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bb0e44f83e2101a7b21e7b7ec6fdc75974c6d6d8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333600"
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

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>모듈 가져오기

_ContentModeratorQS.py_라는 새 Python 스크립트를 만들고 다음 코드를 추가하여 SDK의 필요한 부분을 가져옵니다. JSON 응답을 보다 쉽게 읽을 수 있도록 예쁜 인쇄 모듈이 포함되어 있습니다.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>변수 초기화

그런 다음, Content Moderator 구독 키 및 엔드포인트 URL에 대한 변수를 추가합니다. `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 이름을 환경 변수에 추가하고 구독 키를 해당 값으로 사용합니다. 기본 엔드포인트 URL의 경우 `CONTENT_MODERATOR_ENDPOINT`를 환경 변수에 추가하고 지역별 URL을 해당 값으로 사용합니다(예: `https://westus.api.cognitive.microsoft.com`). 평가판 구독 키는 **westus** 지역에 생성됩니다.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

한 파일의 여러 줄 텍스트 문자열이 조정됩니다. 다음과 같이 로컬 루트 폴더에 [content_moderator_text_moderation.txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) 파일을 포함하고 해당 파일 이름을 변수에 추가합니다.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Moderator 서비스 쿼리

구독 키 및 엔드포인트 URL을 사용하여 **ContentModeratorClient** 인스턴스를 만듭니다. 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

그런 다음, 클라이언트의 멤버 **TextModerationOperations** 인스턴스를 사용하여 `screen_text` 함수를 통해 조정 API를 호출합니다. 호출 방법에 대한 자세한 내용은 **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** 참조 문서를 참조하세요.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>인쇄 응답 확인

샘플을 실행하고 응답을 확인합니다. 성공적으로 완료되면 **화면** 인스턴스가 반환됩니다. 성공적인 결과는 다음과 같습니다.

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
