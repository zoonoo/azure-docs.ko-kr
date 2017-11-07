---
title: "Azure에서 읽기 액세스 중복 저장소 액세스 오류 시뮬레이션 | Microsoft Docs"
description: "읽기 액세스 지역 중복 저장소 액세스 오류 시뮬레이션"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 2919eb0e301000b53f4f63799e9c65aad45ca9f2
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>읽기 액세스 중복 저장소 액세스 오류 시뮬레이션

이 자습서는 시리즈의 2부입니다. 이 자습서에는 [RA-GRS](../common/storage-redundancy.md#read-access-geo-redundant-storage)(읽기 액세스 지역 중복 저장소) 계정에 대한 요청에 대해 Fiddler의 실패 응답을 삽입하여 오류를 시뮬레이션하고 보조 끝점에서 응용 프로그램을 읽게 합니다.

![시나리오 앱](media/storage-simulate-failure-ragrs-account-app/scenario.png)

시리즈 2부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 응용 프로그램 실행 및 일시 중지
> * 오류 시뮬레이션
> * 기본 끝점 복원 시뮬레이션

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Fiddler](https://www.telerik.com/download/fiddler) 다운로드 및 설치

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

이 자습서를 완료하려면 이전 저장소 자습서([Azure Storage를 통해 응용 프로그램 데이터의 고가용성 지원][previous-tutorial])를 완료해야 합니다.

## <a name="launch-fiddler"></a>Fiddler 시작

Fiddler를 열고 **규칙**과 **규칙 사용자 지정**을 선택합니다.

![Fiddler 규칙 사용자 지정](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor가 시작되어 **SampleRules.js** 파일을 표시합니다. 이 파일을 사용하여 Fiddler를 사용자 지정합니다. 다음 코드 샘플을 `OnBeforeResponse` 함수에 붙여 넣습니다. 새 코드는 생성 논리가 즉시 구현되지 않도록 주석 처리가 제거됩니다. 완료되면 **파일**과 **저장**을 선택하여 변경 내을 저장합니다.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![사용자 지정된 규칙 붙여넣기](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>응용 프로그램 시작 및 일시 중지

Visual Studio에서 **F5** 키를 누르거나 **시작**을 클릭하여 응용 프로그램 디버깅을 시작합니다. 응용 프로그램이 기본 끝점에서 읽기를 시작하면 콘솔 창에서 **아무 키**나 눌러 응용 프로그램을 일시 중지합니다.

## <a name="simulate-failure"></a>오류 시뮬레이션

응용 프로그램이 일시 중지된 상태에서, 이제 이전 단계에서 Fiddler에 저장한 사용자 지정 규칙을 주석 처리 제거할 수 있습니다. 이 코드 샘플은 RA-GRS 저장소 계정에 대한 요청을 찾으며 경로에 해당 이미지의 이름(`HelloWorld`)이 포함되어 있는 경우 `503 - Service Unavailable`의 응답 코드를 반환합니다.

Fiddler로 이동하여 **규칙** -> **규칙 사용자 지정...**을 선택합니다.  다음 라인의 주석 처리를 제거하고 `STORAGEACCOUNTNAME`을(를) 저장소 계정 이름으로 대체합니다. **파일** -> **저장**을 선택하여 변경 내용을 저장합니다.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

응용 프로그램을 다시 시작하려면 **아무 키**나 누릅니다.

응용 프로그램이 다시 실행되기 시작하면 기본 끝점에 대한 요청이 실패하기 시작합니다. 응용 프로그램이 기본 끝점으로 다시 연결을 5회 시도합니다. 5회의 실패 임계값 후에는 보조 읽기 전용 끝점에서 이미지를 요청합니다. 응용 프로그램이 보조 끝점에서 이미지를 20회 성공적으로 검색하면 응용 프로그램이 기본 끝점에 연결을 시도합니다. 그래도 기본 끝점에 연결할 수 없는 경우 응용 프로그램은 보조 끝점에서 읽기를 다시 시작합니다. 이 패턴은 이전 자습서에서 설명한 [회로 차단기](/azure/architecture/patterns/circuit-breaker.md) 패턴입니다.

![사용자 지정된 규칙 붙여넣기](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>기본 끝점 복원 시뮬레이션

이전 단계에서의 Fiddler 사용자 지정 규칙 집합으로, 기본 끝점에 대한 요청이 실패합니다. 기본 끝점의 작동을 다시 시뮬레이션하려면 `503` 오류를 생성하는 논리를 삭제합니다.

응용 프로그램을 일시 중지하려면 **아무 키**나 누릅니다.

### <a name="remove-the-custom-rule"></a>사용자 지정 규칙 제거

Fiddler로 이동하여 **규칙**, **규칙 사용자 지정...**을 선택합니다.  `OnBeforeResponse` 함수에서 사용자 지정 논리를 주석 처리하거나 제거하여 기본 함수를 유지합니다. **파일**, **저장**을 선택하여 변경 내용을 저장합니다.

![사용자 지정된 규칙 제거](media/storage-simulate-failure-ragrs-account-app/figure5.png)

완료되면 **아무 키**나 눌러 응용 프로그램을 다시 시작합니다. 응용 프로그램은 999 읽기에 도달할 때까지 기본 끝점에서 읽기를 계속합니다.

![응용 프로그램 다시 시작](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>다음 단계

시리즈 2부에서는 읽기 액세스 지역 중복 저장소 오류를 시뮬레이션하는 방법에 대해 배웠습니다.

> [!div class="checklist"]
> * 응용 프로그램 실행 및 일시 중지
> * 오류 시뮬레이션
> * 기본 끝점 복원 시뮬레이션

미리 작성된 저장소 샘플을 보려면 이 링크를 따라 이동합니다.

> [!div class="nextstepaction"]
> [Azure Storage 스크립트 샘플](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md