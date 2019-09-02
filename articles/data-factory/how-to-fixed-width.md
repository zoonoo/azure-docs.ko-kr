---
title: Azure Data Factory에서 데이터 흐름이 매핑되는 고정 길이 텍스트 파일 처리
description: 데이터 흐름 매핑을 사용 하 여 Azure Data Factory에서 고정 길이 텍스트 파일을 처리 하는 방법에 대해 알아봅니다.
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210677"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>Data Factory 매핑 데이터 흐름을 사용 하 여 고정 길이 텍스트 파일 처리

Data Factory 매핑 데이터 흐름은 고정 너비 텍스트 파일의 변환 데이터를 지원 합니다. 구분 기호 없이 텍스트 파일에 대 한 데이터 집합을 정의한 다음 서 수 위치에 따라 부분 문자열 분할을 설정 합니다.

## <a name="create-a-pipeline"></a>파이프라인을 만듭니다.

1. **+ 새 파이프라인** 으로 이동 하 여 새 파이프라인을 시작 합니다.

2. 고정 너비 파일을 처리 하는 데 사용 되는 데이터 흐름 작업 추가

  ![고정 너비 파이프라인](media/data-flow/fwpipe.png)

3. 데이터 흐름 작업에서 새 매핑 데이터 흐름을 선택 합니다.

4. 원본 변환, 파생 열, 선택 및 싱크 변환 추가

  ![고정 너비 데이터 흐름](media/data-flow/fw2.png)

5. 구분 기호로 분리 된 텍스트 형식의 새 데이터 집합을 사용 하도록 원본 변환 구성

6. 열 구분 기호 없음 및 머리글 없음 설정

이 파일 콘텐츠에 대 한 필드 시작 지점과 길이를 설정 하기만 하면 됩니다.

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

7. 원본 변환의 프로젝션 탭에 "Column_1" 라는 하나의 문자열 열이 표시 됩니다.

8. 이제 파생 열에서 새 열을 만듭니다.

9. 열에 col1과 같은 단순 이름을 지정 합니다.

10. 그런 다음 식 작성기에서 다음을 입력 합니다.

  ```substring(Column_1,1,4)```

  ![파생 열(derived column)](media/data-flow/fwderivedcol1.png)

10. 구문 분석 해야 하는 모든 열에 대해이를 반복 합니다.

12. 검사 탭을 클릭 하 여 생성 될 새 열을 확인 합니다.

  ![검사](media/data-flow/fwinspect.png)

13. 변환 선택은 변환에 필요 하지 않은 열을 제거 하는 데 사용 됩니다.

  ![변환 선택](media/data-flow/fwselect.png)

14. 마지막으로, 싱크를 사용 하 여 데이터를 폴더에 출력 합니다.

  ![고정 너비 싱크](media/data-flow/fwsink.png)

  출력은 다음과 같습니다.

  ![고정 폭 출력](media/data-flow/fxdoutput.png)

  이제 고정 너비 데이터는 각각 4 개의 문자로 분할 되 고 Col1, Col2, Col3, Col4 등에 할당 됩니다. 위의 예제를 기준으로 데이터를 4 개의 열로 분할 합니다.

## <a name="next-steps"></a>다음 단계

* 데이터 흐름 [변환](concepts-data-flow-overview.md) 매핑을 사용 하 여 나머지 데이터 흐름 논리 작성
