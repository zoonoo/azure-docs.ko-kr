---
title: 식별 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750246"
---
### <a name="financial-account-identification"></a>금융 계정 식별

이 엔터티 범주에는 재무 정보 및 id의 공식 형식이 포함 되어 있습니다.

#### <a name="category-aba-routing-number"></a>범주: ABA routing number

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        ABA 라우팅 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        ABA (미국식 은행원 Association) 전송 라우팅 번호입니다.
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>범주: SWIFT 코드

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        SWIFT 코드

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        지불 지침 정보에 대 한 코드를 SWIFT.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>범주: 신용 카드

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        신용 카드

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        신용 카드 번호. 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>범주: IBAN (국제 은행 계좌 번호) 

이 범주에는 다음 엔터티가 포함 됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        신용 카드

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        지불 지침 정보에 대 한 IBAN 코드입니다.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>정부 및 국가/지역 관련 id

> [!NOTE]
> 다음 재무 및 국가 별 엔터티는 매개 변수를 사용 하 여 반환 되지 않습니다 `domain=phi` .
> * 여권 번호
> * 세금 Id

다음 엔터티는 country로 그룹화 되 고 나열 됩니다.

#### <a name="argentina"></a>아르헨티나

:::row:::
    :::column span="":::
        **엔터티**

        DNI (아르헨티나 국내 Id) 번호

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>오스트리아

:::row:::
    :::column span="":::
        **엔터티**

        오스트리아 id 카드

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트리아 세금 id 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        오스트리아 값 추가 세금 (VAT) 번호

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>오스트레일리아

:::row:::
    :::column span="":::
        **엔터티**

        오스트레일리아 은행 계좌 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 비즈니스 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 회사 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 운전 면허  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 의료 계정 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 여권 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 여권 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 세금 파일 번호

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>벨기에

:::row:::
    :::column span="":::
        **엔터티**

        벨기에 국가 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        벨기에 값 추가 세금 (VAT) 번호

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>브라질 

:::row:::
    :::column span="":::
        **엔터티**

        브라질 법률 엔터티 번호 (CNPJ)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        브라질 CPF 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        브라질 국가 ID 카드 (RG)

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>캐나다

:::row:::
    :::column span="":::
        **엔터티**

        캐나다 은행 계좌 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        캐나다 운전 면허 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 health service 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 여권 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 PHIN (개인 상태 식별 번호)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        캐나다 사회 보험 번호

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>칠레 

:::row:::
    :::column span="":::
        **엔터티**

        칠레 id 카드 번호

    :::column-end:::
:::row-end:::

#### <a name="china"></a>중국

:::row:::
    :::column span="":::
        **엔터티**

        중국 상주 Id 카드 (중국) 번호

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>EU (유럽 연합)

:::row:::
    :::column span="":::
        **엔터티**

        EU 직불 카드 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 운전 면허 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 국가 식별 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 여권 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU SSN (주민 등록 번호) 또는 동등한 ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU TIN(납세자 식별 번호)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU GPS 좌표

    :::column-end:::
:::row-end:::

#### <a name="france"></a>프랑스

:::row:::
    :::column span="":::
        **엔터티**

        프랑스 드라이버의 라이선스 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 건강 보험 수

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 국가 ID 카드 (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 여권 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 주민 등록 번호 (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 세금 식별 번호 (Numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 값 추가 세금 (VAT) 번호

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>독일

:::row:::
    :::column span="":::
        **엔터티**

        독일어 드라이버의 라이선스 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 Id 카드 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 여권 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 세금 식별 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 부가 세금 번호를 추가 했습니다.

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>홍콩

:::row:::
    :::column span="":::
        **엔터티**

        HKID (홍콩 Id Card) 번호

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>헝가리

:::row:::
    :::column span="":::
        **엔터티**

        헝가리 개인 식별 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        헝가리 세금 id 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        헝가리 값이 추가 된 세금 번호

    :::column-end:::
:::row-end:::

#### <a name="india"></a>인도

:::row:::
    :::column span="":::
        **엔터티**

        인도 (영구 계정 번호)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        인도 Aadhaar (고유 Id) 번호

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>인도네시아

:::row:::
    :::column span="":::
        **엔터티**

        인도네시아 Id 카드 (KTP) 번호

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>아일랜드

:::row:::
    :::column span="":::
        **엔터티**

        아일랜드 PPS (Personal Public Service) 번호

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>이스라엘

:::row:::
    :::column span="":::
        **엔터티**

        이스라엘 국가 ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        이스라엘 은행 계좌 번호

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>이탈리아

:::row:::
    :::column span="":::
        **엔터티**

        이탈리아 드라이버의 라이선스 ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        이탈리아 회계 코드

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        이탈리아 부가 세금 번호를 추가 했습니다.

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>일본

:::row:::
    :::column span="":::
        **엔터티**

        일본 은행 계좌 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        일본 드라이버의 라이선스 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 "내 번호" (개인)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 "내 번호" (회사)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 상주 등록 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 거주지 카드 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 사회 보험 번호 (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 여권 번호

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>룩셈부르크

:::row:::
    :::column span="":::
        **엔터티**

        룩셈부르크 국가 식별 번호 (자연 개인)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        룩셈부르크 국가 식별 번호 (비 자연 사람)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>몰타

:::row:::
    :::column span="":::
        **엔터티**

        몰타 Id 카드 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        몰타 세금 Id 번호

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>뉴질랜드

:::row:::
    :::column span="":::
        **엔터티**

        뉴질랜드 은행 계좌 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        뉴질랜드 드라이버의 라이선스 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        뉴질랜드 Inland 수익 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        새 뉴질랜드 경제 산업성 상태 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       뉴질랜드 소셜 Welfare 번호

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>필리핀

:::row:::
    :::column span="":::
        **엔터티**

        필리핀 통일 된 다목적 ID 번호

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>포르투갈 

:::row:::
    :::column span="":::
        **엔터티**

        포르투갈 시민 카드 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       포르투갈 세금 식별 번호

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>싱가포르

:::row:::
    :::column span="":::
        **엔터티**

        싱가포르 국제 등록 ID 카드 (NRIC) 번호

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>남아프리카

:::row:::
    :::column span="":::
        **엔터티**

        남아프리카 공화국 식별 번호

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>대한민국

:::row:::
    :::column span="":::
        **엔터티**

        남부 대한민국 등록 번호

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>스페인

:::row:::
    :::column span="":::
        **엔터티**

        스페인 DNI

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스페인 사회 보장 번호 (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스페인 세금 식별 번호

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>스위스

:::row:::
    :::column span="":::
        **엔터티**

        스위스 사회 보장 번호 AHV

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>대만 

:::row:::
    :::column span="":::
        **엔터티**

        대만 국가 ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       대만 상주 인증서 (ARC/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        대만 Passport 번호

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>영국

:::row:::
    :::column span="":::
        **엔터티**

        영국의 드라이버의 라이선스 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국의 사용한 선거 지도로 Roll 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국의 국가 상태 관리 서비스 (NHS) 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국의 국가 보험 번호 (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국의 또는 미국 여권 번호

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       영국의 고유 납세자 참조 번호

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>미국

:::row:::
    :::column span="":::
        **엔터티**

        미국 사회 보장 번호 (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 운전 면허 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 또는 영국 여권 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 스냅숏입니다 (개별 납세자 식별 번호)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       DEA (미국 마약 집행 기관) 번호

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 은행 계좌 번호

    :::column-end:::
:::row-end:::
