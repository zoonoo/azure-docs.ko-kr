---
title: 핵심 IO 작업 | Microsoft Azure Maps
description: 공간 IO 모듈의 핵심 라이브러리를 사용하여 XML 및 구분된 데이터를 효율적으로 읽고 쓰는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 08966549d2c4d2bf701ee0dbfc5ed24f202396fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891379"
---
# <a name="core-io-operations"></a>핵심 IO 작업

공간 IO 모듈은 공간 데이터 파일을 읽는 도구를 제공하는 것 외에도 핵심 기본 라이브러리를 제공하여 XML 및 구분된 데이터를 빠르고 효율적으로 읽고 씁니다.

`atlas.io.core` 네임스페이스에는 CSV 및 XML 데이터를 빠르게 읽고 쓸 수 있는 하위 수준 클래스가 두 개 있습니다. 이러한 기본 클래스는 공간 IO 모듈에서 공간 데이터 판독기 및 작성기를 구동합니다. 이를 CSV 또는 XML 파일에 대한 읽기와 쓰기 지원을 추가하는 데 자유롭게 사용할 수 있습니다.
 
## <a name="read-delimited-files"></a>구분된 파일 읽기

`atlas.io.core.CsvReader` 클래스는 구분된 데이터 집합을 포함하는 문자열을 읽습니다. 이 클래스는 데이터를 읽는 두 가지 메서드를 제공합니다.

- `read` 함수는 전체 데이터 집합을 읽고 구분된 데이터 집합의 모든 셀을 나타내는 문자열의 2차원 배열을 반환합니다.
- `getNextRow` 함수는 구분된 데이터 집합의 각 텍스트 줄을 읽고 해당 줄의 데이터 집합에 있는 모든 셀을 나타내는 문자열의 배열을 반환합니다. 사용자는 행을 처리하고 다음 행을 처리하기 전에 해당 행에서 불필요한 메모리를 삭제할 수 있습니다. 따라서 함수는 메모리를 더 효율적으로 사용합니다.

기본값으로 판독기는 쉼표 문자를 구분 기호로 사용합니다. 그러나 구분 기호를 단일 문자로 변경하거나 `'auto'`로 설정할 수 있습니다. `'auto'`로 설정된 경우 판독기는 문자열의 첫 번째 텍스트 줄을 분석합니다. 그런 다음 아래 표에서 구분 기호로 사용할 가장 일반적인 문자를 선택합니다.

| 구분 기호 | 문자 |
| :-- | :-- |
| 쉼표 | `,` |
| 탭 | `\t` |
| 파이프 | `|` |

또한 이 판독기는 구분 기호 문자가 포함된 셀을 처리하는 데 사용되는 텍스트 한정자를 지원합니다. 따옴표(`'"'`) 문자는 기본 텍스트 한정자이지만, 이를 임의의 단일 문자로 변경할 수 있습니다.

## <a name="write-delimited-files"></a>구분된 파일 쓰기

`atlas.io.core.CsvWriter`는 개체의 배열을 구분된 문자열로 씁니다. 모든 단일 문자를 구분 기호 또는 텍스트 한정자로 사용할 수 있습니다. 기본 구분 기호는 쉼표(`','`)이고 기본 텍스트 한정자는 따옴표(`'"'`) 문자입니다.

이 클래스를 사용하려면 다음 단계를 따르세요.

- 클래스의 인스턴스를 만들고 필요에 따라 사용자 지정 구분 기호 또는 텍스트 한정자를 설정합니다.
- `write` 함수 또는 `writeRow` 함수를 사용하여 클래스에 데이터를 씁니다. `write` 함수는 여러 행과 셀을 나타내는 개체의 2차원 배열을 전달합니다. `writeRow` 함수를 사용하면 여러 열이 있는 데이터 행을 나타내는 개체 배열을 전달합니다.
- `toString` 함수를 호출하여 구분된 문자열을 검색합니다. 
- 필요에 따라 `clear` 메서드를 호출하여 작성기를 재사용하고 리소스 할당을 축소하거나 `delete` 메서드를 호출하여 기록기 인스턴스를 삭제합니다.

> [!Note]
> 기록되는 열 수는 기록기로 전달된 데이터의 첫 번째 행에 있는 셀 수로 제한됩니다.

## <a name="read-xml-files"></a>XML 파일 읽기

`atlas.io.core.SimpleXmlReader` 클래스는 `DOMParser`보다 XML 파일을 구문 분석하는 데 더 빠릅니다. 그러나 `atlas.io.core.SimpleXmlReader` 클래스는 XML 파일의 형식을 잘 지정해야 합니다. 형식이 잘 지정되지 않은 XML 파일(예: 닫는 태그 누락)로 인해 오류가 발생할 수 있습니다.

다음 코드는 `SimpleXmlReader` 클래스를 사용하여 XML 문자열을 JSON 개체로 구문 분석하고 원하는 형식으로 직렬화하는 방법을 보여줍니다.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>XML 파일 쓰기

`atlas.io.core.SimpleXmlWriter` 클래스는 메모리 효율적인 방식으로 올바른 형식의 XML을 작성합니다.

다음 코드는 `SimpleXmlWriter` 클래스를 사용하여 올바른 형식의 XML 문자열을 생성하는 방법을 설명합니다.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

위의 코드에서 생성된 XML은 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

[CsvReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

[CsvWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

[SimpleXmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

[SimpleXmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

[지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)