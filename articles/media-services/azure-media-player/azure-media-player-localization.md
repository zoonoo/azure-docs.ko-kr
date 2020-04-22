---
title: Azure 미디어 플레이어 지역화
description: 영어 가외 로캘 사용자에 대한 다국어 지원.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727178"
---
# <a name="localization"></a>지역화 #

다국어 지원을 통해 영어 이외의 로캘의 사용자가 기본적으로 플레이어와 상호 작용할 수 있습니다. Azure Media Player는 페이지 언어를 기반으로 오류 메시지를 지역화하는 전역 언어 사전을 인스턴스화합니다. 개발자는 강제 설정 언어를 사용하여 플레이어 인스턴스를 만들 수도 있습니다. 기본 언어는 영어(en)입니다.

> [!NOTE]
> 이 기능은 여전히 일부 테스트를 거치고 있으며 버그가 있을 수 있습니다.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player는 현재 해당 언어 코드로 다음 언어를 지원합니다.

| 언어            | 코드 | 언어                | 코드   | 언어                | 코드         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| 영어 {기본}   | en   | 크로아티아어                | hr     | 루마니아어                | ro           |
| 아랍어              | ar   | 헝가리어               | hu     | 슬로바키아어                  | sk           |
| 불가리아어           | bg   | 인도네시아어              | id     | 슬로베니아어                 | sl           |
| 카탈로니아어             | Ca   | 아이슬란드어               | is     | 세르비아어 - 키릴 자모      | sr-cyrl-cs   |
| 체코어               | cs   | 이탈리아어                 | it     | 세르비아어 - 라틴 문자         | sr-latn-rs   |
| 덴마크어              | da   | 일본어                | ja     | 러시아어                 | ru           |
| 독일어              | de   | 카자흐어                  | 주식     | 스웨덴어                 | sv           |
| 그리스어               | el   | 한국어                  | ko     | 태국어                    | th           |
| 스페인어             | es   | 리투아니아어              | lt     | 타갈로그어                 | Tl           |
| 에스토니아어            | et   | 라트비아어                 | lv     | 터키어                 | tr           |
| 바스크어              | Eu   | 말레이시아               | ms     | 우크라이나어               | uk           |
| 페르시아어               | Fa   | 노르웨이어 - 보크앙 ¥l     | nb     | 우르두어                    | 당신의           |
| 핀란드어             | fi   | 네덜란드어                   | nl     | 베트남어              | Ⅵ           |
| 프랑스어              | fr   | 노르웨이어 - 니노르스크     | nn     | 중국어 - 간체    | zh-hans      |
| 갈리시아어            | gl   | 폴란드어                  | pl     | 중국어 - 전통   | zh-hant      |
| 히브리어              | he   | 포르투갈어 - 브라질     | pt-br  |                         |              |
| 힌디어               | hu   | 포르투갈어 - 포르투갈   | pt-pt  |                         |              |


> [!NOTE]
> 현지화가 발생하지 않으려면 언어를 영어로 강제해야 합니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure 미디어 플레이어 빠른 시작](azure-media-player-quickstart.md)