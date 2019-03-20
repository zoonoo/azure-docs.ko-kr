---
title: Azure 앱 구성을 사용 하 여 연속 통합 및 배달 파이프라인을 사용 하 여 통합 | Microsoft Docs
description: 지속적인 통합 및 업데이트 하는 동안 Azure 앱 구성에서 데이터를 사용 하 여 구성 파일을 생성 하는 방법 알아보기
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
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224331"
---
# <a name="integrate-with-a-cicd-pipeline"></a>CI/CD 파이프라인과 통합

Azure 앱 구성에 연결할 수 없는 원격 가능성에 대해 응용 프로그램의 복원 력을 향상 시킬 수 있습니다. 이렇게 하려면 구성 데이터를 현재 배포 된 로드 및 응용 프로그램을 사용 하 여 로컬로 시작 하는 동안 파일로 패키지 합니다. 이 방법을 사용 하면 응용 프로그램에 기본 설정 값 이상입니다. 사용 가능한 경우 앱 구성 저장소의 최신 변경 내용을 이러한 값을 덮어씁니다.

사용 하는 [내보내기](./howto-import-export-data.md#export-data) 함수 앱 구성을 Azure의 단일 파일로 현재 구성 데이터를 검색 하는 프로세스를 자동화할 수 있습니다. 그런 다음 연속 통합 및 지속적인 배포 (CI/CD) 파이프라인에서 빌드 또는 배포 단계에서이 파일을 포함 합니다.

다음 예제에서는 앱 구성을 포함 하는 방법을 보여 줍니다 빌드 데이터를 빠른 시작에 도입 된 웹 앱에 대 한 단계입니다. 계속 하기 전에 완료 [앱 구성을 사용 하 여 ASP.NET Core 앱을 만들고](./quickstart-aspnet-core-app.md) 첫 번째입니다.

이 빠른 시작의 단계를 수행 하려면 어떤 코드 편집기를 사용할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/) 은 뛰어난 옵션 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

로컬로 빌드하는 경우 다운로드 하 고 설치 합니다 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 아직 없는 경우.

클라우드 빌드를 위해 Azure DevOps를 사용 하 여 예를 들어 있는지 확인 합니다 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 빌드 시스템에 설치 됩니다.

## <a name="export-an-app-configuration-store"></a>내보내기는 앱 구성 저장소

1. 오픈 프로그램 *.csproj* 파일을 열고 다음 스크립트를 추가 합니다.

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    추가 된 *ConnectionString* 를 환경 변수로 앱 구성 저장소와 연결 합니다.

2. Program.cs를 열고 업데이트를 `CreateWebHostBuilder` 메서드를 호출 하 여 내보낸된 JSON 파일을 사용 하는 `config.AddJsonFile()` 메서드.

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

1. 이라는 환경 변수를 설정 **ConnectionString**, 앱 구성 저장소에 대 한 액세스 키로 설정 합니다. Windows 명령 프롬프트를 사용 하는 경우 다음 명령을 실행 하 고 변경 내용이 적용 되려면를 허용 하려면 명령 프롬프트를 다시 시작 합니다.

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell을 사용 하는 경우 다음 명령을 실행 합니다.

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    MacOS 또는 Linux를 사용 하는 경우 다음 명령을 실행 합니다.

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. .NET Core CLI를 사용 하 여 앱을 빌드하려면 명령 셸에서 다음 명령을 실행 합니다.

        dotnet build

3. 빌드가 성공적으로 완료 된 후에 웹 앱을 로컬로 실행 하려면 다음 명령을 실행 합니다.

        dotnet run

4. 브라우저 창을 열고 이동할 `http://localhost:5000`, 로컬로 호스트 웹 앱에 대 한 기본 URL 인 합니다.

    ![로컬로 빠른 시작 앱 시작](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>다음 단계

* [Azure 리소스 통합에 대 한 관리 되는 id](./integrate-azure-managed-service-identity.md)
