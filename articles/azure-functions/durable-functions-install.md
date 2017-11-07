---
title: "지속성 함수 확장 및 샘플 설치 - Azure"
description: "Azure Functions, 포털 개발 또는 Visual Studio 개발을 위한 지속성 함수 확장을 설치하는 방법에 대해 알아봅니다."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: debadde78d937bcd4ec1df665aacfd1887fbcd02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>지속성 함수 확장 및 샘플 설치(Azure Functions)

Azure Functions에 대한 [지속성 함수](durable-functions-overview.md) 확장이 NuGet 패키지 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)에서 제공됩니다. 이 문서에서는 다음 개발 환경을 위한 패키지 및 샘플 집합을 설치하는 방법을 보여 줍니다.

* Visual Studio 2017(권장) 
* Azure portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

현재 Visual Studio는 지속성 함수를 사용하는 앱을 개발하기 위한 최적의 환경을 제공합니다.  사용자 함수를 로컬로 실행할 수 있으며 Azure에도 게시할 수도 있습니다. 빈 프로젝트 또는 샘플 함수 집합으로 시작할 수 있습니다.

### <a name="prerequisites"></a>필수 조건

* [최신 버전의 Visual Studio](https://www.visualstudio.com/downloads/)(버전 15.3 이상)를 설치합니다. 설치 옵션에 Azure 도구를 포함합니다.

### <a name="start-with-sample-functions"></a>샘플 함수로 시작

1. [Visual Studio용 샘플 앱 .zip 파일](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip)을 다운로드합니다. 샘플 프로젝트에 이미 있으므로 NuGet 참조를 추가할 필요는 없습니다.
2. [Azure Storage 에뮬레이터](https://docs.microsoft.com/azure/storage/storage-use-emulator) 버전 5.2 이상을 설치 및 실행합니다. 또는 실제 Azure Storage 연결 문자열로 *local.appsettings.json* 파일을 업데이트할 수 있습니다.
3. Visual Studio 2017에서 프로젝트를 엽니다. 
4. 샘플을 실행하는 방법에 대한 지침은 [함수 체이닝 - Hello 시퀀스 샘플](durable-functions-sequence.md)로 시작하세요. 샘플을 로컬로 실행하거나 Azure에 게시할 수 있습니다.

### <a name="start-with-an-empty-project"></a>빈 프로젝트로 시작
 
샘플을 시작할 때와 같은 지침을 따르지만 *.zip* 파일을 다운로드하는 대신 다음 단계를 수행합니다.

1. 함수 앱 프로젝트를 만듭니다.
2. 다음 NuGet 패키지 참조를 *.csproj* 파일에 추가합니다.

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Azure portal

원하는 경우 지속성 함수 개발을 위해 Azure Portal을 사용할 수 있습니다.

### <a name="create-an-orchestrator-function"></a>오케스트레이터 함수 만들기

1. [functions.azure.com](https://functions.azure.com/signin)에서 새 함수 앱을 만듭니다.
2. [2.0 런타임 버전을 사용](functions-versions.md)하도록 함수 앱을 구성합니다.
3. 새 함수를 만들고 **지속성 함수 오케스트레이터 - C#** 템플릿을 선택합니다.
4. **Extensions not installed(설치되지 않은 확장)** 아래에서 **설치**를 클릭하여 NuGet.org에서 확장을 다운로드합니다.

### <a name="copy-sample-code-to-the-function-app"></a>함수 앱에 샘플 코드 복사

1. [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip) 파일을 다운로드합니다.
2. Kudu 또는 FTP를 사용하여 샘플 파일을 함수 앱의 `D:\home\site\wwwroot`로 압축을 풉니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [함수 체이닝 샘플 실행](durable-functions-sequence.md)
