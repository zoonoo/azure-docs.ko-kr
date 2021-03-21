---
title: 식별 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 352b81bf2dfeca1d7413e7cac131264d06c7b92e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599307"
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

        이 엔터티 범주를 가져오려면 `ABARoutingNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ABARoutingNumber` 검색 된 경우 API 응답에도 반환 됩니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`
      
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

        이 엔터티 범주를 가져오려면 `SWIFTCode` 매개 변수에를 추가 `pii-categories` 합니다. `SWIFTCode` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
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

        이 엔터티 범주를 가져오려면 `CreditCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `CreditCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.

    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
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

        이 엔터티 범주를 가져오려면 `InternationlBankingAccountNumber` 매개 변수에를 추가 `pii-categories` 합니다. `InternationlBankingAccountNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `ARNationalIdentityNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ARNationalIdentityNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>오스트리아

:::row:::
    :::column span="":::
        **엔터티**

        오스트리아 id 카드

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `ATIdentityCard` 매개 변수에를 추가 `pii-categories` 합니다. `ATIdentityCard` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트리아 세금 id 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ATTaxIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ATTaxIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트리아 값 추가 세금 (VAT) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ATValueAddedTaxNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ATValueAddedTaxNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>오스트레일리아

:::row:::
    :::column span="":::
        **엔터티**

        오스트레일리아 은행 계좌 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `AUDriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `AUDriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 비즈니스 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `AUBusinessNumber` 매개 변수에를 추가 `pii-categories` 합니다. `AUBusinessNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 회사 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `AUCompanyNumber` 매개 변수에를 추가 `pii-categories` 합니다. `AUCompanyNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 운전 면허  

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `AUDriversLicense` 매개 변수에를 추가 `pii-categories` 합니다. `AUDriversLicense` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 의료 계정 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `AUMedicalAccountNumber` 매개 변수에를 추가 `pii-categories` 합니다. `AUMedicalAccountNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ATPassportNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ATPassportNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 세금 파일 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ATTaxIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ATTaxIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>벨기에

:::row:::
    :::column span="":::
        **엔터티**

        벨기에 국가 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `BENationalNumber` 매개 변수에를 추가 `pii-categories` 합니다. `BENationalNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        벨기에 값 추가 세금 (VAT) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `BEValueAddedTaxNumber` 매개 변수에를 추가 `pii-categories` 합니다. `BEValueAddedTaxNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>브라질 

:::row:::
    :::column span="":::
        **엔터티**

        브라질 법률 엔터티 번호 (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `BRLegalEntityNumber` 매개 변수에를 추가 `pii-categories` 합니다. `BRLegalEntityNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        브라질 CPF 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `BRCPFNumber` 매개 변수에를 추가 `pii-categories` 합니다. `BRCPFNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        브라질 국가 ID 카드 (RG)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `BRNationalIDRG` 매개 변수에를 추가 `pii-categories` 합니다. `BRNationalIDRG` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>캐나다

:::row:::
    :::column span="":::
        **엔터티**

        캐나다 은행 계좌 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `CABankAccountNumber` 매개 변수에를 추가 `pii-categories` 합니다. `CABankAccountNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 운전 면허 번호

    :::column-end:::

    :::column span="2":::

        이 엔터티 범주를 가져오려면 `CADriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `CADriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 health service 번호

        
    :::column-end:::

    :::column span="2":::

        이 엔터티 범주를 가져오려면 `CAHealthServiceNumber` 매개 변수에를 추가 `pii-categories` 합니다. `CAHealthServiceNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `CAPassportNumber` 매개 변수에를 추가 `pii-categories` 합니다. `CAPassportNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 PHIN (개인 상태 식별 번호)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `CAPersonalHealthIdentification` 매개 변수에를 추가 `pii-categories` 합니다. `CAPersonalHealthIdentification` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 사회 보험 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `CASocialInsuranceNumber` 매개 변수에를 추가 `pii-categories` 합니다. `CASocialInsuranceNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>칠레 

:::row:::
    :::column span="":::
        **엔터티**

        칠레 id 카드 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `CLIdentityCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `CLIdentityCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>중국

:::row:::
    :::column span="":::
        **엔터티**

        중국 상주 Id 카드 (중국) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `CNResidentIdentityCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `CNResidentIdentityCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>EU (유럽 연합)

:::row:::
    :::column span="":::
        **엔터티**

        EU 직불 카드 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `EUDebitCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `EUDebitCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 운전 면허 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `EUDriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `EUDriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU GPU 좌표

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `EUGPSCoordinates` 매개 변수에를 추가 `pii-categories` 합니다. `EUGPSCoordinates` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 국가 식별 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `EUNationalIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `EUNationalIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `EUPassportNumber` 매개 변수에를 추가 `pii-categories` 합니다. `EUPassportNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU SSN (주민 등록 번호) 또는 동등한 ID

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `EUSocialSecurityNumber` 매개 변수에를 추가 `pii-categories` 합니다. `EUSocialSecurityNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU TIN(납세자 식별 번호)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `EUTaxIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `EUTaxIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>프랑스

:::row:::
    :::column span="":::
        **엔터티**

        프랑스 드라이버의 라이선스 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `FRDriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `FRDriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 건강 보험 수

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `FRHealthInsuranceNumber` 매개 변수에를 추가 `pii-categories` 합니다. `FRHealthInsuranceNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 국가 ID 카드 (CNI)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `FRNationalID` 매개 변수에를 추가 `pii-categories` 합니다. `FRNationalID` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `FRPassportNumber` 매개 변수에를 추가 `pii-categories` 합니다. `FRPassportNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 주민 등록 번호 (INSEE)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `FRSocialSecurityNumber` 매개 변수에를 추가 `pii-categories` 합니다. `FRSocialSecurityNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 세금 식별 번호 (Numéro SPI)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `FRTaxIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `FRTaxIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 값 추가 세금 (VAT) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `FRValueAddedTaxNumber` 매개 변수에를 추가 `pii-categories` 합니다. `FRValueAddedTaxNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>독일

:::row:::
    :::column span="":::
        **엔터티**

        독일어 드라이버의 라이선스 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `DEDriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `DEDriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 Id 카드 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `DEIdentityCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `DEIdentityCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `DEPassportNumber` 매개 변수에를 추가 `pii-categories` 합니다. `DEPassportNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 세금 식별 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `DETaxIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `DETaxIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 부가 세금 번호를 추가 했습니다.

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `DEValueAddedNumber` 매개 변수에를 추가 `pii-categories` 합니다. `DEValueAddedNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>홍콩

:::row:::
    :::column span="":::
        **엔터티**

        HKID (홍콩 Id Card) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `HKIdentityCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `HKIdentityCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>헝가리

:::row:::
    :::column span="":::
        **엔터티**

        헝가리 개인 식별 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `HUPersonalIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `HUPersonalIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        헝가리 세금 id 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `HUTaxIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `HUTaxIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        헝가리 값이 추가 된 세금 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `HUValueAddedNumber` 매개 변수에를 추가 `pii-categories` 합니다. `HUValueAddedNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>인도

:::row:::
    :::column span="":::
        **엔터티**

        인도 (영구 계정 번호)

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `INPermanentAccount` 매개 변수에를 추가 `pii-categories` 합니다. `INPermanentAccount` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        인도 Aadhaar (고유 Id) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `INUniqueIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `INUniqueIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>인도네시아

:::row:::
    :::column span="":::
        **엔터티**

        인도네시아 Id 카드 (KTP) 번호

    :::column-end:::
    :::column span="2":::

        **세부 정보**

        이 엔터티 범주를 가져오려면 `IDIdentityCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `IDIdentityCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>아일랜드

:::row:::
    :::column span="":::
        **엔터티**

        아일랜드 PPS (Personal Public Service) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `IEPersonalPublicServiceNumber` 매개 변수에를 추가 `pii-categories` 합니다. `IEPersonalPublicServiceNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        아일랜드 PPS (Personal Public Service) 번호 v2

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `IEPersonalPublicServiceNumberV2` 매개 변수에를 추가 `pii-categories` 합니다. `IEPersonalPublicServiceNumberV2` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>이스라엘

:::row:::
    :::column span="":::
        **엔터티**

        이스라엘 국가 ID

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `ILNationalID` 매개 변수에를 추가 `pii-categories` 합니다. `ILNationalID` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        이스라엘 은행 계좌 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ILBankAccountNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ILBankAccountNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>이탈리아

:::row:::
    :::column span="":::
        **엔터티**

        이탈리아 드라이버의 라이선스 ID

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `ITDriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ITDriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        이탈리아 회계 코드

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ITFiscalCode` 매개 변수에를 추가 `pii-categories` 합니다. `ITFiscalCode` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        이탈리아 부가 세금 번호를 추가 했습니다.

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ITValueAddedTaxNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ITValueAddedTaxNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>일본

:::row:::
    :::column span="":::
        **엔터티**

        일본 은행 계좌 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `JPBankAccountNumber` 매개 변수에를 추가 `pii-categories` 합니다. `JPBankAccountNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 드라이버의 라이선스 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `JPDriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `JPDriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 "내 번호" (개인)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `JPMyNumberPersonal` 매개 변수에를 추가 `pii-categories` 합니다. `JPMyNumberPersonal` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 "내 번호" (회사)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `JPMyNumberCorporate` 매개 변수에를 추가 `pii-categories` 합니다. `JPMyNumberCorporate` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 상주 등록 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ITValueAddedTaxNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ITValueAddedTaxNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 거주지 카드 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `JPResidenceCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `JPResidenceCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 사회 보험 번호 (SIN)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `JPSocialInsuranceNumber` 매개 변수에를 추가 `pii-categories` 합니다. `JPSocialInsuranceNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `JPPassportNumber` 매개 변수에를 추가 `pii-categories` 합니다. `JPPassportNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>룩셈부르크

:::row:::
    :::column span="":::
        **엔터티**

        룩셈부르크 국가 식별 번호 (자연 개인)

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `LUNationalIdentificationNumberNatural` 매개 변수에를 추가 `pii-categories` 합니다. `LUNationalIdentificationNumberNatural` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        룩셈부르크 국가 식별 번호 (비 자연 사람)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `LUNationalIdentificationNumberNonNatural` 매개 변수에를 추가 `pii-categories` 합니다. `LUNationalIdentificationNumberNonNatural` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>몰타

:::row:::
    :::column span="":::
        **엔터티**

        몰타 Id 카드 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `MTIdentityCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `MTIdentityCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        몰타 세금 Id 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `MTTaxIDNumber` 매개 변수에를 추가 `pii-categories` 합니다. `MTTaxIDNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>뉴질랜드

:::row:::
    :::column span="":::
        **엔터티**

        뉴질랜드 은행 계좌 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `NZBankAccountNumber` 매개 변수에를 추가 `pii-categories` 합니다. `NZBankAccountNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        뉴질랜드 드라이버의 라이선스 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `NZDriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `NZDriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        뉴질랜드 Inland 수익 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `NZInlandRevenueNumber` 매개 변수에를 추가 `pii-categories` 합니다. `NZInlandRevenueNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        새 뉴질랜드 경제 산업성 상태 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `NZMinistryOfHealthNumber` 매개 변수에를 추가 `pii-categories` 합니다. `NZMinistryOfHealthNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       뉴질랜드 소셜 Welfare 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `NZSocialWelfareNumber` 매개 변수에를 추가 `pii-categories` 합니다. `NZSocialWelfareNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>필리핀

:::row:::
    :::column span="":::
        **엔터티**

        필리핀 통일 된 다목적 ID 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `PHUnifiedMultiPurposeIDNumber` 매개 변수에를 추가 `pii-categories` 합니다. `PHUnifiedMultiPurposeIDNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>포르투갈 

:::row:::
    :::column span="":::
        **엔터티**

        포르투갈 시민 카드 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `PTCitizenCardNumber` 매개 변수에를 추가 `pii-categories` 합니다. `PTCitizenCardNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       포르투갈 세금 식별 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `PTTaxIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `PTTaxIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>싱가포르

:::row:::
    :::column span="":::
        **엔터티**

        싱가포르 국제 등록 ID 카드 (NRIC) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `PTTaxIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `PTTaxIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>남아프리카 공화국

:::row:::
    :::column span="":::
        **엔터티**

        남아프리카 공화국 식별 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `ZAIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ZAIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>대한민국

:::row:::
    :::column span="":::
        **엔터티**

        남부 대한민국 등록 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `KRResidentRegistrationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `KRResidentRegistrationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>스페인

:::row:::
    :::column span="":::
        **엔터티**

        스페인 DNI

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `ESDNI` 매개 변수에를 추가 `pii-categories` 합니다. `ESDNI` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스페인 사회 보장 번호 (SSN)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ESSocialSecurityNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ESSocialSecurityNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스페인 세금 식별 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `ESTaxIdentificationNumber` 매개 변수에를 추가 `pii-categories` 합니다. `ESTaxIdentificationNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>스위스

:::row:::
    :::column span="":::
        **엔터티**

        스위스 사회 보장 번호 AHV

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `CHSocialSecurityNumber` 매개 변수에를 추가 `pii-categories` 합니다. `CHSocialSecurityNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>대만 

:::row:::
    :::column span="":::
        **엔터티**

        대만 국가 ID

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `TWNationalID` 매개 변수에를 추가 `pii-categories` 합니다. `TWNationalID` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       대만 상주 인증서 (ARC/TARC)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `TWResidentCertificate` 매개 변수에를 추가 `pii-categories` 합니다. `TWResidentCertificate` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        대만 Passport 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `TWPassportNumber` 매개 변수에를 추가 `pii-categories` 합니다. `TWPassportNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>영국

:::row:::
    :::column span="":::
        **엔터티**

        영국의 드라이버의 라이선스 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `UKDriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `UKDriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       영국의 사용한 선거 지도로 Roll 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `UKNationalInsuranceNumber` 매개 변수에를 추가 `pii-categories` 합니다. `UKNationalInsuranceNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국의 국가 상태 관리 서비스 (NHS) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `UKNationalHealthNumber` 매개 변수에를 추가 `pii-categories` 합니다. `UKNationalHealthNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국의 국가 보험 번호 (NINO)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `UKNationalInsuranceNumber` 매개 변수에를 추가 `pii-categories` 합니다. `UKNationalInsuranceNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국의 또는 미국 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `USUKPassportNumber` 매개 변수에를 추가 `pii-categories` 합니다. `USUKPassportNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국의 고유 납세자 참조 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `UKUniqueTaxpayerNumber` 매개 변수에를 추가 `pii-categories` 합니다. `UKUniqueTaxpayerNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>미국

:::row:::
    :::column span="":::
        **엔터티**

        미국 사회 보장 번호 (SSN)

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `USSocialSecurityNumber` 매개 변수에를 추가 `pii-categories` 합니다. `USSocialSecurityNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 되는 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 운전 면허 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `USDriversLicenseNumber` 매개 변수에를 추가 `pii-categories` 합니다. `USDriversLicenseNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 또는 영국 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `USUKPassportNumber` 매개 변수에를 추가 `pii-categories` 합니다. `USUKPassportNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 스냅숏입니다 (개별 납세자 식별 번호)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `USIndividualTaxpayerIdentification` 매개 변수에를 추가 `pii-categories` 합니다. `USIndividualTaxpayerIdentification` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       DEA (미국 마약 집행 기관) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `DrugEnforcementAgencyNumber` 매개 변수에를 추가 `pii-categories` 합니다. `DrugEnforcementAgencyNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 은행 계좌 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `USBankAccountNumber` 매개 변수에를 추가 `pii-categories` 합니다. `USBankAccountNumber` 가 검색 되 면 API 응답에서 반환 됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
