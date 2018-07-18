---
title: C#을 사용하여 LUIS 앱에 발언을 추가하는 방법을 알아보는 자습서 | Microsoft Docs
description: 이 자습서에서는 C#을 사용하여 LUIS 앱을 호출하는 방법을 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: v-geberr
ms.openlocfilehash: d9b3ca46cc635d961edadf3e3555f9656b6b5a1d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264246"
---
# <a name="tutorial-add-utterances-to-a-luis-app-using-c"></a>자습서: C#을 사용하여 LUIS 앱에 발언 추가 
이 자습서에서는 C#로 API 작성을 사용하여 의도에 발언을 추가하는 프로그램을 작성합니다.

<!-- green checkmark -->
> [!div class="checklist"]
> * Visual Studio 콘솔 프로젝트 만들기 
> * 발언을 추가하고 앱을 학습하기 위해 LUIS API를 호출하는 메서드 추가
> * BookFlight 의도에 대해 예제 발언을 사용하여 JSON 파일 추가
> * 콘솔을 실행하고 발언에 대한 학습 상태 확인

자세한 정보는 [의도에 예제 발언 추가](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [학습](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) 및 [학습 상태](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) API의 기술 설명서를 참조하세요.

이 아티클에서는 LUIS 앱을 작성하기 위해 체험 [LUIS][LUIS] 계정이 필요합니다.

## <a name="prerequisites"></a>필수 조건

* 최신 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/) 
* LUIS  **[작성 키](luis-concept-keys.md#authoring-key)** [LUIS](luis-reference-regions.md) 웹 사이트의 계정 설정에서 이 키를 찾을 수 있습니다.
* 기존 LUIS [**응용 프로그램 ID**](./luis-get-started-create-app.md) 응용 프로그램 ID는 응용 프로그램 대시보드에 표시됩니다. `utterances.json` 파일에서 사용된 의도 및 엔터티를 포함한 LUIS 응용 프로그램은 이 코드를 실행하기 전에 설치되어야 합니다. 이 아티클의 코드는 의도 및 엔터티를 만들지 않습니다. 기존 의도 및 엔터티에 대한 발언을 추가합니다. 
* 발언을 수신하는 응용 프로그램 내의 **버전 ID**입니다. 기본 ID는 "0.1"입니다.
* VSCode에서 `add-utterances.cs` 프로젝트라는 새 파일을 만듭니다.

> [!NOTE] 
> 예제 `utterances.json` 파일을 비롯한 전체 C# 솔루션은 [**LUIS 샘플** Github 리포지토리](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/csharp/)에서 지원됩니다.

## <a name="create-the-project-in-visual-studio"></a>Visual Studio에서 프로젝트 만들기

Visual Studio에서 .NET Framework를 사용하여 새 **Windows 클래식 데스크톱 콘솔** 앱을 만듭니다. 

![Visual Studio 프로젝트 형식](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

## <a name="add-the-systemweb-dependency"></a>System.Web 종속성 추가

Visual Studio 프로젝트에서 프로젝트에는 **System.Web**이 필요합니다. 솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다.

![System.web 참조 추가](./media/luis-quickstart-cs-add-utterance/system.web.png)

## <a name="write-the-c-code"></a>C# 코드 작성
**Program.cs** 파일은 다음과 같아야 합니다.

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

System.IO 및 System.Net.Http 종속성을 추가합니다.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=1-6 "Add the dependencies")]


LUIS ID 및 문자열을 **프로그램** 클래스에 추가합니다.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=12-30&dedent=8 "Add the LUIS IDs and strings")]

JsonPrettyPrint 메서드를 추가합니다.

   [!code-csharp[Add the JsonPrettyPrint method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=32-92 "Add the JsonPrettyPrint method")]

발언을 만들거나 학습을 시작하는 데 사용되는 GET 요청을 추가합니다. 

   [!code-csharp[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=93-103 "SendGet")]


발언을 만들거나 교육을 시작하는 데 사용되는 POST 요청을 추가합니다. 

   [!code-csharp[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=104-115 "SendPost")]

`AddUtterances` 함수를 추가합니다.

   [!code-csharp[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=116-125 "AddUtterances method")]


`Train` 함수를 추가합니다. 

   [!code-csharp[Train](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=126-136 "Train")]

`Status` 함수를 추가합니다.

   [!code-csharp[Status](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=137-143 "Status")]

인수를 관리하려면 기본 코드를 추가합니다.

   [!code-csharp[Main code](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=144-172 "Main code")]

## <a name="specify-utterances-to-add"></a>추가할 발언 지정
`utterances.json` 파일을 만들고 편집하여 LUIS 앱에 추가하려는 **발언의 배열**을 지정합니다. 의도 및 엔터티는 이미 LUIS 앱에 포함되어 **있어야** 합니다.

> [!NOTE]
> `utterances.json` 파일에서 사용된 의도 및 엔터티를 포함한 LUIS 응용 프로그램은 `add-utterances.cs`에서 코드를 실행하기 전에 설치되어야 합니다. 이 아티클의 코드는 의도 및 엔터티를 만들지 않습니다. 기존 의도 및 엔터티에 대한 발언만을 추가합니다.

`text` 필드에는 발언의 텍스트가 포함됩니다. `intentName` 필드는 LUIS 앱에 있는 의도의 이름에 해당해야 합니다. `entityLabels` 필드는 필수입니다. 엔터티의 레이블을 지정하지 않으려면 다음 예제에서 표시된 대로 빈 목록을 제공합니다.

entityLabels 목록이 비어 있는 경우는 `startCharIndex` 및 `endCharIndex`는 `entityName` 필드에서 참조되는 엔터티를 표시해야 합니다. 두 인덱스는 모두 0부터 시작하는 계수입니다. 즉 위의 예제에서 6은 시애틀(Seattle)의 "S"를 의미하고 대문자 S 앞에 있는 공백이 아닙니다.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="mark-the-json-file-as-content"></a>JSON 파일을 콘텐츠로 표시
솔루션 탐색기에서 `utterances.json`을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 속성 창에서 `Content`의 **빌드 작업** 및 `Copy Always`의 **출력 디렉터리에 복사**를 표시합니다.  

![JSON 파일을 콘텐츠로 표시](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="add-an-utterance-from-the-command-line"></a>명령줄의 발언 추가

/bin/Debug 디렉터리에서 C#을 사용하여 명령줄에서 응용 프로그램을 빌드하고 실행합니다. utterances.json 파일이 이 디렉터리에도 있는지 확인합니다.

utterance.json을 인수로 사용하여 add-utterances.cs를 호출하면 새 발언에서 LUIS를 추가하지만 학습하지는 않습니다.
````
ConsoleApp\bin\Debug> ConsoleApp1.exe utterances.json
````

이 명령줄은 add utterances API를 호출한 결과를 표시합니다. `response` 필드는 추가된 발언에서 이 형식으로 표시됩니다. `hasError`는 false이며 발언이 추가되었음을 나타냅니다.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>발언 추가 및 명령줄에서 학습
`-train` 인수를 사용하여 add-utterance를 호출하면 학습할 요청을 보냅니다. 

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -train utterances.json
````

> [!NOTE]
> 중복 발언은 다시 추가할 수 없지만 오류가 발생하지 않습니다. `response`에는 원래 발언의 ID가 포함됩니다.

학습 요청이 성공한 결과는 다음 JSON과 같습니다.

```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

학습에 대한 요청이 큐에 대기된 후에 학습을 완료하려면 시간이 걸릴 수 있습니다.

## <a name="get-training-status-from-the-command-line"></a>명령줄의 학습 상태 가져오기
`-status` 인수를 사용하여 앱을 호출하면 학습 상태를 확인하고 상태 세부 정보를 표시합니다.

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>리소스 정리
이 자습서를 완료하고 Visual Studio 및 콘솔 응용 프로그램이 더 이상 필요없는 경우 제거합니다. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [프로그래밍 방식으로 LUIS 앱 빌드](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
