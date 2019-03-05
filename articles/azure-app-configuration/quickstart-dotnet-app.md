---
title: .NET Framework 지원 Azure App Configuration용 빠른 시작 | Microsoft Docs
description: .NET Framework 앱 지원 Azure App Configuration 사용 빠른 시작
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b5e41b1f9ee982b8ff8c86232f715d5dab705cd6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962165"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>빠른 시작: Azure App Configuration을 사용하여 .NET Framework 앱 만들기

Azure App Configuration은 Azure의 관리 구성 서비스로서, 코드와 구분하여 한 곳에서 모든 애플리케이션 설정을 쉽게 저장하고 관리할 수 있습니다. 이 빠른 시작은 .NET Framework 기반 Windows 데스크톱 콘솔 앱으로 서비스를 통합하는 방법을 보여줍니다.

![빠른 시작 전체 로컬](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 이미 설치하지 않은 경우 [Visual Studio 2017](https://visualstudio.microsoft.com/vs) 및 [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) 이상을 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>앱 구성 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>.NET 콘솔 앱 만들기

1. Visual Studio를 실행하고 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.

2. **새 프로젝트** 대화 상자에서 **설치됨**을 선택하고 **Visual C#** > **Windows 데스크톱**을 확장한 후 **콘솔 앱(.NET Framework)** 을 선택하고 프로젝트에 대한 **이름**을 입력한 다음, **.NET Framework 4.7.1** 이상을 선택하고 **확인**을 클릭합니다.

## <a name="connect-to-app-configuration-store"></a>앱 구성 저장소에 연결

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. **찾아보기** 탭에서 다음 NuGet 패키지를 검색하여 프로젝트에 추가합니다(찾을 수 없는 경우 **시험판 포함** 선택).
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. 프로젝트의 *App.config* 파일을 다음과 같이 업데이트합니다.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   단, 환경 변수 `ConnectionString`에서 앱 구성 저장소의 연결 문자열을 읽으므로, `appSettings` 섹션의 `configBuilders` 속성에서 `MyConfigStore` 앞에 `Environment` 구성 빌더를 추가하는 것이 중요합니다.

3. *Program.cs*를 열고 `ConfigurationManager`를 호출하여 App Configuration을 사용하도록 `Main` 메서드를 업데이트합니다.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. **ConnectionString**이라는 환경 변수를 앱 구성 저장소의 연결 문자열로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 사용합니다.

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell을 사용하는 경우 다음 명령을 사용합니다.

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Visual Studio를 다시 시작하여 변경 사항을 적용한 다음, 키보드에서 **Ctrl + F5**를 눌러 콘솔 앱을 빌드하고 실행합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 앱 구성 저장소를 만들고 .NET Framework 콘솔 앱에 사용해봤습니다. App Configuration을 사용하는 방법을 자세히 알아보려면 인증을 보여주는 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Resources Integration에 대한 관리 ID](./integrate-azure-managed-service-identity.md)
