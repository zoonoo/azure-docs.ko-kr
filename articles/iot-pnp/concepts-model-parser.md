---
title: Digital Twins 모델 파서 이해 | Microsoft Docs
description: 개발자는 DTDL 파서를 사용 하 여 모델의 유효성을 검사 하는 방법을 알아봅니다.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d68abe8548dac3306228683e4b6ce8935a248ebc
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331790"
---
# <a name="understand-the-digital-twins-model-parser"></a>디지털 트윈 모델 파서 이해

Dtdl (디지털 Twins 정의 언어)는 [Dtdl 사양](https://github.com/Azure/opendigitaltwins-dtdl)에 설명 되어 있습니다. 사용자는 _Digital Twins 모델 파서_ NuGet 패키지를 사용 하 여 여러 파일에 정의 된 모델의 유효성을 검사 하 고 쿼리할 수 있습니다.

## <a name="install-the-dtdl-model-parser"></a>DTDL 모델 파서 설치

파서는 ID가 [DigitalTwins](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)인 NuGet.org에서 사용할 수 있습니다. 파서를 설치 하려면 Visual Studio 또는 CLI와 같은 호환 되는 NuGet 패키지 관리자를 사용 합니다 `dotnet` .

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> 작성 시 파서 버전은 `3.12.5` 입니다.

## <a name="use-the-parser-to-validate-a-model"></a>파서를 사용 하 여 모델 유효성 검사

모델은 JSON 파일에 설명 된 하나 이상의 인터페이스로 구성 될 수 있습니다. 파서를 사용 하 여 지정 된 폴더의 모든 파일을 로드 하 고 파서를 사용 하 여 파일 간의 모든 참조를 포함 하 여 전체 파일의 유효성을 검사할 수 있습니다.

1. `IEnumerable<string>`모든 모델 콘텐츠 목록을 포함 하는을 만듭니다.

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. 을 인스턴스화하고를 `ModelParser` 호출 합니다 `ParseAsync` .

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. 유효성 검사 오류를 확인합니다. 파서가 오류를 발견 하면 `ParsingException` 오류 목록과 함께이 throw 됩니다.

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. 를 검사 `Model` 합니다. 유효성 검사가 성공 하면 모델 파서 API를 사용 하 여 모델을 검사할 수 있습니다. 다음 코드 조각에서는 구문 분석 된 모든 모델을 반복 하 고 기존 속성을 표시 하는 방법을 보여 줍니다.

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>다음 단계

이 문서에서 검토 한 모델 파서 API를 사용 하면 여러 시나리오에서 DTDL 모델에 종속 된 작업을 자동화 하거나 유효성을 검사할 수 있습니다. 예를 들어 모델의 정보에서 UI를 동적으로 만들 수 있습니다.
