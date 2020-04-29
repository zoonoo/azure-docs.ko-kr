---
title: Azure Media Player 지역화
description: 영어가 아닌 로캘의 사용자를 위한 다국어 지원
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727178"
---
# <a name="localization"></a>지역화 #

다중 언어 지원을 사용 하면 영어가 아닌 로캘의 사용자가 기본적으로 플레이어와 상호 작용할 수 있습니다. Azure Media Player는 페이지 언어에 따라 오류 메시지를 지역화 하는 언어의 전역 사전을 사용 하 여 인스턴스화합니다. 개발자는 강제로 설정 된 언어로 플레이어 인스턴스를 만들 수도 있습니다. 기본 언어는 영어 (en)입니다.

> [!NOTE]
> 이 기능은 여전히 일부 테스트를 진행 하 고 있으므로 버그의 영향을 받습니다.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player 현재 해당 언어 코드를 사용 하 여 다음 언어를 지원 합니다.

| 언어            | 코드 | 언어                | 코드   | 언어                | 코드         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| 영어 {default}   | en   | 크로아티아어                | hr     | 루마니아어                | ro           |
| 아랍어              | ar   | 헝가리어               | hu     | 슬로바키아어                  | sk           |
| 불가리아어           | bg   | 인도네시아어              | id     | 슬로베니아어                 | sl           |
| 카탈로니아어             | 발급   | 아이슬란드어               | 다음인 경우     | 세르비아어 - 키릴 자모      | cyrl-cs   |
| 체코어               | cs   | 이탈리아어                 | it     | 세르비아어 - 라틴 문자         | sr-latn-rs   |
| 덴마크어              | da   | 일본어                | ja     | 러시아어                 | ru           |
| 독일어              | de   | 카자흐어                  | kk     | 스웨덴어                 | sv           |
| 그리스어               | el   | 한국어                  | ko     | 태국어                    | th           |
| 스페인어             | es   | 리투아니아어              | lt     | 타갈로그어                 | tl           |
| 에스토니아어            | et   | 라트비아어                 | lv     | 터키어                 | tr           |
| 바스크어              | eu   | 말레이시아               | ms     | 우크라이나어               | uk           |
| 페르시아어               | fa   | 노르웨이어-BokmÃ ¥ l     | nb     | 우르두어                    | ur           |
| 핀란드어             | fi   | 네덜란드어                   | nl     | 베트남어              | vi           |
| 프랑스어              | fr   | 노르웨이어-니노르스크     | nn     | 중국어-간체    | zh-hans      |
| 갈리시아어            | gl   | 폴란드어                  | pl     | 중국어-번체   | zh-hant      |
| 히브리어              | he   | 포르투갈어 - 브라질     | pt-br  |                         |              |
| 힌디어               | hu   | 포르투갈어 - 포르투갈   | pt-pt  |                         |              |


> [!NOTE]
> 지역화를 수행 하지 않으려면 언어를 영어로 강제 해야 합니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)