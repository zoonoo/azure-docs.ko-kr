---
title: 수신-양식 인식기
titleSuffix: Azure Cognitive Services
description: 인식기 API 사용 및 제한으로 수신 분석과 관련 된 개념을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 7a14b3c93a6c545648faf28c991764e990e487b1
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724984"
---
# <a name="receipt-concepts"></a>수신 개념

AzureForm 인식기는 미리 작성 된 모델 중 하나를 사용 하 여 영수증을 분석할 수 있습니다. 수신 API는 판매 영수증에서 판매 영수증의 핵심 정보를 추출 합니다 (예: 상인 이름, 거래 날짜, 트랜잭션 합계, 품목 등). 

## <a name="understanding-receipts"></a>수신 확인 이해 

비즈니스 경비 보고서, 상환을, 세금, 예산 또는 기타 목적에 관계 없이 많은 비즈니스와 개인은 여전히 판매 영수증에서 데이터를 수동으로 추출 하는 데 의존 합니다. 일반적으로 이러한 시나리오에서는 유효성 검사를 위해 물리적 수신의 이미지가 필요 합니다.  

이러한 수신 확인에서 데이터를 자동으로 추출 하는 것은 복잡할 수 있습니다. 수신 확인은 crumpled 하 고 읽기 어려울 수 있으며, 스마트폰의 확인 이미지는 품질이 낮을 수 있습니다. 또한 수신 템플릿 및 필드는 시장, 지역 및 판매자에 따라 크게 다를 수 있습니다. 데이터 추출 및 필드 검색 모두에서 이러한 문제는 고유한 문제를 처리 하는 것입니다.  

확인 API는 OCR (광학 문자 인식) 및 미리 작성 된 수신 모델을 사용 하 여 이러한 수신 처리 시나리오를 가능 하 게 합니다. 모델은 데이터에 대해 미리 학습 되므로 &mdash; 모델 학습 또는 레이블 지정이 필요 하지 않은 한 단계에서 쉽게 확인을 분석할 수 있습니다.

![샘플 수신](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>수신 API는 무엇을 하나요? 

미리 작성 된 수신 API는 &mdash; 식당, 소매점 또는 식료품 스토어에서 일반적으로 받게 되는 수신의 유형에 대 한 판매 영수증의 콘텐츠를 추출 합니다.

### <a name="fields-extracted"></a>추출 된 필드

* 판매자 이름 
* 판매자 주소 
* 판매자 전화 번호 
* 트랜잭션 날짜 
* 트랜잭션 시간 
* 소계 
* 세금 
* 합계 
* 팁 
* 줄 항목 추출 (예: 항목 수량, 항목 가격, 항목 이름)

### <a name="additional-features"></a>추가 기능

또한 수신 API는 다음 정보를 반환 합니다.

* 수신 유형 (예: 항목별, 신용 카드 등)
* 필드 신뢰 수준 (각 필드는 연결 된 신뢰도 값을 반환)
* OCR 원시 텍스트 (전체 수신에 대 한 OCR 추출 된 텍스트 출력)

## <a name="input-requirements"></a>입력 요구 사항

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>지원 되는 로캘 

* GA ( **미리 작성 된 수신** )는 en-us 로캘의 판매 영수증을 지원 합니다.
* **미리 작성 된 수령 v 2.1-preview. 1** (공개 미리 보기)에는 다음 EN 수신 로캘에 대 한 추가 지원이 추가 되었습니다. 
  * EN-오스트레일리아 
  * EN-CA 
  * EN-US 
  * EN-US 

  > [!NOTE]
  > 언어 입력 
  >
  > 미리 작성 된 수신 v 2.1-preview. 1에는 추가 영어 시장에서 수신 로캘을 지정 하는 선택적 요청 매개 변수가 있습니다. 오스트레일리아 (EN-US), 캐나다 (EN-US), 뛰어난 Britain (en-us) 및 인도 (EN-US)의 영어로 판매 영수증을 위해 로캘을 지정 하 여 향상 된 결과를 얻을 수 있습니다. V 2.1-preview. 1에 지정 된 로캘이 없으면 모델은 기본적으로 EN-US 모델로 지정 됩니다.

## <a name="customer-scenarios"></a>고객 시나리오  

수신 API를 사용 하 여 추출 된 데이터를 사용 하 여 다양 한 작업을 수행할 수 있습니다. 다음은 고객이 수신 API를 사용 하 여 수행한 작업의 몇 가지 예입니다. 

### <a name="business-expense-reporting"></a>비즈니스 비용 보고  

비즈니스 경비를 확인 하는 것은 종종 수신 이미지에서 데이터를 수동으로 입력 하는 시간을 지출 합니다. 수신 API를 사용 하면 추출 된 필드를 사용 하 여이 프로세스를 부분적으로 자동화 하 고 확인을 신속 하 게 분석할 수 있습니다.  

수신 API에는 간단한 JSON 출력이 있으므로 추출 된 필드 값을 여러 가지 방법으로 사용할 수 있습니다. 내부 비용 응용 프로그램과 통합 하 여 경비 보고서를 미리 채웁니다. 이 시나리오에 대 한 자세한 내용은 Acumatica에서 수신 API를 활용 하 여 [비용을 보고](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure)하는 프로세스를 확인 하는 방법을 참조 하세요.  

### <a name="auditing-and-accounting"></a>감사 및 계정 

수신 API 출력은 expense reporting 및 보상 프로세스의 다양 한 지점에서 많은 비용을 분석 하는 데 사용할 수도 있습니다. 수신 확인을 처리 하 여 수동 감사 또는 빠른 승인에 대해 심사를 수행할 수 있습니다.  

수신 출력은 업무용 또는 개인용 사용을 위한 일반 책에도 유용 합니다. 수신 API를 사용 하 여 원시 수신 이미지/PDF 데이터를 실행 가능한 디지털 출력으로 변환 합니다.

### <a name="consumer-behavior"></a>소비자 동작 

수신 확인에는 소비자 동작 및 쇼핑 추세를 분석 하는 데 사용할 수 있는 유용한 데이터가 포함 되어 있습니다.

