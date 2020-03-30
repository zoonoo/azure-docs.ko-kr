---
title: 핵심 IO 운영 | 마이크로소프트 Azure 지도
description: 공간 IO 모듈의 핵심 라이브러리를 사용하여 XML 및 분리된 데이터를 효율적으로 읽고 작성하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371453"
---
# <a name="core-io-operations"></a>핵심 IO 운영

공간 IO 모듈은 공간 데이터 파일을 읽을 수 있는 도구를 제공할 뿐만 아니라 핵심 기본 라이브러리를 노출하여 XML을 읽고 쓰고 데이터를 빠르고 효율적으로 구분합니다.

`atlas.io.core` 네임스페이스에는 CSV 및 XML 데이터를 빠르게 읽고 쓸 수 있는 두 개의 하위 수준 클래스가 포함되어 있습니다. 이러한 기본 클래스는 공간 IO 모듈의 공간 데이터 판독기 및 작성기에 전원을 공급합니다. CSV 또는 XML 파일에 대한 추가 읽기 및 쓰기 지원을 추가하는 데 사용할 수 있습니다.
 
## <a name="read-delimited-files"></a>구분된 파일 읽기

클래스는 `atlas.io.core.CsvReader` 제한된 데이터 집합을 포함하는 문자열을 읽습니다. 이 클래스는 데이터를 읽기 위한 두 가지 방법을 제공합니다.

- 함수는 `read` 전체 데이터 집합을 읽고 구분된 데이터 집합의 모든 셀을 나타내는 2차원 문자열 배열을 반환합니다.
- 함수는 `getNextRow` 제한된 데이터 집합의 각 텍스트 줄을 읽고 해당 데이터 집합의 모든 셀을 나타내는 문자열 배열을 반환합니다. 사용자는 다음 행을 처리하기 전에 행을 처리하고 해당 행에서 불필요한 메모리를 삭제할 수 있습니다. 따라서 기능은 메모리 효율적입니다.

기본적으로 판독기는 쉼표 문자를 구분 기호로 사용합니다. 그러나 구분 기호를 단일 문자로 변경하거나 `'auto'`로 설정할 수 있습니다. 로 설정하면 `'auto'`판독기는 문자열의 첫 번째 텍스트 줄을 분석합니다. 그런 다음 아래 표에서 가장 일반적인 문자를 선택하여 구분 기호로 사용합니다.

| | |
| :-- | :-- |
| 쉼표 | `,` |
| 탭 | `\t` |
| Pipe | `|` |

이 판독기는 구분 기호 문자를 포함하는 셀을 처리하는 데 사용되는 텍스트 한정자도 지원합니다. quote ()`'"'`문자는 기본 텍스트 한정자이지만 모든 단일 문자로 변경할 수 있습니다.

## <a name="write-delimited-files"></a>구분된 파일 쓰기

개체 `atlas.io.core.CsvWriter` 배열을 구분된 문자열로 씁니다. 모든 단일 문자는 구분 기호 또는 텍스트 한정자로 사용할 수 있습니다. 기본 구분 기호는 쉼표`','`() 이며 기본 텍스트`'"'`한정자는 따옴표 () 문자입니다.

이 클래스를 사용하려면 다음 단계를 따르십시오.

- 클래스의 인스턴스를 만들고 선택적으로 사용자 지정 구분 기호 또는 텍스트 한정자를 설정합니다.
- 함수 또는 `write` `writeRow` 함수를 사용하여 클래스에 데이터를 씁니다. 함수의 `write` 경우 여러 행과 셀을 나타내는 2차원 개체 배열을 전달합니다. 이 함수를 `writeRow` 사용하려면 여러 열이 있는 데이터 행을 나타내는 개체 배열을 전달합니다.
- 함수를 `toString` 호출하여 구분된 문자열을 검색합니다. 
- 선택적으로 메서드를 `clear` 호출하여 작성기를 재사용할 수 있도록 하고 리소스 `delete` 할당을 줄이거나 작성기 인스턴스를 삭제하는 메서드를 호출합니다.

> [!Note]
> 기록된 열 수는 기록기에 전달된 데이터의 첫 번째 행의 셀 수로 제한됩니다.

## <a name="read-xml-files"></a>XML 파일 읽기

클래스는 `atlas.io.core.SimpleXmlReader` XML 파일을 구문 분석하는 것이 `DOMParser`보다 빠릅니다. 그러나 클래스에서는 `atlas.io.core.SimpleXmlReader` XML 파일의 포맷을 잘 지정해야 합니다. 형식이 잘 지정되지 않은 XML 파일(예: 닫는 태그 누락)은 오류가 발생할 수 있습니다.

다음 코드에서는 클래스를 `SimpleXmlReader` 사용하여 XML 문자열을 JSON 개체로 구문 분석하고 원하는 형식으로 직렬화하는 방법을 보여 줍니다.

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

클래스는 `atlas.io.core.SimpleXmlWriter` 메모리 효율적인 방법으로 잘 형식이 지정된 XML을 씁니다.

다음 코드는 클래스를 `SimpleXmlWriter` 사용하여 잘 형식이 지정된 XML 문자열을 생성하는 방법을 보여 줍니다.

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

> [!div class="nextstepaction"]
> [Csv리더](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [심플렉스Ml리더](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [심플렉스Ml라이터](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)