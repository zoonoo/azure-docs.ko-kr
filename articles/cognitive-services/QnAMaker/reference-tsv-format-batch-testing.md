---
title: 일괄 처리 테스트 TSV 형식 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 일괄 처리 테스트를 위한 TSV 형식 이해
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132129"
---
# <a name="batch-testing-tsv-format"></a>일괄 처리 테스트 TSV 형식

일괄 처리 테스트는 [소스 코드](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)에서 사용하거나 [다운로드 가능한 압축된 실행 파일](https://aka.ms/qna_btzip)로 사용할 수 있습니다. 일괄 처리 테스트를 실행하는 명령 형식은 다음과 같습니다.

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|매개 변수|예상 값|
|--|--|
|1|[TSV 입력 필드](#tsv-input-fields)로 형식이 지정된 tsv 파일의 이름|
|2|QnA Maker 포털의 게시 페이지에서 YOUR-HOST를 사용하는 엔드포인트의 URI|
|3|QnA Maker 포털의 게시 페이지에 있는 ENDPOINT-KEY|
|4|결과에 대한 일괄 테스트를 통해 만들어진 TSV 파일의 이름.|

일괄 처리 테스트를 위한 TSV 형식을 이해하고 구현하려면 다음 정보를 사용합니다. 

## <a name="tsv-input-fields"></a>TSV 입력 필드

|TSV 입력 파일 필드|메모|
|--|--|
|KBID|게시 페이지에 있는 기술 자료 ID|
|질문|사용자가 입력할 질문|
|메타데이터 태그|선택 사항|
|top(상위 항목) 매개 변수|선택 사항| 
|예상 대답 ID|선택 사항|

![일괄 처리 테스트를 위한 TSV 파일의 입력 형식](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 출력 필드 

|TSV 출력 파일 매개 변수|참고|
|--|--|
|KBID|게시 페이지에 있는 기술 자료 ID|
|질문|입력 파일에서 입력한 질문|
|답변|기술 자료에 있는 최상의 응답|
|응답 ID|응답 ID|
|점수|응답에 대한 예측 점수 |
|메타데이터 태그|반환된 응답과 연결됨|
|예상 대답 ID|선택 사항(예상 응답 ID가 지정된 경우에만)|
|판단 레이블|선택 사항, 값은 맞거나 틀릴 수 있음(예상 응답이 제공된 경우에만).|
