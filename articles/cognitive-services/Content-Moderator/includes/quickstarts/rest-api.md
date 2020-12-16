---
title: Content Moderator REST API 빠른 시작
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Azure Content Moderator REST API를 시작하는 방법에 대해 알아봅니다. 앱에 콘텐츠 필터링 소프트웨어를 빌드하여 규정을 준수하거나 사용자를 위한 의도된 환경을 유지 관리합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: ebef33072b802ffc35b8c011d974dbcd203fa6e1
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561480"
---
Azure Content Moderator REST API를 시작합니다. 

Content Moderator는 공격을 받을 수 있거나 위험한 또는 바람직하지 않은 콘텐츠를 처리할 수 있는 AI 서비스입니다. AI 지원 콘텐츠 조정 서비스를 사용하여 텍스트, 이미지 및 비디오를 검색하고 콘텐츠 플래그를 자동으로 적용합니다. 그런 다음, 사용자 검토자 팀을 위한 온라인 중재자 환경인 검토 도구와 앱을 통합합니다. 앱에 콘텐츠 필터링 소프트웨어를 빌드하여 규정을 준수하거나 사용자를 위한 의도된 환경을 유지 관리합니다.

Content Moderator REST API를 사용하여 다음을 수행합니다.

* 텍스트 조정
* 이미지 조정

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Content Moderator 리소스 만들기"  target="_blank">Content Moderator 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Content Moderator에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.


## <a name="moderate-text"></a>텍스트 조정

다음과 같은 명령을 사용하여 Content Moderator API를 호출하여 텍스트 본문을 분석하고 결과를 콘솔에 인쇄합니다.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

명령을 텍스트 편집기에 복사하여 다음을 변경합니다.

1. `Ocp-Apim-Subscription-Key`를 유효한 Face 구독 키에 할당합니다.
1. 쿼리 URL의 첫 번째 부분을 구독 키에 해당하는 엔드포인트에 맞게 변경합니다.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 선택적으로 요청 본문을 분석하려는 텍스트 문자열로 변경합니다.

변경한 후에는 명령 프롬프트를 열고 새 명령을 입력합니다. 

### <a name="examine-the-results"></a>결과 검사

콘솔 창에 텍스트 조정 결과가 JSON 데이터로 표시됩니다. 예를 들면 다음과 같습니다.

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Content Moderator가 차단하는 텍스트 특성에 대한 자세한 내용은 [텍스트 조정 개념](../../text-moderation-api.md) 가이드를 참조하세요.

## <a name="moderate-images"></a>이미지 조정

다음과 같은 명령을 사용하여 Content Moderator API를 호출하여 원격 이미지를 조정하고 결과를 콘솔에 인쇄합니다.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

명령을 텍스트 편집기에 복사하여 다음을 변경합니다.

1. `Ocp-Apim-Subscription-Key`를 유효한 Face 구독 키에 할당합니다.
1. 쿼리 URL의 첫 번째 부분을 구독 키에 해당하는 엔드포인트에 맞게 변경합니다.
1. 선택적으로 요청 본문의 `"Value"` URL을 조정하려는 원격 이미지로 변경합니다.

> [!TIP]
> 바이트 데이터를 요청 본문에 전달하여 로컬 이미지를 조정할 수도 있습니다. 이를 수행하는 방법에 대한 자세한 내용은 [참조 문서](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)를 참조하세요.

변경한 후에는 명령 프롬프트를 열고 새 명령을 입력합니다. 

### <a name="examine-the-results"></a>결과 검사

콘솔 창에 이미지 조정 결과가 JSON 데이터로 표시됩니다. 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Content Moderator가 차단하는 이미지 특성에 대한 자세한 내용은 [이미지 조정 개념](../../image-moderation-api.md) 가이드를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Content Moderator REST API를 사용하여 조정 작업을 수행하는 방법을 알아보았습니다. 다음으로, 개념 가이드를 참조하여 이미지 또는 다른 미디어의 조정에 대해 자세히 알아봅니다.

* [이미지 조정 개념](../../image-moderation-api.md)
* [텍스트 조정 개념](../../text-moderation-api.md)
* [Azure Content Moderator란?](../../overview.md)