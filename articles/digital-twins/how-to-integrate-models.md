---
title: 업계 표준 모델 통합
titleSuffix: Azure Digital Twins
description: 특수 DTDL 온톨로지를 사용 하거나 기존 온톨로지를 변환 하 여 Azure Digital Twins의 DTDL에 업계 표준 모델을 통합 하는 방법을 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1866e9b233a1379474c061779ada09fd6d119107
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338400"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Azure Digital Twins 용 DTDL과 업계 표준 모델 통합

업계 표준을 기반으로 하는 모델을 사용 하거나 RDF 또는 OWL와 같은 표준 ontology 표현을 사용 하면 Azure Digital Twins 모델을 디자인할 때 다양 한 시작 지점을 제공 합니다. 산업 모델을 사용 하면 표준화 및 정보 공유에도 도움이 됩니다.

Azure Digital Twins에서 사용 하려면 모델이 JSON-LD 기반 [**디지털 Twins 정의 언어 (DTDL)**](concepts-models.md)로 표시 되어야 합니다. 따라서이 문서에서는 DTDL에서 업계 표준 모델을 표시 하는 방법에 대해 설명 하 고, Azure Digital Twins에서 사용할 수 있도록 기존 업계 개념을 DTDL 의미 체계와 통합 합니다. 그런 다음 DTDL 모델은 Azure Digital Twins 내에서 모델에 대 한 진위의 원본으로 사용 됩니다.

DTDL과 업계 표준 모델을 통합 하는 데는 세 가지 가능한 경로가 있습니다.
* **채택** : 널리 채택 된 산업 표준을 기반으로 하는 오픈 소스 dtdl ontology 솔루션을 시작할 수 있습니다. 
* **변환** : 기존 모델이 이미 있는 경우 dtdl로 변환 해야 합니다.
* **작성자** : [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)에 설명 된 대로 항상 사용자 지정 dtdl 모델을 처음부터 개발할 수 있습니다.

## <a name="adopt-an-open-source-dtdl-ontology"></a>오픈 소스 DTDL ontology 도입

빈 페이지에서 시작 하는 것 보다 오픈 소스 DTDL ontology 시작 하는 것이 더 쉽습니다. 

예를 들어, 스마트 빌딩 솔루션은 오픈 소스 [**Dtdl 기반 RealEstateCore ontology**](https://github.com/Azure/opendigitaltwins-building)를 활용할 수 있습니다 .이를 통해 업계 표준을 활용 하 여 reinvention을 방지 하는 데 사용 되는 스마트 건물을 모델링 하는 데 일반적인 접지를 제공 

이러한 오픈 소스 DTDL 온톨로지는 모델을 사용 하 고 적절 하 게 확장 하는 방법에 대 한 모범 사례도 제공 합니다. 

## <a name="convert-existing-models-to-dtdl"></a>기존 모델을 DTDL로 변환

대부분의 업계 모델 ( **온톨로지** 라고도 함)은 [OWL](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)및 [rdfs](https://www.w3.org/2001/sw/wiki/RDFS)와 같은 의미 체계 웹 표준을 기반으로 합니다. 

Azure Digital Twins에서 모델을 사용 하려면 DTDL 형식 이어야 합니다. 이 섹션에서는 RDF 기반 모델을 DTDL로 변환 하 여 Azure Digital Twins에서 사용할 수 있도록 **변환 패턴** 형식의 일반적인 디자인 지침에 대해 설명 합니다. 

또한 [Brick](https://brickschema.org/ontology/) 스키마에 대해 유효성을 검사 하 고 건물 업계의 다른 스키마를 위해 확장할 수 있는 [RDF 변환기에 대 한 **샘플 변환기 코드도**](#sample-converter-application)포함 되어 있습니다.

### <a name="conversion-pattern"></a>변환 패턴

RDF 기반 모델을 DTDL로 변환할 때 사용할 수 있는 여러 타사 라이브러리가 있습니다. 이러한 라이브러리 중 일부를 사용 하 여 그래프에 모델 파일을 로드할 수 있습니다. 그래프를 반복 하 여 특정 RDFS 및 OWL 구문을 찾고이를 DTDL로 변환할 수 있습니다.   

다음 표는 RDFS 및 OWL 구문을 DTDL에 매핑할 수 있는 방법의 예입니다. 

| RDFS/OWL 개념 | RDFS/OWL 구문 | DTDL 개념 | DTDL 구문 |
| --- | --- | --- | --- |
| 클래스 | `owl:Class`<br>IRI 접미사<br>``rdfs:label``<br>``rdfs:comment`` | 인터페이스 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| 를 서브클래싱합니다. | `owl:Class`<br>IRI 접미사<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | 인터페이스 | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Datatype 속성 | `owl:DatatypeProperty`<br>`rdfs:label` 또는 `INode`<br>`rdfs:label`<br>`rdfs:range` | 인터페이스 속성 | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| 개체 속성 | `owl:ObjectProperty`<br>`rdfs:label` 또는 `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | 관계 | `type:Relationship`<br>`name`<br>`target` (그렇지 않은 경우 생략 `rdfs:range` )<br>`comment`<br>`displayName`<br>

다음 c # 코드 조각에서는 [**dotNetRDF**](https://www.dotnetrdf.org/) 라이브러리를 사용 하 여 RDF 모델 파일이 그래프로 로드 되 고 dtdl로 변환 되는 방법을 보여 줍니다. 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

### <a name="sample-converter-application"></a>샘플 변환기 응용 프로그램 

Azure Digital Twins 서비스에서 사용 하기 위해 RDF 기반 모델 파일을 [Dtdl (버전 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 로 변환 하는 사용 가능한 샘플 응용 프로그램이 있습니다. [Brick](https://brickschema.org/ontology/) 스키마에 대해 유효성이 검사 되었으며 건물 업계의 다른 스키마 ( [봇 Ontology (봇)](https://w3c-lbd-cg.github.io/bot/), [의미 체계 센서 네트워크](https://www.w3.org/TR/vocab-ssn/)또는 [buildingSmart의 IFC (업계 Foundation 클래스](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)))를 위해 확장할 수 있습니다.

이 샘플은 **Rdftodtdlconverter** 라는 .net Core 명령줄 응용 프로그램입니다.

[**Rdftodtdlconverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)에서 샘플을 다운로드할 수 있습니다. 

컴퓨터에 코드를 다운로드 하려면 샘플 방문 페이지의 제목 아래에 있는 *ZIP 다운로드* 단추를 누릅니다. 그러면 *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* 이름으로 *ZIP* 파일이 다운로드 됩니다. 그러면 압축을 풀고 탐색할 수 있습니다.

이 샘플을 사용 하 여 컨텍스트에서 변환 패턴을 확인 하 고 고유한 특정 요구 사항에 따라 모델 변환을 수행 하는 고유한 응용 프로그램의 빌딩 블록으로 지정할 수 있습니다.

## <a name="validate-and-upload-dtdl-models"></a>DTDL 모델 유효성 검사 및 업로드

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

모델을 변환 하 고 유효성을 검사 한 후에는 **Azure Digital Twins 인스턴스에 업로드할** 수 있습니다. 이 프로세스에 대 한 자세한 내용은 *방법: 사용자 지정 모델 관리* 의 [*모델 업로드*](how-to-manage-model.md#upload-models) 섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계 

디지털 쌍 모델 디자인 및 관리에 대해 자세히 알아보세요.
 
* [*개념: 사용자 지정 모델*](concepts-models.md)
* [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)
* [*방법: 모델 구문 분석 및 유효성 검사*](how-to-parse-models.md)
