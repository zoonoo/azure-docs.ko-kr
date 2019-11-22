---
title: Form Recognizer란?
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Form Recognizer를 사용하면 양식 문서에서 키/값 쌍과 테이블 데이터를 식별하고 추출할 수 있습니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 86e0494ded84e9711b2283ae5fad77ff626cf792
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904495"
---
# <a name="what-is-form-recognizer"></a>Form Recognizer란?

Azure Form Recognizer는 기계 학습 기술을 사용하여 양식 문서에서 키-값 쌍과 테이블 데이터를 식별하고 추출하는 인지 서비스입니다. 그런 다음, 원본 파일의 관계가 포함된 정형 데이터를 출력합니다. 간단한 REST API를 통해 사용자 지정 Form Recognizer 모델을 호출하여 복잡성을 줄이고 워크플로 또는 애플리케이션에 쉽게 통합할 수 있습니다. 시작하려면 5개의 채워진 양식 문서 또는 2개의 채워진 양식과 함께 입력 자료와 동일한 형식의 빈 양식만 필요합니다. 많은 수동 작업 또는 광범위한 데이터 과학 전문 지식 없이도 특정 콘텐츠에 맞게 조정된 정확한 결과를 빠르게 얻을 수 있습니다.

## <a name="custom-models"></a>사용자 지정 모델

Form Recognizer 사용자 지정 모델은 자신의 데이터로 훈련하며, 시작하려면 5개의 샘플 입력 양식만 필요합니다. 입력 데이터를 제출하면 알고리즘에서 형식별로 양식을 클러스터하고, 존재하는 키와 테이블을 검색하고, 값을 키에 연결하고 항목을 테이블에 연결합니다. 그런 다음, 원본 파일의 관계가 포함된 정형 데이터를 출력합니다. 모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

자율 학습을 사용하면 수동으로 데이터 레이블을 지정하거나 집중적인 코딩 및 유지 관리 작업 없이도 모델이 필드와 항목 간의 레이아웃 및 관계를 이해할 수 있습니다. 반면, 미리 학습된 기계 학습 모델에는 표준화된 데이터가 필요합니다. 그것들은 산업별 형식과 같이 전통적인 형식에서 벗어나는 입력 자료로 정확도가 떨어집니다.

## <a name="prebuilt-receipt-model"></a>미리 작성된 영수증 모델

Form Recognizer에는 판매 영수증을 읽기 위한 모델도 포함됩니다. 이 모델은 트랜잭션 시간 및 날짜, 판매자 정보, 세금의 합계 등과 같은 주요 정보를 추출합니다. 또한 미리 작성된 영수증 모델은 영수증의 모든 텍스트를 인식하고 반환하도록 학습됩니다.

## <a name="what-it-includes"></a>포함되는 항목

Form Recognizer는 REST API로 사용할 수 있습니다. 이러한 API를 호출하여 사용자 지정 모델을 만들고 학습시키고 점수를 매기거나, 미리 작성된 모델에 액세스할 수 있습니다. 원하는 경우 로컬 Docker 컨테이너에서 사용자 지정 모델을 학습하고 실행할 수 있습니다.

## <a name="input-requirements"></a>입력 요구 사항
### <a name="custom-model"></a>사용자 지정 모델

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>미리 작성된 영수증 모델

영수증 모델에 대한 입력 요구 사항은 약간 다릅니다.

* 형식은 JPEG, PNG, BMP, PDF(텍스트 또는 스캔) 또는 TIFF여야 합니다.
* 파일 크기는 20MB 미만이어야 합니다.
* 이미지 크기는 50x50 픽셀에서 10,000x10,000 픽셀 사이여야 합니다. 
* PDF 크기는 최대 17x17인치(Legal 또는 A3 용지 크기 이하에 해당)여야 합니다.
* PDF 및 TIFF의 경우 처음 200페이지만 처리됩니다(체험 계층 구독의 경우 처음 2페이지만 처리됨).

## <a name="request-access"></a>액세스 요청

Form Recognizer는 제한된 액세스 미리 보기에서 사용할 수 있습니다. 미리 보기에 액세스하려면 [Form Recognizer 액세스 요청](https://aka.ms/FormRecognizerRequestAccess) 양식을 작성하여 제출하세요. 이 양식에서는 Form Recognizer를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. Azure Cognitive Services 팀에서 해당 요청이 승인되면 서비스에 액세스하기 위한 지침이 포함된 이메일을 받게 됩니다.

## <a name="where-do-i-start"></a>시작 단계

**1단계:** Azure Portal에서 Form Recognizer 리소스를 만듭니다.

**2단계:** REST API를 사용하려면 빠른 시작을 수행합니다.
* [빠른 시작: cURL에서 REST API를 사용하여 Form Recognizer 모델 학습 및 양식 데이터 추출](quickstarts/curl-train-extract.md)
* [빠른 시작: Python에서 REST API를 사용하여 Form Recognizer 모델 학습 및 양식 데이터 추출](quickstarts/python-train-extract.md)
* [빠른 시작: cURL을 사용하여 영수증 데이터 추출](quickstarts/curl-receipts.md)
* [빠른 시작: Python을 사용하여 영수증 데이터 추출](quickstarts/python-receipts.md)

기술을 학습할 때 체험판 서비스를 이용하는 것이 좋습니다. 체험판 페이지는 한 달에 500페이지로 제한되어 있다는 점을 유념하세요.

**3단계:** REST API 검토

다음 API를 사용하여 양식에서 정형 데이터를 학습하고 추출합니다.

|||
|---|---|
| 모델 학습| 동일한 형식의 5개 양식을 사용하여 양식을 분석하는 새 모델을 학습시킵니다. 또는 빈 양식 및 두 개의 채워진 양식을 사용하여 학습시킵니다.  |
| 양식 분석 |스트림으로 전달된 단일 문서를 분석하여 사용자 지정 모델을 통해 양식에서 키/값 쌍과 테이블을 추출합니다.  |
| 영수증 분석 |단일 영수증 문서를 분석하여 키 정보 및 다른 영수증 텍스트를 추출합니다.|

자세히 알아보려면 [REST API 참조 설명서](https://aka.ms/form-recognizer/api)를 확인하세요. 

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

이 서비스는 [온라인 서비스 약관](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)에 따라 Azure 서비스의 [미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 제공됩니다. 모든 Cognitive Services와 마찬가지로 Form Recognizer 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[빠른 시작](quickstarts/curl-train-extract.md)을 수행하여 [Form Recognizer API](https://aka.ms/form-recognizer/api)를 시작합니다.