---
title: GitHub 작업으로 CI/CD 구성
description: GitHub 작업을 사용하여 CI/CD 파이프라인에서 Azure 앱 서비스에 코드를 배포하는 방법을 알아봅니다. 빌드 작업을 사용자 지정하고 복잡한 배포를 실행합니다.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084994"
---
# <a name="deploy-to-app-service-using-github-actions"></a>GitHub 작업을 사용하여 앱 서비스에 배포

[GitHub 작업은](https://help.github.com/en/articles/about-github-actions) 자동화된 소프트웨어 개발 수명 주기 워크플로를 구축할 수 있는 유연성을 제공합니다. GitHub에 대한 Azure 앱 서비스 작업을 사용하면 GitHub 작업을 사용하여 [Azure 앱 서비스에](overview.md) 배포하는 워크플로를 자동화할 수 있습니다.

> [!IMPORTANT]
> GitHub 작업은 현재 베타 버전입니다. 먼저 GitHub 계정을 사용하여 [미리 보기에 가입해야](https://github.com/features/actions) 합니다.
> 

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에 의해 정의됩니다. 이 정의에는 워크플로를 구성하는 다양한 단계와 매개 변수가 포함되어 있습니다.

Azure 앱 서비스 워크플로의 경우 파일에는 세 가지 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체 정의 <br /> 2. GitHub 비밀 만들기 |
|**빌드** | 1. 환경 설정 <br /> 2. 웹 앱 빌드 |
|**배포** | 1. 웹 앱 배포 |

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI에서](https://docs.microsoft.com/cli/azure/) [az 광고 sp 만들기-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 [서비스 주체를](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 만들 수 있습니다. Azure 포털에서 [Azure 클라우드 셸을](https://shell.azure.com/) 사용하거나 **시도** 단추를 선택하여 이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

이 예제에서는 리소스의 자리 표시자를 구독 ID, 리소스 그룹 이름 및 앱 이름으로 바꿉니다. 출력은 앱 서비스 앱에 대한 액세스를 제공하는 역할 할당 자격 증명입니다. GitHub에서 인증하는 데 사용할 수 있는 이 JSON 개체를 복사합니다.

> [!NOTE]
> 인증을 위해 게시 프로필을 사용하기로 결정한 경우 서비스 주체를 만들 필요가 없습니다.

> [!IMPORTANT]
> 항상 최소 액세스 권한을 부여하는 것이 좋습니다. 따라서 이전 예제의 범위는 전체 리소스 그룹이 아닌 특정 앱 서비스 앱으로 제한됩니다.

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

배포를 위해 프로필 게시와 같은 앱 수준 자격 증명을 사용할 수도 있습니다. 단계를 수행하여 비밀을 구성합니다.

1. 게시 프로필 받기 옵션을 사용하여 포털에서 앱 서비스 앱에 대한 게시 **프로필을** 다운로드합니다.

2. [GitHub에서](https://github.com/)리포지토리를 탐색하고 **비밀 > > 새 비밀 추가를** 선택합니다.

    ![secrets](media/app-service-github-actions/secrets.png)

3. 다운로드한 게시 프로필 파일의 내용을 비밀의 값 필드에 붙여넣습니다.

4. 이제 분기의 워크플로 파일에서 배포 `.github/workflows/workflow.yml` Azure `publish-profile` Web App 작업의 입력에 대한 비밀을 바꿉습니다.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. 한 번 정의 된 아래와 같이 비밀을 볼 수 있습니다.

    ![secrets](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>환경 설정

환경 설정 은 설치 작업 중 하나를 사용하여 수행 할 수 있습니다.

|**언어**  |**설정 작업**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

다음 예제는 지원되는 다양한 언어에 대한 환경을 설정하는 워크플로의 일부를 보여 주며 있습니다.

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>웹 앱 빌드

Azure App Service에서 지원하는 언어 및 언어에 따라 이 섹션은 각 언어의 표준 빌드 단계여야 합니다.

다음 예제에서는 웹 앱을 빌드하는 워크플로의 일부를 지원되는 다양한 언어로 보여 준다.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>App Service에 배포

앱을 앱 서비스 앱에 배포하려면 `azure/webapps-deploy@v2` 작업을 사용합니다. 이 작업에는 다음 네 가지 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **앱 이름** | (필수) 앱 서비스 앱 이름 | 
| **게시 프로필** | (선택 사항) 웹 배포 비밀로 프로필 파일 내용 게시 |
| **package** | (선택 사항) 패키지 또는 폴더로의 경로입니다. *.zip, *.war, *.jar 또는 배포폴더 |
| **슬롯 이름** | (선택 사항) 프로덕션 슬롯 이외의 기존 슬롯 입력 |

### <a name="deploy-using-publish-profile"></a>게시 프로필을 사용하여 배포

다음은 게시 프로필을 사용하여 Node.js 앱을 Azure에 빌드하고 배포하는 샘플 워크플로입니다.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Azure 서비스 주체를 사용하여 배포

다음은 Azure 서비스 주체를 사용하여 Node.js 앱을 빌드하고 Azure에 배포하는 샘플 워크플로입니다.

```yaml
on: [push]

name: Node.js

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
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>다음 단계

Ci/CD에 GitHub를 사용하고 Azure에 앱을 배포하는 데 도움이 되는 설명서와 예제가 포함된 각 작업 집합은 GitHub의 다른 리포지토리로 그룹화되어 있습니다.

- [Azure에 배포할 작업 워크플로우](https://github.com/Azure/actions-workflow-samples)

- [Azure 로그인](https://github.com/Azure/login)

- [Azure 웹앱](https://github.com/Azure/webapps-deploy)

- [컨테이너용 Azure 웹앱](https://github.com/Azure/webapps-container-deploy)

- [도커 로그인/로그아웃](https://github.com/Azure/docker-login)

- [워크플로를 트리거하는 이벤트](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 배포](https://github.com/Azure/k8s-deploy)

- [시작 워크플로우](https://github.com/actions/starter-workflows)
