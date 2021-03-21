---
title: 일반적인 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500100"
---
Text Analytics에 대 한 NER 기능은 다음과 같은 일반 (비 식별) 엔터티 범주를 반환 합니다. 예를 들어 요청을 끝점으로 보내는 경우 `/entities/recognition/general` 입니다.


| 범주 | Description                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person](#category-person)     | 사람의 이름입니다.  |
| [PersonType](#category-persontype) | 사용자가 보유 한 작업 유형 또는 역할 |
| [위치](#category-location)    | 자연 스러운 랜드마크, 구조, 지리적 기능 및 지정 학적 엔터티 |
| [조직](#category-organization)  | 회사, 정치적 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체.  |
| [이벤트](#category-event)  | 기록, 소셜 및 자연스럽 게 발생 하는 이벤트입니다. |
| [제품](#category-product) | 다양 한 범주의 물리적 개체 |
| [기술](#category-skill) | 기능, 기술 또는 전문 지식.  |
| [주소](#category-address) | 전체 우편 주소  |
| [전화 번호](#category-phonenumber) | 전화 번호. |
| [전자 메일](#category-email) | 전자 메일 주소. |
| [URL](#category-url) | 웹 사이트에 대 한 Url입니다. |
| [TCP/IP](#category-ip) | 네트워크 IP 주소. |
| [DateTime](#category-datetime) | 날짜 및 시간입니다. |
| [수량](#category-quantity) | 숫자 측정 및 단위. |


### <a name="category-person"></a>범주: Person

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        사람

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사람의 이름입니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>범주: PersonType

이 범주에는 다음 엔터티가 포함 됩니다.


:::row:::
    :::column span="":::
        **엔터티**

        PersonType

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사용자가 보유 한 작업 유형 또는 역할
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>범주: 위치

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        위치

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        자연 스러운 랜드마크, 구조, 지리적 기능 및 지정 학적 엔터티.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>범주가

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        지정 학적 엔터티 (GPE)

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        도시, 국가/지역, 주.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Structural

    :::column-end:::
    :::column span="2":::

        구조체를 만들었습니다. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        지리적

    :::column-end:::
    :::column span="2":::

        강, 대양 및 deserts와 같은 지리적 및 자연 기능.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>범주: 조직

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        조직

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        회사, 정치적 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체. Nationalities 및 religions는이 엔터티 형식에 포함 되지 않습니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>범주가

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        의료

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        의료 회사 및 그룹.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        재고 교환

    :::column-end:::
    :::column span="2":::

        재고 교환 그룹. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스포츠

    :::column-end:::
    :::column span="2":::

        스포츠 관련 조직.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>범주: 이벤트

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        이벤트

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        기록, 소셜 및 자연스럽 게 발생 하는 이벤트입니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`,,,,,,, `es` `fr` `de` `it` `zh-hans` `ja` `ko` `pt-pt` 및 `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>범주가

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        Culture

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        문화권 이벤트 및 휴일.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        자연스럽 게 발생 하는 이벤트입니다.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스포츠

    :::column-end:::
    :::column span="2":::

        스포츠 이벤트.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>범주: Product

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        제품

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        다양 한 범주의 물리적 개체
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>범주가

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        제품 컴퓨팅
    :::column-end:::
    :::column span="2":::
        **세부 정보**

        제품을 계산 합니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>범주: 기술

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        기술

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        기능, 기술 또는 전문 지식.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>범주: 주소

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        주소

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전체 우편 주소입니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>범주: PhoneNumber

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전화 번호 (미국과 EU 전화 번호만 해당).
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>범주: 전자 메일

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        메일

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전자 메일 주소.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>범주: URL

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        URL

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        웹 사이트에 대 한 Url입니다. 
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>범주: IP

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        IP

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        네트워크 IP 주소. 
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>범주: DateTime

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        DateTime

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        날짜 및 시간입니다. 
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

#### <a name="subcategories"></a>범주가

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        날짜

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        일정 날짜
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        시간

    :::column-end:::
    :::column span="2":::

        하루 중 시간
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        날짜 범위.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        시간 범위.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duration

    :::column-end:::
    :::column span="2":::

        기간
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        설정

    :::column-end:::
    :::column span="2":::

        반복 횟수를 설정 합니다.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>범주: 수량

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        수량

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        숫자 및 숫자 수량.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>범주가

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        숫자

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        숫자
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        백분율

    :::column-end:::
    :::column span="2":::

        백분율
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        서 수

    :::column-end:::
    :::column span="2":::

        서 수 번호입니다.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        나이

    :::column-end:::
    :::column span="2":::

        에이징을.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        통화

    :::column-end:::
    :::column span="2":::

        환산
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        차원

    :::column-end:::
    :::column span="2":::

        차원과 측정.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        온도

    :::column-end:::
    :::column span="2":::

        낮게.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
