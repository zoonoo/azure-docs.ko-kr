---
title: 배치 테스트 TSV 형식 - QnA 메이커
titleSuffix: Azure Cognitive Services
description: 일괄 처리 테스트를 위한 TSV 형식 이해
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73501033"
---
# <a name="batch-testing-tsv-format"></a>일괄 테스트 TSV 형식

일괄 처리 테스트는 [소스 코드또는](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) [다운로드 가능한 실행 가능한 압축으로](https://aka.ms/qna_btzip)사용할 수 있습니다. 일괄 처리 테스트를 실행하는 명령의 형식은 다음과 입니다.

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|매개 변수|예상 값|
|--|--|
|1|TSV 입력 필드로 서식이 지정된 [tsv](#tsv-input-fields) 파일의 이름|
|2|QnA Maker 포털의 게시 페이지에서 HOST를 사용할 수 있는 끝점에 대한 URI입니다.|
|3|QnA 메이커 포털의 게시 페이지에서 찾을 수 있는 끝점 키.|
|4|결과에 대한 일괄 처리 테스트에 의해 생성된 tsv 파일의 이름입니다.|

다음 정보를 사용하여 일괄 처리 테스트를 위해 TSV 형식을 이해하고 구현합니다. 

## <a name="tsv-input-fields"></a>TSV 입력 필드

|TSV 입력 파일 필드|메모|
|--|--|
|KBID|게시 페이지에서 KB ID를 찾았습니다.|
|질문|사용자가 입력할 질문입니다.|
|메타데이터 태그|선택 사항|
|top(상위 항목) 매개 변수|선택 사항| 
|예상 대답 ID|선택 사항|

![일괄 처리를 위한 TSV 파일의 입력 형식입니다.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 출력 필드 

|TSV 출력 파일 매개 변수|메모|
|--|--|
|KBID|게시 페이지에서 KB ID를 찾았습니다.|
|질문|입력 파일에서 입력한 질문입니다.|
|Answer|기술 자료의 최고 답변.|
|답변 ID|답변 ID|
|점수|답변에 대한 예측 점수입니다. |
|메타데이터 태그|반환된 답변과 연결됨|
|예상 대답 ID|선택 사항(예상 답변 ID가 부여된 경우에만)|
|심판 라벨|선택적 값일 수 있습니다: 정답 또는 올바르지 않을 수 있습니다(예상된 답변이 주어진 경우에만)|
