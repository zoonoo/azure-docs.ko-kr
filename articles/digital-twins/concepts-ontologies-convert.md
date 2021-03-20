---
title: 온톨로지 변환
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에 대해 산업 표준 모델을 DTDL로 변환 하는 프로세스 이해
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561754"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Azure Digital Twins에 대해 업계 표준 온톨로지를 DTDL로 변환

대부분의 [온톨로지](concepts-ontologies.md) 는 [OWL](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)및 [rdfs](https://www.w3.org/2001/sw/wiki/RDFS)와 같은 의미 체계 웹 표준을 기반으로 합니다. 

Azure Digital Twins에서 모델을 사용 하려면 DTDL 형식 이어야 합니다. 이 문서에서는 RDF 기반 모델을 DTDL로 변환 하 여 Azure Digital Twins에서 사용할 수 있도록 **변환 패턴** 형식의 일반적인 디자인 지침을 설명 합니다. 

이 문서에는 빌드 업계의 다른 스키마에 대해 확장 될 수 있는 RDF 및 OWL 변환기에 대 한 [**샘플 변환기 코드도**](#converter-samples) 포함 되어 있습니다.

## <a name="conversion-pattern"></a>변환 패턴

RDF 기반 모델을 DTDL로 변환할 때 사용할 수 있는 여러 타사 라이브러리가 있습니다. 이러한 라이브러리 중 일부를 사용 하 여 그래프에 모델 파일을 로드할 수 있습니다. 그래프를 반복 하 여 특정 RDFS 및 OWL 구문을 찾고이를 DTDL로 변환할 수 있습니다.   

다음 표는 RDFS 및 OWL 구문을 DTDL에 매핑할 수 있는 방법의 예입니다. 

| RDFS/OWL 개념 | RDFS/OWL 구문 | DTDL 개념 | DTDL 구문 |
| --- | --- | --- | --- |
| 클래스 | `owl:Class`<br>IRI 접미사<br>``rdfs:label``<br>``rdfs:comment`` | 인터페이스 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| 를 서브클래싱합니다. | `owl:Class`<br>IRI 접미사<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | 인터페이스 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Datatype 속성 | `owl:DatatypeProperty`<br>`rdfs:label` 또는 `INode`<br>`rdfs:label`<br>`rdfs:range` | 인터페이스 속성 | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| 개체 속성 | `owl:ObjectProperty`<br>`rdfs:label` 또는 `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | 관계 | `type:Relationship`<br>`name`<br>`target` (그렇지 않은 경우 생략 `rdfs:range` )<br>`comment`<br>`displayName`<br>

다음 c # 코드 조각에서는 [**dotNetRDF**](https://www.dotnetrdf.org/) 라이브러리를 사용 하 여 RDF 모델 파일이 그래프로 로드 되 고 dtdl로 변환 되는 방법을 보여 줍니다. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>변환기 샘플

### <a name="rdf-converter-application"></a>RDF 변환기 응용 프로그램 

Azure Digital Twins 서비스에서 사용 하기 위해 RDF 기반 모델 파일을 [Dtdl (버전 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 로 변환 하는 사용 가능한 샘플 응용 프로그램이 있습니다. [Brick](https://brickschema.org/ontology/) 스키마에 대해 유효성이 검사 되었으며 건물 업계의 다른 스키마 ( [봇 Ontology (봇)](https://w3c-lbd-cg.github.io/bot/), [의미 체계 센서 네트워크](https://www.w3.org/TR/vocab-ssn/)또는 [buildingSmart의 IFC (업계 Foundation 클래스](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)))를 위해 확장할 수 있습니다.

이 샘플은 **Rdftodtdlconverter** 라는 .net Core 명령줄 응용 프로그램입니다.

[**Rdftodtdlconverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)에서 샘플을 다운로드할 수 있습니다. 

컴퓨터에 코드를 다운로드 하려면 샘플 방문 페이지의 제목 아래에 있는 *ZIP 다운로드* 단추를 누릅니다. 그러면 *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* 이름으로 *ZIP* 파일이 다운로드 됩니다. 그러면 압축을 풀고 탐색할 수 있습니다.

이 샘플을 사용 하 여 컨텍스트에서 변환 패턴을 확인 하 고 고유한 특정 요구 사항에 따라 모델 변환을 수행 하는 고유한 응용 프로그램의 빌딩 블록으로 지정할 수 있습니다.

### <a name="owl2dtdl-converter"></a>OWL2DTDL 변환기 

[**OWL2DTDL 변환기**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) 는 OWL Ontology를 Azure Digital twins 서비스와 함께 사용할 수 있는 dtdl 인터페이스 선언 집합으로 변환 하는 샘플입니다. 또한 하나의 루트 ontology 다른 온톨로지를 사용 하 여 선언 된 ontology 네트워크에 대해 작동 `owl:imports` 합니다.

이 변환기는 [부동산 핵심 Ontology](https://doc.realestatecore.io/3.1/full.html) 를 dtdl로 변환 하는 데 사용 되었으며 모든 OWL 기반 Ontology에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계 

* 산업 표준 온톨로지 확장에 대 한 자세한 내용은 [*개념: 산업 온톨로지 확장*](concepts-ontologies-extend.md)을 준수 하세요.

* 또는 온톨로지을 기반으로 하는 모델을 개발 하는 경로에서 계속 합니다. [*모델 개발 경로에 ontology 전략을 사용*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)합니다.