---
title: GitHub Actions를 사용하여 CI/CD 구성
description: GitHub Actions를 사용하여 CI/CD 파이프라인에서 Azure App Service에 코드를 배포하는 방법을 알아봅니다. 빌드 작업을 사용자 지정하고 복잡한 배포를 실행합니다.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: c17b18f42a5b07252874aef812a50746e069c1a9
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108209736"
---
# <a name="deploy-to-app-service-using-github-actions"></a>GitHub Actions를 사용하여 App Service에 배포

[GitHub Actions](https://docs.github.com/en/actions/learn-github-actions)를 사용하여 워크플로를 자동화하고 GitHub에서 [Azure App Service](overview.md)를 배포하는 작업을 시작합니다. 

## <a name="prerequisites"></a>사전 요구 사항 

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.  
- 작동하는 Azure App Service 앱입니다. 
    - .NET: [Azure에서 ASP.NET Core 웹앱 만들기](quickstart-dotnetcore.md)
    - ASP.NET: [Azure에서 ASP.NET Framework 웹앱 만들기](./quickstart-dotnetcore.md?tabs=netframework48)
    - JavaScript: [Azure App Service에서 Node.js 웹앱 만들기](quickstart-nodejs.md)  
    - Java: [Azure App Service에서 Java 앱 만들기](quickstart-java.md)
    - Python: [Azure App Service에서 Python 앱 만들기](quickstart-python.md)

## <a name="workflow-file-overview"></a>워크플로 파일 개요

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

이 파일에는 다음과 같은 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체 또는 게시 프로필을 정의합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**빌드** | 1. 환경을 설정합니다. <br /> 2. 웹앱을 빌드합니다. |
|**배포** | 1. 웹앱을 배포합니다. |

## <a name="use-the-deployment-center"></a>배포 센터 사용

App Service 배포 센터를 사용하면 GitHub Actions를 신속하게 시작할 수 있습니다. 이 기능은 애플리케이션 스택을 기반으로 워크플로 파일을 자동으로 생성하고 올바른 디렉터리의 GitHub 리포지토리에 커밋합니다.

1. Azure Portal의 웹앱으로 이동합니다.
1. 왼쪽에서 **배포 센터** 를 선택합니다.
1. **지속적인 배포(CI/CD)** 에서 **GitHub** 를 선택합니다.
1. 다음으로, **GitHub Actions** 를 선택합니다.
1. 드롭다운을 사용하여 GitHub 리포지토리, 분기 및 애플리케이션 스택을 선택합니다.
    - 선택한 분기가 보호되는 분기인 경우에도 워크플로 파일을 계속 추가할 수 있습니다. 계속하기 전에 분기 보호를 검토해야 합니다.
1. 마지막 화면에서 선택 항목을 검토하고 리포지토리로 커밋될 워크플로 파일을 미리 볼 수 있습니다. 선택 항목이 올바르면 **마침** 을 클릭합니다.

이렇게 하면 워크플로 파일이 리포지토리에 커밋됩니다. 앱을 빌드하고 배포하는 워크플로가 즉시 시작됩니다.

## <a name="set-up-a-workflow-manually"></a>워크플로 수동 설정

배포 센터를 사용하지 않고도 워크플로를 배포할 수도 있습니다. 이렇게 하려면 먼저 배포 자격 증명을 생성해야 합니다. 

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

Azure App Services를 이용하여 GitHub Actions를 인증하는 권장 방법은 게시 프로필을 사용하는 것입니다. 서비스 주체를 사용하여 인증할 수도 있지만 이 프로세스에서는 추가 단계를 진행해야 합니다. 

Azure를 이용하여 인증할 수 있도록 게시 프로필 자격 증명이나 서비스 주체를 [GitHub 비밀](https://docs.github.com/en/actions/reference/encrypted-secrets)로 저장합니다. 워크플로 내에서 비밀에 액세스하게 됩니다. 

# <a name="publish-profile"></a>[게시 프로필](#tab/applevel)

게시 프로필은 앱 수준 자격 증명입니다. 게시 프로필을 GitHub 암호로 설정합니다. 

1. Azure Portal에서 해당 앱 서비스로 이동합니다. 

1. **개요** 페이지에서 **게시 프로필 가져오기** 를 선택합니다.

1. 다운로드한 파일을 저장합니다. 파일의 내용을 사용하여 GitHub 비밀을 만들게 됩니다.

> [!NOTE]
> 2020년 10월부터 Linux 웹앱에서는 앱 설정 `WEBSITE_WEBDEPLOY_USE_SCM`을 `true`로 설정해야 **게시 프로필을 다운로드할 수 있습니다**. 이 요구 사항은 나중에 제거됩니다.

# <a name="service-principal"></a>[서비스 주체](#tab/userlevel)

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

위의 예제에서 자리 표시자를 구독 ID, 리소스 그룹 이름 및 앱 이름으로 바꿉니다. 출력은 아래와 비슷한 App Service 앱에 대한 액세스를 제공하는 역할 할당 자격 증명이 있는 JSON 개체입니다. 나중에 사용할 수 있도록 이 JSON 개체를 복사합니다.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 이전 예제의 범위는 전체 리소스 그룹이 아닌 특정 App Service 앱으로 제한됩니다.

---

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성


# <a name="publish-profile"></a>[게시 프로필](#tab/applevel)

[GitHub](https://github.com/)에서 리포지토리를 검색하고 **설정 > 비밀 > 새 비밀 추가** 를 선택합니다.

[앱 수준 자격 증명](#generate-deployment-credentials)을 사용하려면 다운로드한 게시 프로필 파일의 내용을 비밀의 값 필드에 붙여넣습니다. 비밀 `AZURE_WEBAPP_PUBLISH_PROFILE`의 이름을 지정합니다.

GitHub 워크플로를 구성하는 경우에는 Azure 웹앱 배포 작업에서 `AZURE_WEBAPP_PUBLISH_PROFILE`을 사용합니다. 예를 들면 다음과 같습니다.
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[서비스 주체](#tab/userlevel)

[GitHub](https://github.com/)에서 리포지토리를 검색하고 **설정 > 비밀 > 새 비밀 추가** 를 선택합니다.

[사용자 수준 자격 증명](#generate-deployment-credentials)을 사용하려면 Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 이름을 `AZURE_CREDENTIALS`로 지정합니다.

나중에 워크플로 파일을 구성할 때 이 비밀을 Azure 로그인 작업의 `creds` 입력에 사용합니다. 예를 들면 다음과 같습니다.

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>환경 설정

환경 설정은 설정 작업 중 하나를 사용하여 수행할 수 있습니다.

|**언어**  |**설정 작업**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

다음 예에서는 지원되는 여러 언어에 대해 환경을 설정하는 방법을 확인할 수 있습니다.

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>웹앱 빌드

언어에 따라 웹앱을 빌드하고 Azure App Service 변경 내용을 배포하는 프로세스입니다. 

다음 예제에서는 지원되는 다양한 언어로 웹앱을 빌드하는 워크플로의 일부를 확인할 수 있습니다.

모든 언어에 대해 `working-directory`를 사용하여 웹앱 루트 디렉터리를 설정할 수 있습니다. 

**.NET**

환경 변수인 `AZURE_WEBAPP_PACKAGE_PATH`는 웹앱 프로젝트의 경로를 설정합니다. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

NuGet 종속성을 복원하고 `run`을 사용하여 msbuild를 실행할 수 있습니다. 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.2

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Node.js의 경우에는 `working-directory`를 변경하거나 `pushd`의 npm 디렉터리를 변경할 수 있습니다. 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>App Service에 배포

App Service 앱에 코드를 배포하려면 `azure/webapps-deploy@v2` 작업을 사용합니다. 이 작업에는 다음 4개의 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **app-name** | (필수) App Service 앱의 이름 | 
| **publish-profile** | (선택 사항) 웹 배포 비밀을 포함하는 게시 프로필 파일 콘텐츠 |
| **package** | (선택 사항) 패키지 또는 폴더 경로. 경로에는 *.zip, *.war, *.jar 또는 배포할 폴더가 포함될 수 있습니다. |
| **slot-name** | (선택 사항) 프로덕션 [슬롯](deploy-staging-slots.md)이 아닌 기존 슬롯 입력 |


# <a name="publish-profile"></a>[게시 프로필](#tab/applevel)

### <a name="net-core"></a>.NET Core

Azure 게시 프로필을 사용하여 .NET Core 앱을 빌드하고 Azure에 배포합니다. `publish-profile` 입력은 이전에 만든 `AZURE_WEBAPP_PUBLISH_PROFILE` 암호를 참조합니다.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

인증에 NuGet과 `publish-profile`을 사용하는 ASP.NET MVC 앱을 빌드하고 배포합니다. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@main  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.2

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Azure 게시 프로필을 사용하여 Java Spring 앱을 빌드하고 Azure에 배포합니다. `publish-profile` 입력은 이전에 만든 `AZURE_WEBAPP_PUBLISH_PROFILE` 암호를 참조합니다.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

`jar` 대신 `war`을 배포하려면 `package` 값을 변경해야 합니다. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

앱의 게시 프로필을 사용하여 Node.js 앱을 빌드하고 Azure에 배포합니다. `publish-profile` 입력은 이전에 만든 `AZURE_WEBAPP_PUBLISH_PROFILE` 암호를 참조합니다.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

앱의 게시 프로필을 사용하여 Python 앱을 빌드하고 Azure에 배포합니다. `publish-profile` 입력은 이전에 만든 `AZURE_WEBAPP_PUBLISH_PROFILE` 비밀을 참조한다는 점에 주목하세요.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[서비스 주체](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Azure 서비스 주체를 사용하여 .NET Core 앱을 빌드하고 Azure에 배포합니다. `creds` 입력은 이전에 만든 `AZURE_CREDENTIALS` 비밀을 참조한다는 점에 주목하세요.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Azure 서비스 주체를 사용하여 ASP.NET MVC 앱을 빌드하고 Azure에 배포합니다. `creds` 입력은 이전에 만든 `AZURE_CREDENTIALS` 비밀을 참조한다는 점에 주목하세요.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@main
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.2

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Azure 서비스 주체를 사용하여 Java Spring 앱을 빌드하고 Azure에 배포합니다. `creds` 입력은 이전에 만든 `AZURE_CREDENTIALS` 비밀을 참조한다는 점에 주목하세요.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Azure 서비스 주체를 사용하여 Node.js 앱을 빌드하고 Azure에 배포합니다. `creds` 입력은 이전에 만든 `AZURE_CREDENTIALS` 비밀을 참조한다는 점에 주목하세요.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Azure 서비스 주체를 사용하여 Python 앱을 빌드하고 Azure에 배포합니다. `creds` 입력은 이전에 만든 `AZURE_CREDENTIALS` 비밀을 참조한다는 점에 주목하세요.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>다음 단계

GitHub에서 다양한 리포지토리로 그룹화된 일련의 작업을 찾을 수 있습니다. 각 리포지토리에는 CI/CD에 GitHub를 사용하고 Azure에 앱을 배포하는 데 도움이 되는 문서 및 예제가 포함됩니다.

- [Azure에 배포할 작업 워크플로](https://github.com/Azure/actions-workflow-samples)

- [Azure 로그인](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [컨테이너용 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 로그인/로그아웃](https://github.com/Azure/docker-login)

- [워크플로를 트리거하는 이벤트](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s 배포](https://github.com/Azure/k8s-deploy)

- [시작 워크플로](https://github.com/actions/starter-workflows)