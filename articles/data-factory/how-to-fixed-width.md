---
title: Azure 데이터 팩터리에서 데이터 흐름을 매핑하여 고정 길이 텍스트 파일 처리
description: 매핑 데이터 흐름을 사용하여 Azure Data Factory에서 고정 길이 텍스트 파일을 처리하는 방법을 알아봅니다.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d6885e9b30cc71bda822a29574c4d574f2b020a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72387043"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>데이터 팩터리 매핑 데이터 흐름을 사용하여 고정 길이 텍스트 파일 처리

Microsoft Azure 데이터 팩터리에서 매핑 데이터 흐름을 사용하여 고정 너비 텍스트 파일에서 데이터를 변환할 수 있습니다. 다음 작업에서는 구분 기호없이 텍스트 파일에 대한 데이터 집합을 정의한 다음 서수 위치에 따라 하위 문자열 분할을 설정합니다.

## <a name="create-a-pipeline"></a>파이프라인 만들기

1. 새 파이프라인을 만들려면 **+새 파이프라인을** 선택합니다.

2. 고정 너비 파일을 처리하는 데 사용되는 데이터 흐름 활동을 추가합니다.

    ![고정 너비 파이프라인](media/data-flow/fwpipe.png)

3. 데이터 흐름 활동에서 **새 매핑 데이터 흐름을**선택합니다.

4. 소스, 파생 열, 선택 및 싱크 변환 추가:

    ![고정 너비 데이터 흐름](media/data-flow/fw2.png)

5. 분산된 텍스트 유형이 될 새 데이터 집합을 사용하도록 소스 변환을 구성합니다.

6. 열 구분 기호 또는 헤더를 설정하지 마십시오.

   이제 이 파일의 내용에 대한 필드 시작 점과 길이를 설정합니다.

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. 소스 변환의 **프로젝션** 탭에는 *Column_1*라는 문자열 열이 표시됩니다.

8. 파생 열에서 새 열을 만듭니다.

9. 우리는 *col1와*같은 열에 간단한 이름을 줄 것이다.

10. 식 빌더에서 다음을 입력합니다.

    ```substring(Column_1,1,4)```

    ![파생 열(derived column)](media/data-flow/fwderivedcol1.png)

11. 구문 분석해야 하는 모든 열에 대해 10단계를 반복합니다.

12. **검사** 탭을 선택하여 생성될 새 열을 확인합니다.

    ![검사](media/data-flow/fwinspect.png)

13. 변형 선택을 사용하여 변환에 필요하지 않은 열을 제거합니다.

    ![변환 선택](media/data-flow/fwselect.png)

14. 싱크를 사용하여 데이터를 폴더로 출력합니다.

    ![고정 너비 싱크](media/data-flow/fwsink.png)

    출력의 모양은 다음과 같습니다.

    ![고정 너비 출력](media/data-flow/fxdoutput.png)

  고정 너비 데이터는 이제 각각 4개의 문자로 분할되고 Col1, Col2, Col3, Col4 등으로 할당됩니다. 위의 예제에 따라 데이터는 4개의 열로 분할됩니다.

## <a name="next-steps"></a>다음 단계

* 매핑 데이터 흐름 변환을 사용하여 나머지 데이터 흐름 [논리를 빌드합니다.](concepts-data-flow-overview.md)
