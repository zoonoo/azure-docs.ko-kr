<properties
   pageTitle="Azure 마켓플레이스에 제품 배포를 위한 준비 및 테스트 | Microsoft Azure"
   description="Azure 마켓플레이스에 제품을 배포하기 전에 마케팅 콘텐츠를 제공하고 가격 계획을 구성하며 테스트하는 방법에 대한 자세한 지침입니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="01/07/2016"
   ms.author="hascipio"/>

# 마케팅 콘텐츠로 제품 만들기 완료
이 게시 프로세스 단계에서는 Azure 마켓플레이스의 특정 마케팅 콘텐츠와 제품 및/또는 SKU에 대한 세부 정보를 제공해야 합니다. 예를 들어 제품 및/또는 SKU를 스테이징으로 푸시하는 데 필요한 제품 설명, 회사 로고, 가격 계획, 세부 계획 정보 및 기타 정보를 제공합니다. 이 정보는 Azure 포털에서 마케팅 콘텐츠로 사용됩니다. [게시 포털][link-pubportal]에서 이 프로세스를 시작합니다.

## 1단계: 마켓플레이스 마케팅 콘텐츠 제공
**영어가 기본값이며 지원되는 유일한 언어입니다.** 필드에 제공된 모든 정보가 영어로 되어 있는지 확인하세요. 스테이징으로 푸시하기 전에는 언제든지 모든 정보를 편집할 수 있습니다.

  1. 게시 포털 [https://publish.windowsazure.com](https://publish.windowsazure.com)으로 이동합니다.
  2. 왼쪽 메뉴에서 **Marketing** 탭을 클릭합니다.
  3. 주 패널에서 **English (US)** 단추를 클릭합니다.

### 세부 정보
1. 제품 요약, 세부 요약 및 제품 설명을 입력합니다.
2.	각 크기별로 하나씩 PNG 형식으로 필요한 사양(게시 포털에서 언급한)의 이미지를 업로드합니다.

  ![drawing](media/marketplace-publishing-push-to-staging/pubportal-02.png)

  *제품 제목, 설명 및 로고*

### 링크
왼쪽 막대의 **Links** 탭에 고객에게 유용한 정보가 있는 링크를 입력합니다. 각 링크의 이름과 URL을 입력합니다.

![그리기](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### 샘플 이미지(선택 사항)
> [AZURE.NOTE] 샘플 이미지를 포함하는 것은 선택적인 단계입니다. 기타 마케팅 콘텐츠를 작성하여 스테이징으로 푸시하기 위한 요건을 갖춥니다.

왼쪽 메뉴의 **Sample Images** 탭에서 **Upload a new image**를 클릭하여 새 이미지를 업로드합니다. 기존 이미지가 있고 이를 바꾸려는 경우 **Replace image**를 클릭합니다.

![그리기](media/marketplace-publishing-push-to-staging/pubportal-03.png)

### 요금제
![그리기](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### 법적 정보
**Legal** 탭에 정책/사용 약관을 연결하는 링크를 제공합니다. 큰 **Terms of Use** 상자에 약관을 입력하거나 붙여넣습니다.

> [AZURE.IMPORTANT] 스테이징에 푸시하려면 이미지를 포함하여 모든 필드에 항목이 있어야 합니다.


## 2단계: 가격 설정
### 가격 책정 모델
|가격 책정 모델 |설명 |
|---------------|------------------------------------------|
|기본| 구입 시 지불하는 월간 정액 요금(예: 10달러/월)|
|사용량 과금(즉, 사용량, 측정기) | 제품의 게시자가 정한 사용당 요금을 지불합니다. 사용자 또는 기능에는 비례 배분을 위한 분수 개념이 없으므로 기능, 사용자 등을 기준으로 한 초과분을 정의할 수 없습니다. 사용 현황은 매시간 파트너에 의해 보고됩니다. 고객은 월별 요금제와 같은 선불제와 달리 월간 청구 주기로 후불로 지불합니다. |
|무료 평가판 | 고객은 제한된 시간 동안 무료로 사용한 후 해당 기간 이후부터 정상 요금으로 지불할 수 있습니다. |
|무료 계층 | 요금제가 항상 무료입니다. |
| 요금제 마이그레이션(즉, 변환 또는 업그레이드/다운그레이드) | 사용자가 현재 요금제에서 파트너가 정한 허용 가능한 다른 요금제로 이동하는 개념입니다. |

**제품 유형별 사용 가능한 가격 책정 모델**

> [AZURE.IMPORTANT] 제품 유형별로 사용 가능한 특정 가격 책정 모델은 다양합니다. 아래 표를 참조하세요.

| | 기본만 | 사용량 과금만 | 기본 + 사용량 과금 |
|---|---|---|---|
| 가상 컴퓨터 이미지 | 아니요 | 예 | 아니요|
| 개발자 서비스 | 예 | 예 | 예 |
| 데이터 서비스 | 예 | 아니요 | 아니요 |

### 2\.1. VM 가격 설정
> [AZURE.NOTE] BYOL은 가상 컴퓨터에만 지원됩니다.

1.	**Pricing** 탭 아래에 지원되는 모든 시장이 표시됩니다. 해당 시장을 선택하여 가격 필드를 표시합니다.
2.	게시 포털에 제공된 링크는 SKU 가격을 결정하는 데 도움이 되는 가격 정보를 보여 줍니다.
3.	SKU가 BYOL인 경우 외부적으로 사용이 허가된 (BYOL) SKU 사용 가능성에 대한 확인란을 선택합니다.
4.	SKU가 매시간인 경우 해당 소프트웨어에 대한 가격 책정을 입력합니다. 가격 책정이 없는 SKU는 구입 또는 사용할 수 없습니다.

  >[AZURE.NOTE] BYOL 및 매시간 SKU를 모두 포함하는 경우 두 요건을 모두 다루어야 하므로 BYOL 확인란 및 매시간에 대한 가격 값을 모두 포함합니다.

5.	가격 마법사가 열립니다. 마법사를 진행하여 가격 책정을 완료합니다. 지정된 시장의 외부 구입을 허용하려면 다른 국가에 대한 가격 책정도 완료합니다.
6.	ISV 송금 국가도 있습니다. ISV 송금 국가에서 판매하려면 SKU로 세금을 청구하여 징수하고, 세금을 계산한 후 해당 국가의 정부에 납부할 수 있어야 합니다. Microsoft는 법률 또는 납세 안내의 책임이 없습니다. "구매처 국가"에 대한 자세한 내용은 이 문서의 소개에 있는 "제품의 구매처 국가" 섹션을 참조하세요.

### 2\.2. 개발자 서비스 가격 설정
요금제는 기본 + 사용량 과금의 모든 조합이 가능합니다. 여기서 기본은 월별 가격이고 초과분은 사용량에 따른 가격입니다. 자세한 내용은 다음을 참조하세요.

**예:** Contoso 개발자 서비스 제공 사항

| 계획 | 가격 | 포함되는 내용 | 마이그레이션 경로 |
|-------|------|-------|-------|
|무료|0달러/월|기본 기능입니다.|다른 요금제로 마이그레이션 가능|
|Bronze|10달러/월|기본 기능 및 기능 X의 1,000 할당량입니다.|Bronze Plus, Silver 및 Gold Plans로 마이그레이션 가능|
|Bronze Plus|무료 평가판 기간: 0달러/월 + 0달러/meter01 |기본 기능 및 기능 X의 10,000 할당량. 기능 X 할당량을 사용했으면 고객은 meter01을 통해 사용량에 따라 지불할 수 있습니다.|Silver Plus 및 Gold Plans로 마이그레이션 가능|
|Bronze Plus| 유료 기간(즉, 무료 평가판 만료): 10달러/월 + 0.05달러/meter01|기본 기능 및 기능 X의 10,000 할당량. 기능 X 할당량을 사용했으면 고객은 meter01을 통해 사용량에 따라 지불할 수 있습니다.|Silver Plus 및 Gold Plans로 마이그레이션 가능|
|Silver|0\.15달러/meter01|고객은 X 기능에 대해 meter01을 통해 사용량에 따라 지불할 수 있습니다.|Bronze 및 Gold Plans로 마이그레이션 가능|
|Silver Plus|20달러/월 + 0.15달러/meter01 + 0.01달러/meter02|기본 기능 및 기능 X의 10,000 할당량 및 기능 Y의 100 할당량. 기능 X 할당량을 사용했으면 고객은 meter01을 통해 사용량에 따라 지불할 수 있습니다. 기능 Y 할당량을 사용했으면 고객은 meter02를 통해 사용량에 따라 지불할 수 있습니다.|Bronze Plus 및 Gold Plans로 마이그레이션 가능|
|Gold|1,000달러/월|기능 X의 10,000 할당량, 기능 Y의 1,000 할당량 및 기능 Z 무제한입니다.|무료를 제외한 모든 요금제로 마이그레이션 가능|

## 3단계: 지원 정보 제공
일부 정보는 인증 단계 중에 완료됩니다. 아래 단계와 같이 정보를 추가하거나 편집할 수 있습니다. 연락처 정보는 파트너와 Microsoft 간의 내부 통신에만 사용됩니다. 지원 URL은 최종 고객에게 제공됩니다.

1.	게시 포털 왼쪽의 **지원** 머리글로 이동합니다.
2.	**엔지니어링 연락처** 아래에 정보를 입력합니다.
3.	**고객 지원** 아래에 정보를 입력합니다. 메일 지원만 제공할 경우 더미 전화번호를 입력합니다. 그러면 제공된 메일이 대신 사용됩니다.
4.	지원 URL을 입력합니다.

## 4단계: Azure 마켓플레이스 범주 선택
**범주** 탭에서는 선택 항목의 배열을 제공합니다. 개발자의 제품이 이러한 범주에 포함될 수 있으며, 최대 다섯 개의 범주를 선택할 수 있습니다.

## 마케팅이 표시되는 방식
다음은 제품 마케팅 정보가 [Azure 마켓플레이스 웹 사이트](https://azure.microsoft.com/marketplace/) 및 [Azure Preview 포털](https://ms.portal.azure.com)에서 어떻게 사용되는지를 자세히 보여 줍니다.

### Azure 마켓플레이스 웹 사이트
![그리기](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![그리기](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Azure 마켓플레이스 웹 사이트에서 제품 목록*

![그리기](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Azure 마켓플레이스 웹 사이트에서 제품 설명 정보*

![그리기](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Azure 마켓플레이스 웹 사이트에서 제품 설명 가격 책정 정보*

### Azure 미리 보기 포털
![그리기](media/marketplace-publishing-push-to-staging/portal-catalog-01.png)

*Azure Preview 포털에서 제품 목록*

![그리기](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

*Azure Preview 포털에서 제품 설명 정보*

## 다음 단계
마켓플레이스 콘텐츠를 로드했으므로 스테이징에서 제품 테스트를 진행하겠습니다. 그러나 제품 유형별로 다양한 단계에 따라 아래 목록에서 적절한 제품 유형을 선택해야 합니다.

|가상 컴퓨터 이미지 |개발자 서비스 | 데이터 서비스 | 솔루션 템플릿 |
|----|----|----|----|----|
| **3단계. 제품을 스테이징으로 푸시** | [스테이징에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md) | 스테이징에서 개발자 서비스 제품 테스트 | [스테이징에서 데이터 서비스 제품 테스트](marketplace-publishing-data-service-test-in-staging.md) | [스테이징에서 솔루션 템플릿 테스트](marketplace-publishing-solution-template-test-in-staging.md) |

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0128_2016-->