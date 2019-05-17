---
title: Azure App Configuration을 사용하여 지속적인 통합 및 배포 파이프라인 통합 | Microsoft Docs
description: 이 자습서에서는 지속적인 통합 및 배포를 수행하는 동안 Azure App Configuration의 데이터를 사용하여 구성 파일을 생성하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 26bd49af7245d6e6dde3162a2e1d95c54f13e35b
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415933"
---
# <a name="integrate-with-a-cicd-pipeline"></a>CI/CD 파이프라인과 통합

이 문서에서는 지속적인 통합 및 지속적인 배포 시스템에서 Azure App Configuration의 데이터를 사용하는 다양한 방법에 대해 설명합니다.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Azure DevOps 파이프라인에서 App Configuration 앱 구성

Azure DevOps 파이프라인이 있는 경우 App Configuration에서 키-값을 가져오고 작업 변수로 설정할 수 있습니다. [Azure App Configuration DevOps 확장](https://go.microsoft.com/fwlink/?linkid=2091063)은 이 기능을 제공하는 추가 기능 모듈입니다. 해당 지침에 따라 빌드 또는 릴리스 작업 시퀀스에서 확장을 사용하면 됩니다.

## <a name="deploy-app-configuration-data-with-your-application"></a>애플리케이션과 함께 App Configuration 데이터 배포

애플리케이션이 Azure App Configuration에 의존하는데 연결할 수 없는 경우 실행되지 않을 수 있습니다. 이러한 이벤트를 처리하기 위해 애플리케이션 복원력을 향상시킬 수 있지만 이러한 이벤트는 발생하지 않을 가능성이 높습니다. 이렇게 하려면 시작할 때 애플리케이션과 함께 배포되어 로컬로 로드된 파일에 현재 구성 데이터를 패키징해야 합니다. 이 방법을 사용하면 애플리케이션이 적어도 기본 설정 값을 갖게 됩니다. 앱 구성 저장소에 새로운 변경 내용이 있으면 해당 변경 내용이 이러한 값을 덮어씁니다.

Azure App Configuration의 [Export](./howto-import-export-data.md#export-data) 함수를 사용하면 현재 구성 데이터를 단일 파일로 검색하는 프로세스를 자동화할 수 있습니다. 그런 다음, 이 파일을 CI/CD(지속적인 통합 및 지속적인 배포) 파이프라인의 빌드 또는 배포 단계에 포함하면 됩니다.

다음은 App Configuration 데이터를 빠른 시작에서 소개한 웹앱의 빌드 단계로 포함하는 방법을 보여주는 예제입니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 ASP.NET Core 앱 만들기](./quickstart-aspnet-core-app.md)를 완료합니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

### <a name="prerequisites"></a>필수 조건

로컬로 빌드하는 경우 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 아직 없으면 Azure CLI를 다운로드하여 설치합니다.

클라우드 빌드를 수행하려면(예: Azure DevOps) 빌드 시스템에 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 설치되어 있어야 합니다.

### <a name="export-an-app-configuration-store"></a>앱 구성 저장소 내보내기

1. *.csproj* 파일을 열고 다음 스크립트를 추가합니다.

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    앱 구성 저장소와 연결된 *ConnectionString*을 환경 변수로 추가합니다.

2. *Program.cs*를 열고, `config.AddJsonFile()` 메서드를 호출하여 내보낸 JSON 파일을 사용하도록 `CreateWebHostBuilder` 메서드를 업데이트합니다.

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

### <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **ConnectionString**이라는 환경 변수를 앱 구성 저장소에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. .NET Core CLI를 사용하여 앱을 빌드하려면 명령 셸에서 다음 명령을 실행합니다.

        dotnet build

3. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 웹앱을 로컬로 실행합니다.

        dotnet run

4. 브라우저 창을 열고, 로컬로 호스트되는 웹앱의 기본 URL인 `http://localhost:5000`으로 이동합니다.

    ![로컬로 빠른 시작 앱 시작](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 배포 파이프라인에서 사용할 Azure App Configuration 데이터를 내보냈습니다. App Configuration을 사용하는 방법에 대해 자세히 알아보려면 Azure CLI 샘플로 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
