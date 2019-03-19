---
title: Azure 앱 구성을 사용 하 여 연속 통합 및 배달 파이프라인을 사용 하 여 통합 | Microsoft Docs
description: Azure 앱 구성에서 데이터를 사용 하 여 지속적인 통합 및 업데이트 하는 동안 구성 파일을 생성 하는 방법 알아보기
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957371"
---
# <a name="integrate-with-a-cicd-pipeline"></a>CI/CD 파이프라인과 통합

해당 시작 하는 동안 로컬로 로드 및 응용 프로그램과 함께 배포 되는 파일에 현재 구성 데이터를 패키지 해야 하는 Azure 앱 구성에 연결할 수 없는 원격 가능성에 대해 응용 프로그램의 복원 력의 강화 하려면 . 이 방법을 사용 하면 응용 프로그램 기본 설정 값 이상을 가지게 됩니다. 이러한 값 사용 가능한 경우 앱 구성 저장소의 최신 변경 내용 덮어쓰게 됩니다.

사용 하는 [ **내보내기** ](./howto-import-export-data.md#export-data) 함수 앱 구성을 Azure의 단일 파일로 현재 구성 데이터를 검색 하는 프로세스를 자동화할 수 있습니다. 그런 다음 연속 통합 및 지속적인 배포 파이프라인에서 빌드 또는 배포 단계에서이 파일을 포함할 수 있습니다.

다음 예제에서는 앱 구성을 포함 하는 방법을 보여 줍니다 빌드 데이터를 빠른 시작에 도입 된 웹 앱에 대 한 단계입니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 ASP.NET Core 앱 만들기](./quickstart-aspnet-core-app.md)를 완료하세요.

이 빠른 시작의 단계를 완료하려면 아무 코드 편집기나 사용할 수 있습니다. 그러나 [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 뛰어난 옵션입니다.

## <a name="prerequisites"></a>필수 조건

로컬로 빌드하는 경우 다운로드 및 설치 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 아직 없는 경우.

클라우드 빌드를 수행 하려는 경우 Azure DevOps를 사용 하 여 예를 들어 했는지 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 빌드 시스템에 설치 됩니다.

## <a name="export-app-configuration-store"></a>구성 저장소 응용 프로그램 내보내기

1. 열기 하 *.csproj* 파일과 다음 코드를 추가 합니다.

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    합니다 *ConnectionString* 연결 된 앱 구성을 사용 하 여 환경 변수로 저장소를 추가 합니다.

2. 열기 *Program.cs* 업데이트 및 합니다 `CreateWebHostBuilder` 메서드를 호출 하 여 내보낸된 json 파일을 사용 하는 `config.AddJsonFile()` 메서드.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **ConnectionString**이라는 환경 변수를 설정하고 앱 구성 저장소에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고 명령 프롬프트를 다시 시작하여 변경 사항을 적용합니다.

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. .NET Core CLI를 사용하여 앱을 빌드하려면 명령 셸에서 다음 명령을 실행합니다.

        dotnet build

3. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 웹앱을 로컬로 실행합니다.

        dotnet run

4. 브라우저 창을 시작하고 로컬로 호스팅된 웹앱의 기본 URL인 `http://localhost:5000`으로 이동합니다.

    ![로컬로 빠른 시작 앱 시작](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>다음 단계

* [Azure 리소스 통합을 위한 관리형 ID](./integrate-azure-managed-service-identity.md)
