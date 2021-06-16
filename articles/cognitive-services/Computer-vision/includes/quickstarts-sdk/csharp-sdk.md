---
title: '빠른 시작: .NET용 광학 인식 클라이언트 라이브러리'
description: 이 빠른 시작에서는 .NET용 광학 인식 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0eb795040e999f40ab05c2b6c20e0426327b76
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112083589"
---
<a name="HOLTop"></a>

OCR 클라이언트 라이브러리를 사용하여 이미지에서 인쇄 및 필기 텍스트를 읽습니다.

[참조 설명서](/dotnet/api/overview/azure/cognitiveservices/client/computervision) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [샘플](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 또는 현재 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio를 사용하여 새 .NET Core 애플리케이션을 만듭니다. 

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치 

새 프로젝트를 만든 후 **솔루션 탐색기** 에서 프로젝트 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택하여 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기** 를 선택하고, **시험판 포함** 을 선택하고, `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`를 검색합니다. `7.0.0` 버전, **설치** 를 차례로 선택합니다. 

#### <a name="cli"></a>[CLI](#tab/cli)

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `computer-vision-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs* 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -n computer-vision-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Computer Vision 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 *Program.cs* 파일을 엽니다.

### <a name="find-the-subscription-key-and-endpoint"></a>구독 키 및 엔드포인트 찾기

Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Computer Vision 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 구독 키와 엔드포인트를 찾을 수 있습니다. 

애플리케이션의 **Program** 클래스에서 Computer Vision 구독 키 및 엔드포인트에 대한 변수를 만듭니다. 구독 키와 엔드포인트를 표시된 다음 코드에 붙여넣습니다. Computer Vision 엔트포인트의 형식은 `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/`입니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> 완료되면 코드에서 구독 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../../key-vault/general/overview.md)입니다.

애플리케이션의 `Main` 메서드에서 이 빠른 시작에 사용된 메서드에 대한 호출을 추가합니다. 나중에 만들 것입니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_main_calls)]

> [!div class="nextstepaction"]
> [클라이언트를 설정했습니다.](?success=set-up-client#object-model) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client&product=computer-vision&page=csharp-sdk)

## <a name="object-model"></a>개체 모델

OCR .NET SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | 이 클래스는 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 이 클래스를 인스턴스화한 다음, 대부분의 이미지 작업에 사용합니다.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| 이 클래스는 **ComputerVisionClient** 에 대한 추가 메서드를 포함하고 있습니다.|

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 .NET용 OCR 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [인쇄 텍스트 및 필기 텍스트 읽기](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>클라이언트 인증

**Program** 클래스의 새 메서드에서 엔드포인트 및 구독 키를 사용하여 클라이언트를 인스턴스화합니다. 구독 키를 사용하여 **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** 개체를 만들고, 엔드포인트에서 이를 사용하여 **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** 개체를 만듭니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [클라이언트를 인증했습니다.](?success=authenticate-client#read-printed-and-handwritten-text) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client&product=computer-vision&page=csharp-sdk)

## <a name="read-printed-and-handwritten-text"></a>인쇄 텍스트 및 필기 텍스트 읽기

OCR 서비스는 이미지 속의 시각적 텍스트를 읽고 문자 스트림으로 변환할 수 있습니다. 텍스트 인식에 대한 자세한 내용은 [OCR(광학 인식) 개요](../../overview-ocr.md)를 참조하세요. 이 섹션의 코드는 최신 [Read 3.0용 Computer Vision SDK 릴리스](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)를 사용하고, 클라이언트 개체를 사용하여 이미지의 텍스트를 감지하고 추출하는 `BatchReadFileUrl` 메서드를 정의합니다.

> [!TIP]
> 로컬 이미지에서 텍스트를 추출할 수도 있습니다. [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) 메서드(예: **ReadInStreamAsync**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs#162)의 샘플 코드를 참조하세요.

### <a name="set-up-test-image"></a>테스트 이미지 설정

**Program** 클래스에서 텍스트를 추출하려는 이미지의 URL에 대한 참조를 저장합니다. 이 코드 조각에는 인쇄된 텍스트 및 필기 텍스트 모두에 대한 샘플 이미지가 포함되어 있습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>읽기 API 호출

텍스트를 읽기 위한 새 메서드를 정의합니다. 지정된 이미지에 대해 **ReadAsync** 메서드를 호출하는 아래 코드를 추가합니다. 그러면 작업 ID가 반환되고 이미지의 콘텐츠를 읽는 비동기 프로세스가 시작됩니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>읽기 결과 가져오기

그런 다음, **ReadAsync** 호출에서 반환된 작업 ID를 가져와서 서비스의 작업 결과를 쿼리합니다. 다음 코드는 결과가 반환될 때까지 작업을 검사합니다. 그런 다음, 추출된 텍스트 데이터를 콘솔에 출력합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>읽기 결과 표시

검색된 텍스트 데이터를 구문 분석하고, 표시하고, 메서드 정의를 완료하는 다음 코드를 추가합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>애플리케이션 실행

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE 창의 위쪽에서 **디버그** 단추를 클릭하여 애플리케이션을 실행합니다.

#### <a name="cli"></a>[CLI](#tab/cli)

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 OCR 클라이언트 라이브러리를 설치하고 Read API를 사용하는 방법을 알아보았습니다. 다음으로, Read API 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
>[읽기 API 호출](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR 개요](../../overview-ocr.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)에서 확인할 수 있습니다.
