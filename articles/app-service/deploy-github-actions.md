---
title: GitHub Actions를 사용하여 CI/CD 구성
description: GitHub Actions를 사용하여 CI/CD 파이프라인에서 Azure App Service에 코드를 배포하는 방법을 알아봅니다. 빌드 작업을 사용자 지정하고 복잡한 배포를 실행합니다.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure
ms.openlocfilehash: 6c768df964d46364a8ca501c078dbecaf1aaa21f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095563"
---
# <a name="deploy-to-app-service-using-github-actions"></a>GitHub Actions를 사용하여 App Service에 배포

Github에서 워크플로를 자동화 하 고 [Azure App Service](overview.md) 에 배포 하는 [github 작업](https://help.github.com/en/articles/about-github-actions) 을 시작 하세요. 

## <a name="prerequisites"></a>필수 구성 요소 

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [무료로](https://github.com/join)등록 하세요.  
- 작동 하는 Azure App Service 앱입니다. 
    - .NET: [Azure에서 ASP.NET Core 웹 앱 만들기](quickstart-dotnetcore.md)
    - ASP.NET: [Azure에서 ASP.NET Framework 웹 앱 만들기](quickstart-dotnet-framework.md)
    - JavaScript: [Azure App Service에서 Node.js 웹 앱 만들기](quickstart-nodejs.md)  
    - Java: [Azure App Service에서 java 앱 만들기](quickstart-java.md)
    - Python: [Azure App Service에서 python 앱 만들기](quickstart-python.md)

## <a name="workflow-file-overview"></a>워크플로 파일 개요

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

파일에는 다음과 같은 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체 또는 게시 프로필을 정의 합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**빌드** | 1. 환경을 설정 합니다. <br /> 2. 웹 앱을 빌드합니다. |
|**배포** | 1. 웹 앱을 배포 합니다. |

## <a name="use-the-deployment-center"></a>배포 센터 사용

App Service Deployment Center를 사용 하 여 GitHub 작업을 신속 하 게 시작할 수 있습니다. 그러면 응용 프로그램 스택에 따라 워크플로 파일이 자동으로 생성 되 고 올바른 디렉터리의 GitHub 리포지토리에 커밋합니다.

1. Azure Portal에서 webapp로 이동 합니다.
1. 왼쪽에서 **배포 센터** 를 클릭 합니다.
1. **연속 배포 (CI/CD)** 에서 **GitHub** 를 선택 합니다.
1. 다음으로 **GitHub 작업** 을 선택 합니다.
1. 드롭다운을 사용 하 여 GitHub 리포지토리, 분기 및 응용 프로그램 스택 선택
    - 선택한 분기가 보호 되는 경우에도 워크플로 파일을 계속 추가할 수 있습니다. 계속 하기 전에 분기 보호를 검토 해야 합니다.
1. 최종 화면에서 선택 항목을 검토 하 고 리포지토리로 커밋될 워크플로 파일을 미리 볼 수 있습니다. 선택이 올바르면 **마침** 을 클릭 합니다.

이렇게 하면 워크플로 파일이 리포지토리에 커밋됩니다. 앱을 빌드하고 배포 하는 워크플로가 즉시 시작 됩니다.

## <a name="set-up-a-work-manually"></a>수동으로 작업 설정

또한 Deployment Center를 사용 하지 않고도 워크플로를 배포할 수 있습니다. 이렇게 하려면 먼저 배포 자격 증명을 생성 해야 합니다. 

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

GitHub 작업에 대해 Azure 앱 서비스를 사용 하 여 인증 하는 권장 방법은 게시 프로필을 사용 하는 것입니다. 서비스 주체를 사용 하 여 인증할 수도 있지만이 프로세스에는 추가 단계가 필요 합니다. 

Azure를 인증 하기 위해 게시 프로필 자격 증명 또는 서비스 주체를 [GitHub 암호로](https://docs.github.com/en/actions/reference/encrypted-secrets) 저장 합니다. 워크플로 내에서 비밀에 액세스 합니다. 

# <a name="publish-profile"></a>[프로필 게시](#tab/applevel)

게시 프로필은 앱 수준 자격 증명입니다. 게시 프로필을 GitHub 암호로 설정 합니다. 

1. Azure Portal에서 app service로 이동 합니다. 

1. **개요** 페이지에서 **게시 프로필 가져오기** 를 선택 합니다.

1. 다운로드한 파일을 저장합니다. 파일의 내용을 사용 하 여 GitHub 비밀을 만듭니다.

> [!NOTE]
> 2020 년 10 월까지 Linux 웹 앱은 `WEBSITE_WEBDEPLOY_USE_SCM` `true` **게시 프로필을 다운로드 하기 전에** 앱 설정이로 설정 되어야 합니다. 이 요구 사항은 나중에 제거 될 예정입니다.

# <a name="service-principal"></a>[서비스 주체](#tab/userlevel)

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

위의 예제에서 자리 표시자를 구독 ID, 리소스 그룹 이름 및 앱 이름으로 바꿉니다. 출력은 아래와 같이 App Service 앱에 대 한 액세스를 제공 하는 역할 할당 자격 증명을 포함 하는 JSON 개체입니다. 나중에이 JSON 개체를 복사 합니다.

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
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 이전 예제의 범위는 전체 리소스 그룹이 아니라 특정 App Service 앱으로 제한 됩니다.

---

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성


# <a name="publish-profile"></a>[프로필 게시](#tab/applevel)

[GitHub](https://github.com/)에서 리포지토리를 찾아보고 **설정 > 비밀을 선택 하 > 새 비밀을 추가** 합니다.

[앱 수준 자격 증명](#generate-deployment-credentials)을 사용 하려면 다운로드 한 게시 프로필 파일의 내용을 비밀의 값 필드에 붙여넣습니다. 비밀의 이름을로 `AZURE_WEBAPP_PUBLISH_PROFILE` 합니다.

GitHub 워크플로를 구성 하는 경우 `AZURE_WEBAPP_PUBLISH_PROFILE` Azure 웹 앱 배포 작업에서를 사용 합니다. 다음은 그 예입니다.
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[서비스 주체](#tab/userlevel)

[GitHub](https://github.com/)에서 리포지토리를 찾아보고 **설정 > 비밀을 선택 하 > 새 비밀을 추가** 합니다.

[사용자 수준 자격 증명](#generate-deployment-credentials)을 사용 하려면 Azure CLI 명령의 전체 JSON 출력을 암호의 값 필드에 붙여넣습니다. 비밀 이름을 `AZURE_CREDENTIALS`로 지정합니다.

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

다음 예에서는 지원 되는 여러 언어에 대해 환경을 설정 하는 방법을 보여 줍니다.

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
    - uses: actions/checkout@master
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

언어에 따라 웹 앱을 빌드하고 Azure App Service 변경 내용을 배포 하는 프로세스입니다. 

다음 예제에서는 웹 앱을 빌드하는 워크플로의 일부를 지원 되는 다른 언어로 보여줍니다.

모든 언어에 대해를 사용 하 여 웹 앱 루트 디렉터리를 설정할 수 있습니다 `working-directory` . 

**.NET**

환경 변수는 `AZURE_WEBAPP_PACKAGE_PATH` 웹 앱 프로젝트에 대 한 경로를 설정 합니다. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

NuGet 종속성을 복원 하 고를 사용 하 여 msbuild를 실행할 수 있습니다 `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Node.js의 경우 `working-directory` 에서 npm 디렉터리를 설정 하거나 변경할 수 있습니다 `pushd` . 

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
| **package** | (선택 사항) 패키지 또는 폴더 경로. 경로에는 * .zip, * war, * .jar 또는 배포할 폴더가 포함 될 수 있습니다. |
| **slot-name** | 필드 프로덕션 [슬롯이](deploy-staging-slots.md) 아닌 기존 슬롯을 입력 하세요. |


# <a name="publish-profile"></a>[프로필 게시](#tab/applevel)

### <a name="net-core"></a>.NET Core

Azure 게시 프로필을 사용 하 여 .NET Core 앱을 빌드하고 Azure에 배포 합니다. `publish-profile`입력은 이전에 `AZURE_WEBAPP_PUBLISH_PROFILE` 만든 암호를 참조 합니다.

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
      - uses: actions/checkout@master
      
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

NuGet을 사용 하 고 인증을 위해 ASP.NET MVC 앱을 빌드 및 배포 `publish-profile` 합니다. 


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

    - uses: actions/checkout@master  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

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

Azure 게시 프로필을 사용 하 여 Java 스프링 앱을 빌드하고 Azure에 배포 합니다. `publish-profile`입력은 이전에 `AZURE_WEBAPP_PUBLISH_PROFILE` 만든 암호를 참조 합니다.

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

대신을 배포 하려면 `war` 값을 `jar` 변경 `package` 합니다. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

앱의 게시 프로필을 사용 하 여 Node.js 앱을 빌드하고 Azure에 배포 합니다. `publish-profile`입력은 이전에 `AZURE_WEBAPP_PUBLISH_PROFILE` 만든 암호를 참조 합니다.

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
    - uses: actions/checkout@master
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

앱의 게시 프로필을 사용 하 여 Python 앱을 빌드하고 Azure에 배포 합니다. `publish-profile`입력 `AZURE_WEBAPP_PUBLISH_PROFILE` 이 앞에서 만든 비밀을 참조 하는 방법을 확인 합니다.

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

Azure 서비스 주체를 사용 하 여 .NET Core 앱을 빌드하고 Azure에 배포 합니다. `creds`입력 `AZURE_CREDENTIALS` 이 앞에서 만든 비밀을 참조 하는 방법을 확인 합니다.


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
      - uses: actions/checkout@master
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

Azure 서비스 주체를 사용 하 여 ASP.NET MVC 앱을 빌드하고 Azure에 배포 합니다. `creds`입력 `AZURE_CREDENTIALS` 이 앞에서 만든 비밀을 참조 하는 방법을 확인 합니다.

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
    - uses: actions/checkout@master  
    
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
      uses: microsoft/setup-msbuild@v1.0.0

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

Azure 서비스 주체를 사용 하 여 Azure에 Java 스프링 앱을 빌드하고 배포 합니다. `creds`입력 `AZURE_CREDENTIALS` 이 앞에서 만든 비밀을 참조 하는 방법을 확인 합니다.

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

Azure 서비스 주체를 사용 하 여 Node.js 앱을 빌드하고 Azure에 배포 합니다. `creds`입력 `AZURE_CREDENTIALS` 이 앞에서 만든 비밀을 참조 하는 방법을 확인 합니다.

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
      uses: actions/checkout@master
   
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

Azure 서비스 주체를 사용 하 여 Python 앱을 빌드하고 Azure에 배포 합니다. `creds`입력 `AZURE_CREDENTIALS` 이 앞에서 만든 비밀을 참조 하는 방법을 확인 합니다.

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

- [워크플로를 트리거하는 이벤트](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 배포](https://github.com/Azure/k8s-deploy)

- [시작 워크플로](https://github.com/actions/starter-workflows)
