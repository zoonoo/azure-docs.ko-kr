---
title: Ontology 무엇 인가요?
titleSuffix: Azure Digital Twins
description: 특정 도메인에서의 모델링을 위한 DTDL 업계 온톨로지에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b53cac6e732fb568ba799534d9ad9168fd65eab1
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561531"
---
# <a name="what-is-an-ontology"></a>Ontology 무엇 인가요? 

Azure Digital Twins 솔루션의 어휘는 환경에 존재 하는 엔터티의 유형을 설명 하는 [모델](concepts-models.md)을 사용 하 여 정의 됩니다.

경우에 따라 솔루션을 특정 업계에 연결 하는 경우 자체 모델 집합을 처음부터 직접 작성 하는 대신 이미 존재 하는 해당 업계의 모델 집합으로 시작 하는 것이 더 쉽고 효과적일 수 있습니다. 이러한 기존 모델 집합을 **온톨로지** 이라고 합니다. 

일반적으로 ontology는 건물 구조, IoT 시스템, 스마트 도시, 에너지 그리드, 웹 콘텐츠 등과 같은 지정 된 도메인에 대 한 모델 집합입니다. 온톨로지는 사용할 수 있는 기술 자료 그래프의 스키마로 종종 사용 됩니다.
* 소프트웨어 구성 요소, 설명서, 쿼리 라이브러리 등의은
* 개념적 모델링 및 시스템 개발에 대 한 투자 감소
* 의미 체계 수준에서 데이터 상호 운용성을 용이 하 게 합니다.
* 모범 사례를 처음부터 다시 시작 하거나 "reinventing"를 시작 하는 대신 다시 사용 합니다.

이 문서에서는 Azure Digital Twins 모델에 대해 온톨로지를 사용 하는 이유와 현재 사용할 수 있는 온톨로지 및 도구에 대해 설명 합니다.

## <a name="using-ontologies-for-azure-digital-twins"></a>Azure Digital Twins에 온톨로지 사용

온톨로지는 디지털 쌍 솔루션에 대 한 유용한 시작 지점을 제공 합니다. 이러한 모델은 디지털 쌍 그래프의 디자인, 생성 및 구문 분석을 위해 도메인별 모델 집합과 엔터티 간의 관계를 포함 합니다. 온톨로지를 사용 하면 솔루션 개발자가 검증 된 시작 지점에서 디지털 쌍 솔루션을 시작 하 고 비즈니스 문제 해결에 집중할 수 있습니다. Microsoft에서 제공 하는 온톨로지을 쉽게 확장할 수 있도록 설계 되었으므로 솔루션에 맞게 사용자 지정할 수 있습니다. 

또한 솔루션에서 이러한 온톨로지를 사용 하 여 여러 파트너와 공급 업체 간의 원활한 통합을 위해이를 설정할 수 있습니다. 온톨로지는 솔루션 전반에 공통적인 어휘를 제공할 수 있기 때문입니다.

Azure Digital Twins의 모델은 [dtdl (디지털 Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)로 표시 되기 때문에 Azure Digital twins에 사용 되는 온톨로지는 dtdl에도 작성 됩니다. 

## <a name="strategies-for-integrating-ontologies"></a>온톨로지 통합 전략

업계 표준 온톨로지를 DTDL과 통합 하는 세 가지 전략을 사용할 수 있습니다. 요구 사항에 따라 가장 적합 한 항목을 선택할 수 있습니다.

| 전략 | Description | 리소스 |
| --- | --- | --- |
| **채택** | 널리 채택 된 산업 표준을 기반으로 하는 오픈 소스 DTDL ontology 솔루션을 시작할 수 있습니다. 이러한 모델 집합을 기본으로 사용 하거나 사용자 지정 솔루션에 대 한 사용자 고유의 추가 기능을 사용 하 여 확장할 수 있습니다. | [*개념: &nbsp; &nbsp; 업계 &nbsp; 표준 온톨로지 채택*](concepts-ontologies-adopt.md)<br><br>[*개념: &nbsp; &nbsp; 온톨로지 확장*](concepts-ontologies-extend.md) |
| **변환** | 이미 다른 표준 형식으로 표시 된 기존 모델이 있는 경우 DTDL로 변환 하 여 Azure Digital Twins에서 사용할 수 있습니다. | [*개념: &nbsp; &nbsp; 온톨로지 변환*](concepts-ontologies-convert.md)<br><br>[*개념: &nbsp; &nbsp; 온톨로지 확장*](concepts-ontologies-extend.md) |
| **작성자** | 언제 든 지 적절 한 업계 표준을 사용 하 여 사용자 고유의 사용자 지정 DTDL 모델을 처음부터 개발할 수 있습니다. | [*개념: DTDL 모델*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>모델 개발 경로에서 ontology 전략 사용

Azure Digital Twins에 ontology를 통합 하기 위해 어떤 전략을 선택 하든지, 아래의 전체 경로를 따라 ontology를 DTDL 모델로 만들고 업로드 하는 과정을 안내할 수 있습니다.

1. [Azure Digital Twins에서 Dtdl 모델링](concepts-models.md)을 검토 하 고 이해 하는 것부터 시작 합니다.
1. 위에서 선택한 ontology 통합 전략을 계속 진행 합니다. ontology를 기반으로 모델을 [**채택**](concepts-ontologies-adopt.md), [**변환**](concepts-ontologies-convert.md)또는 [**제작**](concepts-models.md) 합니다.
    1. 필요한 경우 ontology를 [확장](concepts-ontologies-extend.md) 하 여 필요에 맞게 사용자 지정 합니다.
1. 모델 [의 유효성을 검사](how-to-parse-models.md) 하 여 dtdl 문서가 작동 하는지 확인 합니다.
1. [Api](how-to-manage-model.md#upload-models) 또는 [Azure digital twins 모델 업 로더](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader)와 같은 샘플을 사용 하 여 완성 된 모델을 azure digital twins에 업로드 합니다.

그런 후에는 Azure Digital Twins 인스턴스에서 모델을 사용할 수 있습니다. 

[Azure digital 쌍 (ADT) 탐색기](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) 또는 [ADT 모델 시각화 도우미](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)와 같은 샘플을 시각화 하거나,이를 사용 하 여 [디지털](concepts-twins-graph.md)쌍을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

온톨로지 도입, 변환 및 제작 전략에 대 한 자세한 내용은 다음을 참조 하세요.
* [*개념: 업계 표준 온톨로지 도입*](concepts-ontologies-adopt.md)
* [*개념: 온톨로지 변환*](concepts-ontologies-convert.md)
* [*방법: DTDL 모델 관리*](how-to-manage-model.md)

또는 모델을 사용 하 여 디지털 쌍을 만드는 방법에 대해 알아봅니다. [*개념: 디지털 쌍 및 쌍 그래프*](concepts-twins-graph.md).