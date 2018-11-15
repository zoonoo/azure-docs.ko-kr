---
title: 문제 해결 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: iainfoulds
ms.author: iainfou
ms.date: 09/11/2018
ms.topic: article
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: a1c68f7e1d0a24be173137d3a7c920876cc8ba66
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515746"
---
# <a name="troubleshooting-guide"></a>문제 해결 가이드

이 가이드는 Azure Dev Spaces를 사용할 때 발생할 수 있는 일반적인 문제에 대한 정보를 포함합니다.

## <a name="enabling-detailed-logging"></a>자세한 로깅 사용

문제를 더 효과적으로 해결하기 위해 검토에 대한 더 자세한 로그를 만드는 게 유용할 수 있습니다.

Visual Studio 확장의 경우 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 환경 변수를 1로 설정합니다. 환경 변수가 적용되도록 Visual Studio를 다시 시작해야 합니다. 사용하도록 설정하면 자세한 로그가 사용자 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 디렉터리에 작성됩니다.

CLI에서 `--verbose` 전환을 사용하여 명령을 실행하는 동안 자세한 정보를 출력할 수 있습니다. `%TEMP%\Azure Dev Spaces`에서 자세한 로그를 찾아볼 수도 있습니다. Mac의 경우 터미널 창에서 `echo $TMPDIR`을 실행하면 TEMP 디렉터리를 찾을 수 있습니다. Linux 컴퓨터에서 TEMP 디렉터리는 일반적으로 `/tmp`입니다.

## <a name="debugging-services-with-multiple-instances"></a>여러 인스턴스를 사용하여 서비스 디버깅

현재 Azure Dev Spaces는 단일 인스턴스(Pod) 디버깅 시에 가장 효율적으로 작동합니다. azds.yaml 파일에는 서비스용으로 실행될 Pod 수를 나타내는 설정인 replicaCount가 포함되어 있습니다. 앱이 지정된 서비스용으로 여러 Pod를 실행하도록 구성하기 위해 replicaCount를 변경하면 디버거는 첫 번째 Pod(사전순으로 나열된 경우)에 연결합니다. 해당 Pod가 재순환하는 경우 디버거는 다른 Pod에 연결하므로 예기치 않은 동작이 발생할 수 있습니다.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>'Azure Dev Spaces 컨트롤러를 만들지 못했음' 오류

컨트롤러를 만들 때 무언가 잘못되면 이 오류가 표시됩니다. 일시적인 오류인 경우 컨트롤러를 삭제하고 다시 만들면 문제가 해결됩니다.

### <a name="try"></a>다음을 시도해 보세요.

컨트롤러를 삭제하려면 Azure Dev Spaces CLI를 사용합니다. Visual Studio 또는 Cloud Shell에서는 작업할 수 없습니다. AZDS CLI를 설치하려면 먼저 Azure CLI를 설치한 다음 이 명령을 실행합니다.

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

그런 다음 이 명령을 실행하여 컨트롤러를 삭제합니다.

```cmd
azds remove -g <resource group name> -n <cluster name>
```

CLI 또는 Visual Studio에서 컨트롤러를 다시 만들 수 있습니다. 처음 만드는 것처럼 이 자습서의 지침에 따릅니다.


## <a name="error-service-cannot-be-started"></a>'서비스를 시작할 수 없습니다.' 오류

서비스 코드를 시작하지 못하면 이 오류가 발생할 수 있습니다. 사용자 코드에 원인이 있는 경우가 많습니다. 자세한 진단 정보를 가져오려면 명령 및 설정을 다음과 같이 변경합니다.

### <a name="try"></a>다음을 시도해 보세요.

명령줄에서:

_azds.exe_를 사용하는 경우 --verbose 명령줄 옵션을 사용하고 --output 명령줄 옵션을 사용하여 출력 형식을 지정합니다.
 
```cmd
azds up --verbose --output json
```

Visual Studio에서

1. **도구 > 옵션**을 열고 **프로젝트 및 솔루션** 아래에서 **빌드 및 실행**을 선택합니다.
2. **MSBuild 프로젝트 빌드 출력 세부 정보 표시**의 설정을 **세부 내용** 또는 **진단**으로 변경합니다.

    ![도구 옵션 대화 상자 스크린샷](media/common/VerbositySetting.PNG)
    
다단계 Dockerfile을 사용하려고 할 때 이 오류가 표시될 수 있습니다. 자세한 출력은 다음과 같습니다.

```cmd
$ azds up
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

이는 AKS 노드가 다단계 빌드를 지원하지 않는 이전 버전의 Docker를 실행하기 때문입니다. 다단계 빌드를 방지하려면 Dockerfile을 다시 작성해야 합니다.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces 서비스에 연결된 공용 URL에 대한 DNS 이름 확인 실패

DNS 이름 확인이 실패하는 경우 Dev Spaces 서비스에 연결된 공용 URL에 연결을 시도하면 웹 브라우저에 "페이지를 표시할 수 없음" 또는 "이 사이트는 연결할 수 없음" 오류가 표시될 수 있습니다.

### <a name="try"></a>다음을 시도해 보세요.

다음 명령을 사용하여 Dev Spaces 서비스에 연결된 모든 URL을 나열할 수 있습니다.

```cmd
azds list-uris
```

URL이 *보류 중* 상태이면 Dev Spaces가 DNS 등록 완료를 대기 중임을 의미합니다. 경우에 따라 등록을 완료하려면 몇 분 정도 걸립니다. Dev Spaces는 각 서비스마다 localhost 터널도 엽니다. 이것을 DNS 등록을 대기하는 동안 사용할 수 있습니다.

URL이 5분 넘게 *보류 중* 상태로 있으면 공용 엔드포인트를 만드는 외부 DNS Pod 및/또는 공용 엔드포인트를 획득하는 nginx 수신 컨트롤러에 문제가 있는 것일 수 있습니다. 다음 명령을 사용하여 이러한 Pod를 삭제할 수 있습니다. 삭제된 Pod는 자동으로 다시 만들어집니다.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>'필수 도구 및 구성이 누락되었습니다.' 오류

VS Code를 시작할 때 다음 오류가 발생할 수 있습니다. "[Azure Dev Spaces] '[프로젝트 이름]'을 빌드하고 디버그할 필수 도구 및 구성이 누락되었습니다."
이 오류는 VS Code에서 볼 수 있듯이 PATH 환경 변수에 azds.exe가 없음을 의미합니다.

### <a name="try"></a>다음을 시도해 보세요.

PATH 환경 변수가 제대로 설정되어 있는 명령 프롬프트에서 VS Code를 시작합니다.

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>오류 'azds'는 내부 또는 외부 명령, 실행 가능한 프로그램 또는 일괄 처리 파일로 인식되지 않습니다.
 
azds.exe가 설치되지 않았거나 올바르게 구성되지 않은 경우 이 오류가 발생할 수 있습니다.

### <a name="try"></a>다음을 시도해 보세요.

1. azds.exe에 대한 위치인 %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI(미리 보기)를 확인합니다. 위치가 있는 경우 해당 위치를 PATH 환경 변수에 추가합니다.
2. azds.exe가 설치되어 있지 않으면 다음 명령을 실행합니다.

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>경고 ‘지원되지 않는 언어로 인해 Dockerfile을 생성할 수 없습니다’
Azure Dev Spaces는 C# 및 Node.js에 대해 네이티브 지원을 제공합니다. 이러한 언어 중 하나로 작성된 코드를 포함하는 디렉터리에서 *azds prep*를 실행할 때 Azure Dev Spaces는 자동으로 적절한 Dockerfile을 만듭니다.

다른 언어로 작성된 코드를 사용하여 Azure Dev Spaces를 계속 사용할 수 있지만, 처음으로 *azds up*을 실행하기 전에는 Dockerfile을 직접 만들어야 합니다.

### <a name="try"></a>다음을 시도해 보세요.
응용 프로그램이 Azure Dev Spaces가 고유하게 지원하지 않는 언어로 작성된 경우 코드를 실행하는 컨테이너 이미지를 빌드하기 위해 적절한 Dockerfile을 제공해야 합니다. Docker는 필요에 맞는 Dockerfile을 작성하는 데 활용할 수 있는 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/)와 함께 [Dockerfile 작성에 대한 모범 사례 목록](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)을 제공합니다.

적절한 Dockerfile을 만들고 나면 *azds up*을 계속 실행하여 Azure Dev Spaces에서 응용 프로그램을 실행할 수 있습니다.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>오류 '업스트림 연결 오류 또는 헤더 전에 연결 끊김/다시 설정'
서비스에 액세스하려고 할 때 이 오류가 표시될 수 있습니다. 예를 들어 브라우저에서 서비스의 URL로 이동하는 경우입니다. 

### <a name="reason"></a>이유 
컨테이너 포트를 사용할 수 없습니다. 이 문제는 다음과 같은 이유 때문에 발생할 수 있습니다. 
* 컨테이너가 아직 빌드 및 배포되는 중입니다. `azds up`을 시작하거나 디버거를 시작한 다음, 성공적으로 배포하기 전에 컨테이너에 액세스하려고 하는 경우 이 문제가 발생할 수 있습니다.
* 포트 구성이 _Dockerfile_, Helm 차트 및 포트를 여는 모든 서버 코드에서 일치하지 않습니다.

### <a name="try"></a>다음을 시도해 보세요.
1. 컨테이너가 빌드/배포되는 중인 경우 2-3초 기다렸다가 서비스에 액세스를 다시 시도할 수 있습니다. 
1. 포트 구성을 확인합니다. 지정된 포트 번호는 아래 모든 자산에서 **동일**해야 합니다.
    * **Dockerfile:** `EXPOSE` 명령으로 지정됩니다.
    * **[Helm 차트](https://docs.helm.sh):** 서비스에 대해 `externalPort` 및 `internalPort` 값으로 지정됩니다(종종 `values.yml` 파일에 위치함).
    * 응용 프로그램 코드에서 열리는 모든 포트(예: Node.js에서 `var server = app.listen(80, function () {...}`)


## <a name="config-file-not-found"></a>구성 파일을 찾을 수 없음
`azds up`을 실행하고 다음 오류가 표시됩니다. `Config file not found: .../azds.yaml`

### <a name="reason"></a>이유
실행하려는 코드의 루트 디렉터리에서 `azds up`을 실행하고, Azure Dev Spaces로 실행하도록 코드 폴더를 초기화해야 합니다.

### <a name="try"></a>다음을 시도해 보세요.
1. 현재 디렉터리를 서비스 코드를 포함하는 루트 폴더로 변경합니다. 
1. 코드 폴더에 _azds.yaml_ 파일이 없는 경우 `azds prep`를 실행하여 Docker, Kubernetes 및 Azure Dev Spaces 자산을 생성합니다.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>오류: '파이프 프로그램 'azds'가 코드 126으로 예기치 않게 종료되었습니다.'
VS Code 디버거를 시작하면 때때로 이 오류가 발생할 수 있습니다.

### <a name="try"></a>다음을 시도해 보세요.
1. VS Code를 닫았다가 다시 엽니다.
2. F5 키를 다시 누릅니다.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>디버깅 오류 '다음 유형에 대한 디버거 확장을 찾지 못함: coreclr'
VS Code 디버거를 실행하면 오류를 보고합니다. `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>이유
개발 컴퓨터에 C#용 VS Code 확장이 설치되어 있지 않습니다. C# 확장에는 .Net Core(CoreCLR)용 디버깅 지원이 포함되어 있습니다.

### <a name="try"></a>다음을 시도해 보세요.
[C#용 VS Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)을 설치합니다.

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>디버깅 오류 '구성된 디버그 형식 'coreclr'은 지원되지 않습니다.'
VS Code 디버거를 실행하면 오류를 보고합니다. `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>이유
개발 컴퓨터에 설치된 Azure Dev Spaces용 VS Code 확장이 없습니다.

### <a name="try"></a>다음을 시도해 보세요.
[Azure Dev Spaces용 VS Code 확장](get-started-netcore.md)을 설치합니다.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>형식 또는 네임스페이스 이름 'MyLibrary'를 찾을 수 없음

### <a name="reason"></a>이유 
빌드 컨텍스트는 기본적으로 프로젝트/서비스 수준에 있으므로 사용하는 라이브러리 프로젝트를 찾을 수 없습니다.

### <a name="try"></a>다음을 시도해 보세요.
수행해야 하는 작업:
1. 빌드 컨텍스트를 솔루션 수준으로 설정하도록 _azds.yaml_ 파일을 수정합니다.
2. 새 빌드 컨텍스트를 기준으로 올바르게 프로젝트 파일(_.csproj_)을 참조하도록 _Dockerfile_ 및 _Dockerfile.develop_ 파일을 수정합니다.
3. .sln 파일 옆에 _.dockerignore_ 파일을 배치하고 필요에 따라 수정합니다.

https://github.com/sgreenmsft/buildcontextsample에서 예제를 찾을 수 있습니다.

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' 권한 부여 오류
Azure Dev Space를 관리하고 소유자 또는 기여자 액세스 권한이 없는 Azure 구독에서 작업하는 경우 다음과 같은 오류가 표시될 수 있습니다.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>이유
선택한 Azure 구독에서 `Microsoft.DevSpaces` 네임스페이스를 등록하지 않았습니다.

### <a name="try"></a>다음을 시도해 보세요.
Azure 구독에 대한 소유자 또는 기여자 액세스 권한이 있는 사용자는 다음 Azure CLI 명령을 실행하여 `Microsoft.DevSpaces` 네임스페이스를 수동으로 등록할 수 있습니다.

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Dev Spaces 시작 시 “오류: could not find a ready tiller pod(준비된 Tiller Pod를 찾을 수 없음)”

### <a name="reason"></a>이유
이 오류는 Helm 클라이언트가 클러스터에서 실행 중인 Tiller Pod에 더 이상 연결할 수 없는 경우에 발생합니다.

### <a name="try"></a>다음을 시도해 보세요.
클러스터에서 에이전트 노드를 다시 시작하면 대체로 이 문제가 해결됩니다.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev Spaces 프록시는 개발 공간에서 실행되는 다른 Pod를 방해할 수 있습니다.

### <a name="reason"></a>이유
AKS 클러스터의 네임스페이스에서 Dev Spaces를 활성화하면 _mindaro-proxy_라는 추가 컨테이너가 해당 네임스페이스 내에서 실행되는 각 Pod에 설치됩니다. 이 컨테이너는 Dev Spaces의 팀 개발 기능에 필수적인 Pod의 서비스에 대한 호출을 차단합니다.

그러나 해당 Pod에서 실행되는 특정 서비스를 방해할 수 있습니다. 특히 마스터/슬레이브 통신에서 연결 오류 및 실패를 일으키는 Redis cache에서 실행되는 Pod를 방해합니다.

### <a name="try"></a>다음을 시도해 보세요.
Dev Spaces가 활성화된 네임스페이스 내에서 애플리케이션의 나머지 부분을 계속해서 실행하는 동안 영향을 받는 Pod를 활성화된 Dev Spaces가 _없는_ 클러스터 내의 네임스페이스로 이동할 수 있습니다. Dev Spaces는 비 Dev Spaces 사용 네임스페이스 내에 _mindaro-proxy_ 컨테이너를 설치하지 않습니다.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces에서 컨테이너를 빌드하는 데 내 기존 Dockerfile을 사용하지 않는 것처럼 보임 

### <a name="reason"></a>이유
프로젝트에서 특정 _Dockerfile_을 가리키도록 Azure Dev Spaces를 구성할 수 있습니다. Azure Dev Spaces에서 컨테이너를 빌드하는 데 예상하는 _Dockerfile_를 사용하지 않는 경우 Azure Dev Spaces에 명시적으로 위치를 알려야 할 수 있습니다. 

### <a name="try"></a>다음을 시도해 보세요.
프로젝트에서 Azure Dev Spaces가 생성한 _azds.yaml_ 파일을 엽니다. `configurations->develop->build->dockerfile` 지시문을 사용하여 사용하려는 Dockerfile을 가리킵니다.

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
