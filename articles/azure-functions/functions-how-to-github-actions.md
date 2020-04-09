---
title: GitHub 작업을 사용하여 Azure 함수에서 코드 업데이트
description: GitHub 작업을 사용하여 GitHub에서 Azure Functions 프로젝트를 빌드하고 배포하는 워크플로를 정의하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: 54010269e5b61ebf28a29dd3165c4310f3472817
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878207"
---
# <a name="continuous-delivery-by-using-github-action"></a>GitHub 작업을 사용하여 연속 제공

[GitHub 작업을](https://github.com/features/actions) 사용하면 Azure에서 기능 앱에 함수 코드를 자동으로 빌드하고 배포하는 워크플로를 정의할 수 있습니다. 

GitHub 작업에서 [워크플로는](https://help.github.com/articles/about-github-actions#workflow) GitHub 리포지토리에서 정의하는 자동화된 프로세스입니다. 이 프로세스는 GitHub에서 함수 앱 프로젝트를 빌드하고 배포하는 방법을 GitHub에 알려줍니다. 

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에 의해 정의됩니다. 이 정의에는 워크플로를 구성하는 다양한 단계와 매개 변수가 포함되어 있습니다. 

Azure Functions 워크플로의 경우 파일에는 세 가지 섹션이 있습니다. 

| 섹션 | 작업 |
| ------- | ----- |
| **인증** | <ol><li>서비스 주체를 정의합니다.</li><li>게시 프로필을 다운로드합니다.</li><li>GitHub 비밀을 만듭니다.</li></ol>|
| **빌드** | <ol><li>환경을 설정합니다.</li><li>함수 앱을 빌드합니다.</li></ol> |
| **배포** | <ol><li>함수 앱을 배포합니다.</li></ol>|

> [!NOTE]
> 인증에 게시 프로필을 사용하기로 결정한 경우 서비스 주체를 만들 필요가 없습니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI에서](/cli/azure/) [az 광고 sp 만들기-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 [서비스 주체를](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 만들 수 있습니다. Azure 포털에서 [Azure 클라우드 셸을](https://shell.azure.com) 사용하거나 **시도** 단추를 선택하여 이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

이 예제에서는 리소스의 자리 표시자를 구독 ID, 리소스 그룹 및 함수 앱 이름으로 바꿉니다. 출력은 함수 앱에 대한 액세스를 제공하는 역할 할당 자격 증명입니다. GitHub에서 인증하는 데 사용할 수 있는 이 JSON 개체를 복사합니다.

> [!IMPORTANT]
> 항상 최소 액세스 권한을 부여하는 것이 좋습니다. 따라서 이전 예제의 범위는 전체 리소스 그룹이 아닌 특정 함수 앱으로 제한됩니다.

## <a name="download-the-publishing-profile"></a>게시 프로필 다운로드

앱의 **개요** 페이지로 이동하여 **게시 프로필 을**클릭하여 함수 앱의 게시 프로필을 다운로드할 수 있습니다.

   ![게시 프로필 다운로드](media/functions-how-to-github-actions/get-publish-profile.png)

파일의 내용을 복사합니다.

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

1. [GitHub에서](https://github.com)리포지토리로 검색하고 **설정** > **추가 를** > **선택합니다.**

   ![비밀 추가](media/functions-how-to-github-actions/add-secret.png)

1. 새 비밀을 추가합니다.

   * Azure CLI를 사용하여 만든 서비스 주체를 사용하는 경우 `AZURE_CREDENTIALS` **Name**에 사용합니다. 그런 다음 **복사된**JSON 개체 출력을 값에 붙여넣고 **비밀 추가를**선택합니다.
   * 게시 프로필을 사용하는 경우 **이름** `SCM_CREDENTIALS` 에 사용합니다. 그런 다음 게시 프로필의 파일 콘텐츠를 **값에**사용하고 **비밀 추가를**선택합니다.

이제 GitHub는 Azure에서 함수 앱을 인증할 수 있습니다.

## <a name="set-up-the-environment"></a>환경 설정 

환경 설정은 언어별 게시 설정 작업을 사용하여 수행됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예제에서는 `actions/setup-node` 작업을 사용하여 환경을 설정하는 워크플로의 일부를 보여 주며 있습니다.

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 `actions/setup-python` 작업을 사용하여 환경을 설정하는 워크플로의 일부를 보여 주며 있습니다.

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 `actions/setup-dotnet` 작업을 사용하여 환경을 설정하는 워크플로의 일부를 보여 주며 있습니다.

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 `actions/setup-java` 작업을 사용하여 환경을 설정하는 워크플로의 일부를 보여 주며 있습니다.

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>함수 앱 빌드

이는 언어및 Azure Functions에서 지원하는 언어에 따라 다르며 이 섹션은 각 언어의 표준 빌드 단계여야 합니다.

다음 예제에서는 언어별 함수 앱을 빌드하는 워크플로의 일부를 보여 주며, 이 부분은 다음과 같은 기능 응용 프로그램을 빌드합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>함수 앱 배포

함수 앱에 코드를 배포하려면 `Azure/functions-action` 작업을 사용해야 합니다. 이 작업에는 다음 두 가지 매개 변수가 있습니다.

|매개 변수 |설명  |
|---------|---------|
|**_앱 이름_** | (필수) 함수 앱의 이름입니다. |
|_**슬롯 이름**_ | (선택 사항) 배포할 [배포 슬롯의](functions-deployment-slots.md) 이름입니다. 함수 앱에 슬롯이 이미 정의되어 있어야 합니다. |


다음 예제에서는 다음 의 `functions-action`버전 1을 사용합니다.

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>다음 단계

전체 워크플로 .yaml을 보려면 이름에 있는 [Azure GitHub 작업 워크플로 샘플 리포지토리의](https://aka.ms/functions-actions-samples) `functionapp` 파일 중 하나를 참조하십시오. 이러한 샘플을 워크플로의 시작점으로 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub 작업에 대해 자세히 알아보기](https://help.github.com/en/articles/about-github-actions)
