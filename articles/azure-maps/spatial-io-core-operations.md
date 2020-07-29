---
title: 코어 IO 작업 | Microsoft Azure 맵
description: 공간 IO 모듈에서 핵심 라이브러리를 사용 하 여 XML 및 구분 된 데이터를 효율적으로 읽고 쓰는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: 021680a81cc5f97bb92c3a9e14036e1a88dae278
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87279511"
---
# <a name="core-io-operations"></a>코어 IO 작업

공간 IO 모듈은 공간 데이터 파일을 읽는 도구를 제공 하는 것 외에도 핵심 기본 라이브러리를 제공 하 여 XML 및 구분 된 데이터를 빠르고 효율적으로 읽고 씁니다.

`atlas.io.core`네임 스페이스에는 CSV 및 XML 데이터를 빠르게 읽고 쓸 수 있는 하위 수준 클래스가 두 개 있습니다. 이러한 기본 클래스는 공간 IO 모듈에서 공간 데이터 판독기 및 작성기를 구동 합니다. CSV 또는 XML 파일에 대 한 읽기 및 쓰기 지원을 추가 하는 데 자유롭게 사용할 수 있습니다.
 
## <a name="read-delimited-files"></a>구분 된 파일 읽기

`atlas.io.core.CsvReader`클래스는 구분 기호로 분리 된 데이터 집합을 포함 하는 문자열을 읽습니다. 이 클래스는 데이터를 읽는 두 가지 메서드를 제공 합니다.

- `read`이 함수는 전체 데이터 집합을 읽고 구분 된 데이터 집합의 모든 셀을 나타내는 문자열의 2 차원 배열을 반환 합니다.
- `getNextRow`함수는 구분 기호로 분리 된 데이터 집합의 각 텍스트 줄을 읽고 해당 줄의 데이터 집합에 있는 모든 셀을 나타내는 문자열의 배열을 반환 합니다. 사용자는 다음 행을 처리 하기 전에 행을 처리 하 고 해당 행에서 불필요 한 메모리를 삭제할 수 있습니다. 따라서 함수는 더 많은 메모리를 효율적으로 처리할 수 있습니다.

기본적으로 판독기는 쉼표 문자를 구분 기호로 사용 합니다. 그러나 구분 기호를 단일 문자로 변경 하거나로 설정할 수 있습니다 `'auto'` . 로 설정 `'auto'` 된 경우 판독기는 문자열의 첫 번째 텍스트 줄을 분석 합니다. 그런 다음 아래 표에서 구분 기호로 사용할 가장 일반적인 문자를 선택 합니다.

| 구분 기호 | 문자 |
| :-- | :-- |
| 쉼표 | `,` |
| 탭 | `\t` |
| 파이프 | `|` |

또한이 판독기는 구분 기호 문자가 포함 된 셀을 처리 하는 데 사용 되는 텍스트 한정자를 지원 합니다. 따옴표 ( `'"'` ) 문자는 기본 텍스트 한정자 이지만 임의의 단일 문자로 변경할 수 있습니다.

## <a name="write-delimited-files"></a>구분 된 파일 쓰기

는 `atlas.io.core.CsvWriter` 개체의 배열을 구분 된 문자열로 씁니다. 단일 문자를 구분 기호 또는 텍스트 한정자로 사용할 수 있습니다. 기본 구분 기호는 쉼표 ( `','` )이 고 기본 텍스트 한정자는 따옴표 ( `'"'` ) 문자입니다.

이 클래스를 사용 하려면 다음 단계를 수행 합니다.

- 클래스의 인스턴스를 만들고 필요에 따라 사용자 지정 구분 기호 또는 텍스트 한정자를 설정 합니다.
- 함수 또는 함수를 사용 하 여 클래스에 데이터를 씁니다 `write` `writeRow` . 함수의 경우 `write` 여러 행과 셀을 나타내는 개체의 2 차원 배열을 전달 합니다. 함수를 사용 하려면 `writeRow` 여러 열이 포함 된 데이터 행을 나타내는 개체의 배열을 전달 합니다.
- 함수를 호출 `toString` 하 여 구분 된 문자열을 검색 합니다. 
- 필요에 따라 메서드를 호출 `clear` 하 여 작성기를 다시 사용할 수 있도록 하 고 해당 리소스 할당을 줄이거나 메서드를 호출 하 여 `delete` 기록기 인스턴스를 삭제 합니다.

> [!Note]
> 쓴 열의 수는 기록기에 전달 된 데이터의 첫 번째 행에 있는 셀 수로 제한 됩니다.

## <a name="read-xml-files"></a>XML 파일 읽기

`atlas.io.core.SimpleXmlReader`클래스는 보다 XML 파일을 구문 분석 하는 속도가 더 빠릅니다 `DOMParser` . 그러나 클래스를 `atlas.io.core.SimpleXmlReader` 사용 하려면 XML 파일의 형식을 올바르게 지정 해야 합니다. 잘 포맷 되지 않은 XML 파일 (예: 닫는 태그가 누락 됨)은 오류가 발생할 수 있습니다.

다음 코드에서는 클래스를 사용 하 여 `SimpleXmlReader` XML 문자열을 JSON 개체로 구문 분석 하 고 원하는 형식으로 serialize 하는 방법을 보여 줍니다.

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

`atlas.io.core.SimpleXmlWriter`클래스는 올바른 형식의 XML을 메모리 효율적인 방식으로 작성 합니다.

다음 코드에서는 클래스를 사용 하 여 `SimpleXmlWriter` 올바른 형식의 XML 문자열을 생성 하는 방법을 보여 줍니다.

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

위의 코드에서 생성 된 XML은 다음과 같습니다.

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
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)