---
title: Form Recognizer란?
titleSuffix: Azure Cognitive Services
description: Form Recognizer를 사용하여 양식 및 테이블 데이터를 구문 분석하는 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 8fb382227c71fce7ebe062057adf5edfb90a1a92
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601632"
---
# <a name="what-is-form-recognizer"></a>Form Recognizer란?

Azure Form Recognizer는 기계 학습 기술을 사용하여 양식 문서에서 키-값 쌍과 테이블 데이터를 식별하고 추출하는 인지 서비스입니다. 그런 다음, 원본 파일의 관계가 포함된 정형 데이터를 출력합니다. 간단한 REST API를 통해 사용자 지정 Form Recognizer 모델을 호출하여 복잡성을 줄이고 워크플로 또는 애플리케이션에 쉽게 통합할 수 있습니다. 시작하려면 5개의 양식 문서 또는 입력 자료와 동일한 형식의 빈 양식만 필요합니다. 많은 수동 작업 또는 광범위한 데이터 과학 전문 지식 없이도 특정 콘텐츠에 맞게 조정된 정확한 결과를 빠르게 얻을 수 있습니다.

## <a name="request-access"></a>액세스 요청
Form Recognizer는 제한된 액세스 미리 보기에서 사용할 수 있습니다. 미리 보기에 액세스하려면 [Form Recognizer 액세스 요청](https://aka.ms/FormRecognizerRequestAccess) 양식을 작성하여 제출하세요. 이 양식에서는 Form Recognizer를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. Azure Cognitive Services 팀에서 해당 요청이 승인되면 서비스에 액세스하기 위한 지침이 포함된 이메일을 받게 됩니다.

## <a name="what-it-does"></a>기능

입력 데이터를 제출하면 알고리즘에서 해당 데이터에 대해 학습하고, 형식별로 양식을 클러스터하고, 존재하는 키와 테이블을 검색하고, 값을 키에 연결하고 항목을 테이블에 연결하는 방법을 학습합니다. 자율 학습을 사용하면 수동으로 데이터 레이블을 지정하거나 집중적인 코딩 및 유지 관리 작업 없이도 모델이 필드와 항목 간의 레이아웃 및 관계를 이해할 수 있습니다. 반면, 미리 학습된 기계 학습 모델에서는 표준화된 데이터가 필요하며, 산업 특정 양식과 같이 기존 형식에서 벗어나는 입력 자료와 함께 사용하면 정확도가 떨어집니다.

모델이 학습되면 데이터를 더 많은 양식에서 안정적으로 추출하기 위해 필요에 따라 모델을 테스트, 재학습 및 최종적으로 사용할 수 있습니다.

## <a name="what-it-includes"></a>포함되는 항목

Form Recognizer는 REST API로 사용할 수 있습니다. API를 호출하여 모델을 만들고, 학습시키고, 채점할 수 있습니다. 원하는 경우 로컬 Docker 컨테이너에서 모델을 실행할 수 있습니다.

## <a name="input-requirements"></a>입력 요구 사항

Form Recognizer는 다음 요구 사항을 충족하는 입력 문서에서 작동합니다.

* JPG, PNG 또는 PDF 형식(텍스트 또는 스캔)이어야 합니다. 문자 추출 및 위치에 오류가 발생할 가능성이 없으므로 텍스트가 포함된 PDF가 가장 좋습니다.
* 파일 크기는 4MB(메가바이트) 미만이어야 합니다.
* 이미지에 대한 크기는 50 x 50 픽셀에서 4,200 x 4,200 픽셀 사이여야 합니다.
* 종이 문서에서 스캔한 경우 양식은 고품질 스캔이어야 합니다.
* 텍스트는 라틴어 알파벳(영어 문자)을 사용해야 합니다.
* 데이터는 인쇄체여야 합니다(필기체가 아님).
* 데이터는 키와 값을 포함해야 합니다.
* 키는 값의 위쪽 또는 왼쪽에 표시될 수 있지만, 아래쪽 또는 오른쪽에는 표시될 수 없습니다.

Form Recognizer에서 현재 지원하지 않는 입력 데이터의 유형은 다음과 같습니다.

* 복합 테이블(중첩된 테이블, 병합된 헤더 또는 셀 등)
* 확인란 또는 라디오 단추
* 50페이지가 넘는 PDF 문서

## <a name="where-do-i-start"></a>시작 단계

**1단계:** Azure Portal에서 Form Recognizer 리소스를 만듭니다.

**2단계:** 실습용 빠른 시작을 시도합니다.
* [빠른 시작: cURL에서 REST API를 사용하여 Form Recognizer 모델 학습 및 양식 데이터 추출](quickstarts/curl-train-extract.md)
* [빠른 시작: Python에서 REST API를 사용하여 Form Recognizer 모델 학습 및 양식 데이터 추출](quickstarts/python-train-extract.md)

기술을 습득하는 경우 체험 서비스를 사용하는 것이 좋지만, 체험 페이지 수는 매월 500페이지로 제한된다 점에 유의하세요.

**3단계:** REST API 검토

다음 API를 사용하여 양식에서 정형 데이터를 학습하고 추출합니다.

| REST API | 설명 |
|-----|-------------|
| 학습 | 동일한 형식 또는 빈 양식의 5개 양식을 사용하여 양식을 분석하는 새 모델을 학습시킵니다.  |
| 분석  |스트림으로 전달된 단일 문서를 분석하여 사용자 지정 모델을 통해 양식에서 키-값 쌍과 테이블을 추출합니다.  |

[REST API 참조 문서](https://aka.ms/form-recognizer/api)를 살펴보세요. 

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

이 서비스는 [온라인 서비스 약관](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)에 따라 Azure 서비스의 [미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 제공됩니다. 모든 Cognitive Services와 마찬가지로 Form Recognizer 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[빠른 시작](quickstarts/curl-train-extract.md)을 수행하여 [Form Recognizer API](https://aka.ms/form-recognizer/api)를 시작합니다.
