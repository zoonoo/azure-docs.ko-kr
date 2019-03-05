---
title: Jenkins Azure Functions 플러그 인을 사용하여 Azure Functions에 배포
description: Jenkins Azure Functions 플러그 인을 사용하여 Azure Functions에 배포하는 방법 알아보기
ms.service: jenkins
keywords: jenkins, azure, devops, java, azure functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 02/23/2019
ms.openlocfilehash: 1138af0e073f68842861df86acd4d9d6eb467782
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824713"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plugin"></a>Jenkins Azure Functions 플러그 인을 사용하여 Azure Functions에 배포

[Azure Functions](/azure/azure-functions/)는 서버를 사용하지 않는 계산 서비스입니다. Azure Functions를 사용하여 프로비저닝하거나 인프라를 관리하지 않고 요청 시 코드를 실행할 수 있습니다. 이 자습서에서는 Azure Functions 플러그 인을 사용하여 Azure Functions에 Java 함수를 배포하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 을 만듭니다.
- **Jenkins 서버**: Jenkins 서버가 설치되어 있지 않다면 [Azure에서 Jenkins 서버 만들기](./install-jenkins-solution-template.md) 문서를 참조하세요.

 > [!TIP]
 > 이 자습서에 사용된 소스 코드는 [Visual Studio China GitHub 리포지토리](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java)에 있습니다.

## <a name="create-a-java-function"></a>Java 함수 만들기

Java 런타임 스택을 사용하여 Java 함수를 만들려면 [Azure Portal](https://portal.azure.com) 또는 [Azure CLI](/cli/azure/?view=azure-cli-latest) 중 하나를 사용합니다.

다음 단계에서는 Azure CLI를 사용하여 Java 함수를 만드는 방법을 보여줍니다.

1. **&lt;resource_group>** 자리 표시자를 자신의 리소스 그룹 이름으로 바꿔서 리소스 그룹을 만듭니다.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. 자리 표시자를 적절한 값으로 바꿔서 Azure Storage 계정을 만듭니다.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. 자리 표시자를 적절한 값으로 바꿔서 테스트 함수 앱을 만듭니다.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```
    
1. 자리 표시자를 적절한 값으로 바꿔서 버전 2.x 런타임으로 업데이트합니다.

    ```cli
    az functionapp config appsettings set --name <function_app> --resource-group <resource_group> --settings FUNCTIONS_EXTENSION_VERSION=~2
    ```

## <a name="prepare-jenkins-server"></a>Jenkins 서버 준비

다음 단계에서는 Jenkins 서버를 준비하는 방법을 설명합니다.

1. Azure에서 [Jenkins 서버](https://aka.ms/jenkins-on-azure)를 배포합니다. Jenkins 서버 인스턴스가 아직 설치되어 있지 않은 경우 [Azure에서 Jenkins 서버 만들기](./install-jenkins-solution-template.md) 과정을 안내하는 문서를 참조하세요.

1. SSH를 사용하여 Jenkins 인스턴스에 로그인합니다.

1. Jenkins 인스턴스에서 다음 명령을 사용하여 Maven을 설치합니다.

    ```terminal
    sudo apt install -y maven
    ```

1. Jenkins 인스턴스에서 터미널 프롬프트에 다음 명령을 실행하여 [Azure Functions 핵심 도구](/azure/azure-functions/functions-run-local)를 설치합니다.

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Jenkins 대시보드에서 다음 플러그 인을 설치합니다.

    - Azure Functions 플러그 인
    - EnvInject 플러그 인

1. Jenkins에서 Azure 서비스 주체는 Azure 리소스를 인증하고 액세스해야 합니다. 단계별 지침은 [Azure App Service에 배포](./tutorial-jenkins-deploy-web-app-azure-app-service.md)를 참조하세요.

1. Azure 서비스 주체를 사용하여 “Microsoft Azure 서비스 주체” 자격 증명 유형을 Jenkins에 추가합니다. [Azure App Service에 배포](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) 자습서를 참조하세요.

## <a name="create-a-jenkins-pipeline"></a>Jenkins 파이프라인 만들기

이 섹션에서는 [Jenkins 파이프라인](https://jenkins.io/doc/book/pipeline/)을 만듭니다.

1. Jenkins 대시보드에서 파이프라인을 만듭니다.

1. **실행을 위한 환경을 준비**를 사용합니다.

1. 자리 표시자를 사용자 환경에 적절한 값으로 바꿔서 다음 환경 변수를 **속성 콘텐츠**에 추가합니다.

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. **파이프라인 > 정의** 섹션에서 **SCM의 파이프라인 스크립트**를 선택합니다.

1. 제공된 [스크립트 예제](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile)를 사용하여 SCM 리포지토리 URL 및 스크립트 경로를 입력합니다.

## <a name="build-and-deploy"></a>빌드 및 배포

이제 Jenkins 작업을 실행할 시간입니다.

1. 먼저 [Azure Functions HTTP 트리거 및 바인딩](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys) 문서의 지침을 통해 권한 부여 키를 가져옵니다.

1. 브라우저에서 앱의 URL을 입력합니다. 자리 표시자를 적절한 값으로 바꾸고 **&lt;input_number>** 에 대한 숫자 값을 Java 함수에 대한 입력으로 지정합니다.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. 다음 예제 출력과 유사한 결과가 표시됩니다(여기서는 홀수 365를 테스트로 사용).

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 다음 단계에 따라 만들었던 관련 리소스를 삭제합니다.

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>다음 단계

Azure Functions에 대해 자세히 알아보려면
> [!div class="nextstepaction"]
> [Azure Functions 설명서](/azure/azure-functions/) 리소스를 참조하세요.