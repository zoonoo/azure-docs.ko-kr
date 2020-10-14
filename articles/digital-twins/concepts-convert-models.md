---
title: 산업 표준 모델 변환
titleSuffix: Azure Digital Twins
description: 산업 표준 (RDF/OWL) 모델을 DTDL로 변환 하기 위한 패턴 이해
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: bf39bd0c787ed64d573cc2a257442b4d3bb8ab7d
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015117"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Azure Digital Twins에 대해 산업 표준 모델을 DTDL로 변환

Azure Digital Twins의 모델은 JSON-LD 기반 [**디지털 Twins 정의 언어 (DTDL)**](concepts-models.md)로 표시 됩니다. RDF 또는 OWL와 같은 업계 표준을 기반으로 하는 Azure Digital Twins 외부에 기존 모델이 있는 경우이를 **DTDL로 변환** 하 여 Azure Digital twins와 함께 사용 해야 합니다. 그러면 DTDL 버전이 Azure Digital Twins 내에서 모델에 대 한 원인이 됩니다.

이 문서에서는 RDF 기반 산업 또는 사용자 지정 모델을 DTDL로 변환 하기 위한 패턴을 설명 합니다. 다음을 포함합니다.
* 다양 한 표준 및 모델 유형에 적용할 수 있는 **전략 수준 지침**
* [RDF 특정 변환기에 대 한 **샘플 코드**](#sample-converter-application)

## <a name="industry-models"></a>산업 모델  

업계 모델을 사용 하면 Azure Digital Twins 모델을 디자인할 때 다양 한 시작 지점을 제공 합니다. 산업 모델을 사용 하면 표준화 및 정보 공유에도 도움이 됩니다. 

일반적인 산업 모델에는 다음이 포함 됩니다.  

| 업계 수직 | 모델 |
| --- | --- | 
| 건물/시설 관리 | [RealEstateCore](https://www.realestatecore.io/)<br>[BRICK 스키마](https://brickschema.org/ontology/1.1/)<br>[Ontology (봇) 빌드](https://w3c-lbd-cg.github.io/bot/)<br>[의미 센서 네트워크](https://www.w3.org/TR/vocab-ssn/)<br>[buildingSmart 산업 Foundation 클래스 (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| 스마트 도시 | [ETSI NGSI](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[스마트 응용 프로그램 참조 (SAREF)](https://saref.etsi.org/)<br>[Fiware](https://www.fiware.org/)<br>[Open & Agile 스마트 도시 (OASC)](https://oascities.org/) |
| 에너지 그리드 | [CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968)<br>에너지 및 상품 거래 용 [Adrm 모델](http://www.adrm.com/) | 

사용자의 요구 사항에 따라 DTDL을 사용 하 여 산업 모델을 사용자 지정 하거나 확장 하거나 사용자 지정 모델을 처음부터 개발할 수도 있습니다. 

## <a name="create-and-edit-models"></a>모델 만들기 및 편집

모델링의 첫 번째 단계는 모델을 만드는 것입니다. 사용자는 DTDL로 변환 하기 전에 업계 표준 모델을 만들고 편집할 수 있으며, 초기에 dtdl로 변환 하 고 DTDL 문서로 계속 편집할 수 있습니다.

### <a name="with-industry-standards"></a>업계 표준 사용

대부분의 업계 모델 ( **온톨로지**라고도 함)은 [OWL](https://www.w3.org/OWL/[), [RDF](https://www.w3.org/2001/sw/wiki/RDF)및 [rdfs](https://www.w3.org/2001/sw/wiki/RDFS)와 같은 의미 체계 웹 표준을 기반으로 합니다. 

경우에 따라 OWL 기반 모델 도구를 사용 하 여 모델을 만들거나 편집할 수 있습니다. 다음을 포함 하 여 원하는 수의 모델 제작 도구를 사용 하 여 OWL 기반 모델을 디자인할 수 있습니다.
* [WebProtégé](https://protege.stanford.edu/products.php#web-protege) 및 [Protégé Desktop](https://protege.stanford.edu/products.php#desktop-protege) 은 인기 있는 예제입니다. 여러 형식으로 산업 모델을 가져오고, 모델을 편집 또는 확장 하 고, 모델을 내보낼 수 있습니다. 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) 는 모델을 시각화 하는 데 널리 사용 되는 또 다른 도구입니다. 

DTDL이 아닌 일부 산업 표준을 사용 하 여 모델을 만드는 경우 DTDL로 변환 하 고 Azure Digital Twins에 업로드 해야 합니다. 

#### <a name="common-model-file-formats"></a>일반 모델 파일 형식 

RDF, OWL 및 RDFS는 의미 체계 웹의 기본 구성 요소입니다. 

**RDF** 는 **삼중 쌍**형식으로 사물을 설명 하기 위한 개념적 구조를 제공 합니다. 삼중 쌍는 **subject**, **predicate**및 **object**의 세 부분으로 구성 됩니다. 개체는 Uri로 구성 될 수 있습니다. 

RDF 삼중 쌍의 몇 가지 예는 다음과 같습니다.

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

이러한 예제는 모두 유효한 RDF 하지만 마지막 문은 의미상 잘못 되었습니다. 이 경우 OWL 사양이 그림에 입력 됩니다. **OWL** 는 유효한 ontology를 위해 RDF로 작성할 수 있는 항목을 정의 합니다.

OWL를 사용 하는 예제는 다음과 같습니다. 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**Rdfs** 는 클래스를 정의 하 고 설명 하는 데 도움이 되는 추가 어휘 의미 체계를 제공 합니다. 예를 들어, 이러한 클래스 중 하나는 `rdfs:subClassOf` 다음과 같습니다.

```
<Equipment> <subClassOf> <Asset>
```

다음을 포함 하 여 여러 파일 형식으로 모델을 저장 하 고 가져오고 내보낼 수 있습니다.  
* RDF/XML 
* 거북이 (TTL) 
* OWL/XML 

### <a name="with-dtdl"></a>DTDL 사용

Azure Digital Twins는 모델링에 대해 JSON-LD 기반 **DTDL (디지털 쌍 정의 언어)** 를 사용 합니다. Azure Digital Twins에 사용 되는 모든 모델은 원래에 쓰거나 DTDL로 변환 해야 합니다.

DTDL에서 모델을 사용 하는 경우 dtdl [**DTDL extension**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   모델 작성을 용이 하 게 하는 구문 유효성 검사 및 기타 기능을 제공 하는 [Visual Studio Code](https://code.visualstudio.com/)사용할 수 있는 dtdl 확장을 사용할 수 있습니다.

Azure Digital Twins 모델 및 해당 구성 요소에 대 한 자세한 내용은 [*사용자 지정 모델*](concepts-models.md) 및 [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)를 참조 하세요.

## <a name="convert-models-to-dtdl"></a>모델을 DTDL로 변환

Azure Digital Twins에서 모델을 사용 하려면 DTDL 형식 이어야 합니다. 이 섹션에서는 RDF 기반 모델을 Azure Digital Twins에서 사용할 수 있도록 DTDL로 변환 하는 방법을 설명 합니다.

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

## <a name="validate-and-upload-dtdl-models"></a>DTDL 모델 유효성 검사 및 업로드

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

모델을 변환 하 고 유효성을 검사 한 후에는 **Azure Digital Twins 인스턴스에 업로드할**수 있습니다. 이 프로세스에 대 한 자세한 내용은 *방법: 사용자 지정 모델 관리*의 [*모델 업로드*](how-to-manage-model.md#upload-models) 섹션을 참조 하세요.

## <a name="sample-converter-application"></a>샘플 변환기 응용 프로그램 

Azure Digital Twins 서비스에서 사용 하기 위해 RDF 기반 모델 파일을 [Dtdl (버전 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 로 변환 하는 사용 가능한 샘플 응용 프로그램이 있습니다. 이 샘플은 **Rdftodtdlconverter**라는 .net Core 명령줄 응용 프로그램입니다.

[**Rdftodtdlconverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/)에서 샘플을 다운로드할 수 있습니다. 

컴퓨터에 코드를 다운로드 하려면 샘플 방문 페이지의 제목 아래에 있는 *ZIP 다운로드* 단추를 누릅니다. 그러면 *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*이름으로 *ZIP* 파일이 다운로드 됩니다. 그러면 압축을 풀고 탐색할 수 있습니다.

이 샘플을 사용 하 여 컨텍스트에서 변환 패턴을 확인 하 고 고유한 특정 요구 사항에 따라 모델 변환을 수행 하는 고유한 응용 프로그램의 빌딩 블록으로 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계 

디지털 쌍 모델 디자인 및 관리에 대해 자세히 알아보세요.
 
* [*개념: 사용자 지정 모델*](concepts-models.md)
* [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)
* [*방법: 모델 구문 분석 및 유효성 검사*](how-to-parse-models.md)