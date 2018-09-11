---
title: 빠른 시작 - C#을 사용하여 모델을 변경하고 LUIS 앱 학습시키기 - Azure Cognitive Services | Microsoft Docs
description: C# 빠른 시작에서는 Home Automation 앱에 예제 발언을 추가하여 앱을 학습시킵니다. 예제 발언은 의도에 매핑된 대화형 사용자 텍스트입니다. 의도에 대한 예제 발언을 제공하여, 사용자가 제공한 텍스트의 종류가 어떤 의도에 속하는지 LUIS에 알려줍니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: 0c631fe281587c86f26643367aead14683b699df
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160918"
---
# <a name="quickstart-change-model-using-c"></a>빠른 시작: C#을 사용하여 모델 변경

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* 최신 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)
* C# 프로그래밍 언어가 설치되어 있습니다.
* [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) 및 [CommandLine](https://www.nuget.org/packages/CommandLineParser/) NuGet 패키지

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>빠른 시작 코드 만들기 

Visual Studio에서 .NET Framework를 사용하여 새 **Windows 클래식 데스크톱 콘솔** 앱을 만듭니다. 

![Visual Studio 프로젝트 형식](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>System.Web 종속성 추가

Visual Studio 프로젝트에 **System.Web**이 필요합니다. 솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다.

![System.web 참조 추가](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>다른 종속성 추가

Visual Studio 프로젝트에 **JsonFormatterPlus** 및 **CommandLineParser**가 필요합니다. 솔루션 탐색기에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리...** 를 선택합니다. 두 패키지를 각각 찾아서 추가합니다. 

![타사 종속성 추가](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>C# 코드 작성
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

종속성을 추가합니다.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


LUIS ID 및 문자열을 **프로그램** 클래스에 추가합니다.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

**Program** 클래스에 명령줄 매개 변수를 관리하는 클래스를 추가합니다.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

**Program** 클래스에 GET request 메서드를 추가합니다.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


**Program** 클래스에 POST request 메서드를 추가합니다. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

파일 메서드의 예제 발언을 **Program** 클래스에 추가합니다.

   [!code-csharp[Add example utterances from file.
](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.
")]

변경 내용이 모델에 적용되면 모델을 학습시킵니다. **Program** 클래스에 메서드를 추가합니다.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

학습은 즉시 완료되지 않을 수 있으며, 상태를 확인하여 학습이 완료되었는지 확인합니다. **Program** 클래스에 메서드를 추가합니다.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

명령줄 인수를 관리하려면 기본 코드를 추가합니다. **Program** 클래스에 메서드를 추가합니다.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>utterances.json을 출력 디렉터리에 복사

솔루션 탐색기에서 `utterances.json`을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 속성 창에서 `Content`의 **빌드 작업** 및 `Copy Always`의 **출력 디렉터리에 복사**를 표시합니다.  

![JSON 파일을 콘텐츠로 표시](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>코드 빌드

Visual Studio에서 코드를 빌드합니다. 

## <a name="run-code"></a>코드 실행

프로젝트의 /bin/Debug 디렉터리에서 명령줄을 사용하여 응용 프로그램을 실행합니다. 

```CMD
ConsoleApp\bin\Debug> ConsoleApp1.exe --add utterances.json --train --status
```

이 명령줄은 add utterances API를 호출한 결과를 표시합니다. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작을 완료하면 이 빠른 시작에서 생성된 모든 파일을 제거하세요. 

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"] 
> [프로그래밍 방식으로 LUIS 앱 빌드](luis-tutorial-node-import-utterances-csv.md) 