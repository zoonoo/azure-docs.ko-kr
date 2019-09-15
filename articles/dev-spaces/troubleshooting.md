---
title: 문제 해결
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: b16a7d874f15747c14df1d728be824fac76de2be
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993956"
---
# <a name="troubleshooting-guide"></a>문제 해결 가이드

이 가이드는 Azure Dev Spaces를 사용할 때 발생할 수 있는 일반적인 문제에 대한 정보를 포함합니다.

Azure Dev Spaces를 사용 하는 동안 문제가 발생 하는 경우 [Azure Dev Spaces GitHub 리포지토리에서 문제](https://github.com/Azure/dev-spaces/issues)를 만듭니다.

## <a name="enabling-detailed-logging"></a>자세한 로깅 사용

자세한 검토 로그를 만들면 더 효과적으로 문제를 해결하는 데 도움이 될 수 있습니다.

Visual Studio 확장의 경우 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 환경 변수를 1로 설정합니다. 환경 변수가 적용되도록 Visual Studio를 다시 시작해야 합니다. 이와 같이 환경 변수가 사용되면 자세한 로그가 사용자 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 디렉터리에 작성됩니다.

CLI에서 `--verbose` 전환을 사용하여 명령을 실행하는 동안 자세한 정보를 출력할 수 있습니다. `%TEMP%\Azure Dev Spaces`에서 자세한 로그를 찾아볼 수도 있습니다. Mac의 경우 터미널 창에서 `echo $TMPDIR`을 실행하면 TEMP 디렉터리를 찾을 수 있습니다. Linux 컴퓨터에서 TEMP 디렉터리는 일반적으로 `/tmp`입니다.

## <a name="debugging-services-with-multiple-instances"></a>여러 인스턴스를 사용하여 서비스 디버깅

현재 Azure Dev Spaces는 단일 인스턴스 또는 Pod를 디버그할 때 가장 원활하게 작동합니다. azds.yaml 파일에는 Kubernetes가 서비스용으로 실행하는 Pod 수를 나타내는 설정인 *replicaCount*가 포함되어 있습니다. 지정된 서비스용으로 여러 개의 Pod를 실행하도록 앱을 구성하기 위해 replicaCount를 변경하면 디버거는 첫 번째 Pod(사전순으로 나열된 경우)에 연결됩니다. 원래 Pod가 재순환될 때는 디버거가 다른 Pod에 연결되므로 예기치 않은 동작이 발생할 수 있습니다.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>'Azure Dev Spaces 컨트롤러를 만들지 못했음' 오류

### <a name="reason"></a>이유
컨트롤러를 만들 때 무언가 잘못되면 이 오류가 표시됩니다. 일시적인 오류인 경우 컨트롤러를 삭제하고 다시 만들면 문제가 해결됩니다.

### <a name="try"></a>시도해 보기

컨트롤러를 삭제 합니다.

```bash
azds remove -g <resource group name> -n <cluster name>
```

컨트롤러를 삭제 하려면 Azure Dev Spaces CLI를 사용 해야 합니다. Visual Studio에서 컨트롤러를 삭제할 수 없습니다. 또한 Azure Cloud Shell에 Azure Dev Spaces CLI를 설치할 수 없으므로 Azure Cloud Shell에서 컨트롤러를 삭제할 수 없습니다.

Azure Dev Spaces CLI를 설치 하지 않은 경우 먼저 다음 명령을 사용 하 여 설치 하 고 컨트롤러를 삭제할 수 있습니다.

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

CLI 또는 Visual Studio에서 컨트롤러를 다시 만들 수 있습니다. 예는 [팀 개발](quickstart-team-development.md) 또는 [.net Core 빠른 시작을 사용 하 여 개발](quickstart-netcore-visualstudio.md) 을 참조 하세요.

## <a name="error-service-cannot-be-started"></a>'서비스를 시작할 수 없습니다.' 오류

서비스 코드를 시작하지 못하면 이 오류가 발생할 수 있습니다. 사용자 코드에 원인이 있는 경우가 많습니다. 자세한 진단 정보를 가져오려면 명령 및 설정을 다음과 같이 변경합니다.

### <a name="try"></a>다음을 시도해 보세요.

명령줄에서:

_azds.exe_를 사용하는 경우 --verbose 명령줄 옵션을 사용하고 --output 명령줄 옵션을 사용하여 출력 형식을 지정합니다.
 
```cmd
azds up --verbose --output json
```

Visual Studio에서 다음을 수행합니다.

1. **도구 > 옵션**을 열고 **프로젝트 및 솔루션** 아래에서 **빌드 및 실행**을 선택합니다.
2. **MSBuild 프로젝트 빌드 출력 세부 정보 표시**의 설정을 **세부 내용** 또는 **진단**으로 변경합니다.

    ![도구 옵션 대화 상자 스크린샷](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>다단계 Dockerfile:
다단계 Dockerfile을 사용하는 경우 ‘서비스를 시작할 수 없습니다.’라는 오류가 표시됩니다. 이 경우 자세한 정보 출력에 다음 텍스트가 포함됩니다.

```cmd
$ azds up -v
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

이 오류는 AKS 노드가 다단계 빌드를 지원하지 않는 이전 버전의 Docker를 실행하기 때문에 발생합니다. 다단계 빌드를 방지하려면 Dockerfile을 다시 작성합니다.

### <a name="rerunning-a-service-after-controller-re-creation"></a>컨트롤러를 다시 만든 후 서비스 다시 실행
이 클러스터와 연결된 Azure Dev Spaces 컨트롤러를 제거했다가 다시 만든 후 서비스를 다시 실행하려고 하면 ‘서비스를 시작할 수 없습니다.’라는 오류가 표시됩니다. 이 경우 자세한 정보 출력에 다음 텍스트가 포함됩니다.

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

이 오류는 Dev Spaces 컨트롤러를 제거해도 해당 컨트롤러에서 이전에 설치한 서비스는 제거되지 않기 때문에 발생합니다. 이전 서비스가 그대로 있기 때문에 컨트롤러를 다시 만든 후, 새 컨트롤러를 사용해서 서비스를 실행하려고 하면 실패합니다.

이 문제를 해결하려면 `kubectl delete` 명령을 사용하여 클러스터에서 이전 서비스를 수동으로 제거하고 Dev Spaces를 다시 실행하여 새 서비스를 설치합니다.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces 서비스에 연결된 공용 URL에 대한 DNS 이름 확인 실패

`azds prep` 명령에 `--public` 스위치를 지정하거나 Visual Studio에서 `Publicly Accessible` 확인란을 선택하여 서비스에 대해 공용 URL 엔드포인트를 구성할 수 있습니다. Dev Spaces에서 서비스를 실행하면 공용 DNS 이름이 자동으로 등록됩니다. 이 DNS 이름이 등록되지 않은 경우 공용 URL에 연결할 때 웹 브라우저에 ‘페이지를 표시할 수 없습니다.’ 또는 ‘사이트에 연결할 수 없습니다.’라는 오류가 표시됩니다.

### <a name="try"></a>다음을 시도해 보세요.

다음 명령을 사용하여 Dev Spaces 서비스에 연결된 모든 URL을 나열할 수 있습니다.

```cmd
azds list-uris
```

URL이 *보류 중* 상태이면 Dev Spaces가 DNS 등록 완료를 대기 중임을 의미합니다. 경우에 따라 등록을 완료하려면 몇 분 정도 걸립니다. Dev Spaces는 각 서비스마다 localhost 터널도 엽니다. 이것을 DNS 등록을 대기하는 동안 사용할 수 있습니다.

URL이 5분 넘게 ‘보류 중’ 상태로 있으면 공용 엔드포인트를 만드는 외부 DNS Pod 또는 공용 엔드포인트를 획득하는 nginx 수신 컨트롤러에 문제가 있는 것일 수 있습니다. 다음 명령을 사용하여 이러한 Pod를 삭제할 수 있습니다. AKS가 삭제된 포드를 자동으로 다시 만듭니다.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>'필수 도구 및 구성이 누락되었습니다.' 오류

VS Code를 시작할 때 다음 오류가 발생할 수 있습니다. "[Azure Dev Spaces] '[프로젝트 이름]'을 빌드하고 디버그할 필수 도구 및 구성이 누락되었습니다."
이 오류는 VS Code에서 볼 수 있듯이 PATH 환경 변수에 azds.exe가 없음을 의미합니다.

### <a name="try"></a>다음을 시도해 보세요.

PATH 환경 변수가 제대로 설정되어 있는 명령 프롬프트에서 VS Code를 시작합니다.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>"'projectname' 빌드 및 디버그에 필요한 도구가 최신 상태가 아닙니다." 오류가 표시됩니다.

Azure Dev Spaces용 VS Code 확장은 최신 버전이지만 Azure Dev Spaces CLI가 이전 버전인 경우 Visual Studio Code에 이 오류가 표시됩니다.

### <a name="try"></a>시도해 보기

최신 버전의 Azure Dev Spaces CLI를 다운로드한 후 설치합니다.

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>오류 'azds'는 내부 또는 외부 명령, 실행 가능한 프로그램 또는 일괄 처리 파일로 인식되지 않습니다.
 
azds.exe가 설치되지 않았거나 올바르게 구성되지 않은 경우 이 오류가 발생할 수 있습니다.

### <a name="try"></a>다음을 시도해 보세요.

1. % ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI for azds를 확인 합니다. 위치가 있는 경우 해당 위치를 PATH 환경 변수에 추가합니다.
2. azds.exe가 설치되어 있지 않으면 다음 명령을 실행합니다.

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>경고 ‘지원되지 않는 언어로 인해 Dockerfile을 생성할 수 없습니다’
Azure Dev Spaces는 C# 및 Node.js에 대해 네이티브 지원을 제공합니다. 이러한 언어 중 하나로 작성된 코드를 포함하는 디렉터리에서 *azds prep*를 실행할 때 Azure Dev Spaces는 자동으로 적절한 Dockerfile을 만듭니다.

다른 언어로 작성된 코드를 통해 Azure Dev Spaces를 사용할 수도 있지만, *azds up*을 처음 실행하기 전에 Dockerfile을 수동으로 만들어야 합니다.

### <a name="try"></a>다음을 시도해 보세요.
Azure Dev Spaces에서 기본적으로 지원하지 않는 언어로 애플리케이션이 작성된 경우 코드를 실행하는 컨테이너 이미지를 빌드하기 위해 적절한 Dockerfile을 제공해야 합니다. Docker는 요구에 맞는 Dockerfile을 작성하는 데 도움이 되는 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/) 및 [Dockerfile 작성 모범 사례 목록](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)을 제공합니다.

적절한 Dockerfile을 만들고 나면 *azds up*을 계속 실행하여 Azure Dev Spaces에서 애플리케이션을 실행할 수 있습니다.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>오류 '업스트림 연결 오류 또는 헤더 전에 연결 끊김/다시 설정'
서비스에 액세스하려고 할 때 이 오류가 표시될 수 있습니다. 예를 들어 브라우저에서 서비스의 URL로 이동하는 경우입니다. 

### <a name="reason"></a>이유 
컨테이너 포트를 사용할 수 없습니다. 이 문제는 다음과 같은 이유 때문에 발생할 수 있습니다. 
* 컨테이너가 아직 빌드 및 배포되는 중입니다. `azds up`을 시작하거나 디버거를 시작한 다음, 성공적으로 배포하기 전에 컨테이너에 액세스하려고 하는 경우 이 문제가 발생할 수 있습니다.
* 포트 구성이 _Dockerfile_, Helm 차트 및 포트를 여는 모든 서버 코드에서 일치하지 않습니다.

### <a name="try"></a>다음을 시도해 보세요.
1. 컨테이너가 빌드/배포되는 중인 경우 2-3초 기다렸다가 서비스에 액세스를 다시 시도할 수 있습니다. 
1. 포트 구성을 확인합니다. 지정된 포트 번호는 다음과 같은 모든 자산에서 **동일**해야 합니다.
    * **Dockerfile:** `EXPOSE` 명령으로 지정됩니다.
    * **[Helm 차트](https://docs.helm.sh):** 서비스에 대해 `externalPort` 및 `internalPort` 값으로 지정됩니다(종종 `values.yml` 파일에 위치함).
    * 애플리케이션 코드에서 열리는 모든 포트(예: Node.js에서 `var server = app.listen(80, function () {...}`)


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
개발 컴퓨터에 C#용 VS Code 확장이 설치되어 있지 않습니다. 확장 C# 에는 CoreCLR (.net Core)에 대 한 디버깅 지원이 포함 됩니다.

### <a name="try"></a>다음을 시도해 보세요.
[C#용 VS Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)을 설치합니다.

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>디버깅 오류 '구성된 디버그 형식 'coreclr'은 지원되지 않습니다.'
VS Code 디버거를 실행하면 오류를 보고합니다. `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>이유
개발 컴퓨터에 설치된 Azure Dev Spaces용 VS Code 확장이 없습니다.

### <a name="try"></a>다음을 시도해 보세요.
[Azure Dev Spaces용 VS Code 확장](get-started-netcore.md)을 설치합니다.

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>디버깅 오류 "'cwd' 값 '/src'가 잘못되었습니다. 시스템은 지정된 파일을 찾을 수 없습니다." 또는 "launch: program '/src/[프로젝트 이진 경로]'이(가) 존재하지 않습니다."가 발생합니다.
VS Code 디버거를 실행하면 오류 `Invalid 'cwd' value '/src'. The system cannot find the file specified.` 및/또는 `launch: program '/src/[path to project executable]' does not exist`를 보고합니다.

### <a name="reason"></a>이유
기본적으로 VS Code 확장은 컨테이너에 대한 프로젝트의 작업 디렉터리로 여 `src`를 사용합니다. 다른 작업 디렉터리를 지정하도록 `Dockerfile`을 업데이트한 경우에 다음 오류가 표시될 수 있습니다.

### <a name="try"></a>다음을 시도해 보세요.
프로젝트 폴더의 `.vscode` 하위 디렉터리에서 `launch.json` 파일을 업데이트합니다. 프로젝트의 `Dockerfile`에 정의된 `WORKDIR`과 동일한 디렉터리를 가리키도록 `configurations->cwd` 지시문을 변경합니다. `configurations->program` 지시문도 업데이트해야 할 수 있습니다.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>형식 또는 네임스페이스 이름 'MyLibrary'를 찾을 수 없음

### <a name="reason"></a>이유 
빌드 컨텍스트는 기본적으로 프로젝트/서비스 수준에 있으므로 사용 중인 라이브러리 프로젝트를 찾을 수 없습니다.

### <a name="try"></a>다음을 시도해 보세요.
수행해야 하는 작업:
1. 빌드 컨텍스트를 솔루션 수준으로 설정하도록 _azds.yaml_ 파일을 수정합니다.
2. 새 빌드 컨텍스트를 기준으로 올바르게 프로젝트 파일( _.csproj_)을 참조하도록 _Dockerfile_ 및 _Dockerfile.develop_ 파일을 수정합니다.
3. .sln 파일 옆에 _.dockerignore_ 파일을 배치하고 필요에 따라 수정합니다.

[https://github.com/sgreenmsft/buildcontextsample](https://github.com/sgreenmsft/buildcontextsample ) 에서 예제를 찾을 수 있습니다.

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' 권한 부여 오류
Azure Dev Spaces를 관리하려면 Azure 구독에서 ‘owner’ 또는 ‘contributor’ 액세스 권한이 있어야 합니다. Dev Spaces를 관리하려고 하는데 관련 Azure 구독에 대한 ‘owner’ 또는 ‘contributor’ 액세스 권한이 없는 경우 이 오류가 표시될 수 있습니다.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>이유
선택한 Azure 구독에서 `Microsoft.DevSpaces` 네임스페이스를 등록하지 않았습니다.

### <a name="try"></a>다음을 시도해 보세요.
Azure 구독에 대한 소유자 또는 기여자 액세스 권한이 있는 사용자는 다음 Azure CLI 명령을 실행하여 `Microsoft.DevSpaces` 네임스페이스를 수동으로 등록할 수 있습니다.

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>AKS 가상 노드에 대한 ‘컨테이너 이미지 빌드 대기 중...’ 단계에서 Dev Spaces가 시간 초과됩니다.

### <a name="reason"></a>이유
이 시간 제한은 [AKS 가상 노드에서](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)실행 하도록 구성 된 서비스를 실행 하기 위해 Dev Spaces를 사용 하려고 할 때 발생 합니다. Dev Spaces는 현재 가상 노드에서 빌드 또는 디버깅 서비스를 지원하지 않습니다.

`--verbose` 스위치를 사용하여 `azds up`을 실행하거나 Visual Studio에서 자세한 정보 로깅을 사용하도록 설정하면 추가 정보가 표시됩니다.

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

위의 명령은 서비스의 pod가 가상 노드인 *가상 노드 aci-linux*에 할당 되었음을 보여 줍니다.

### <a name="try"></a>다음을 시도해 보세요.
서비스에 대한 Helm 차트를 업데이트하여 가상 노드에서 서비스를 실행할 수 있도록 하는 *nodeSelector* 및/또는 *tolerations* 값을 제거합니다. 이러한 값은 일반적으로 차트의 `values.yaml` 파일에서 정의됩니다.

Dev Spaces를 통해 빌드/디버그하려는 서비스가 VM 노드에서 실행되는 경우에는 가상 노드 기능이 사용되는 AKS 클러스터를 계속 사용할 수 있습니다. 이 값이 기본 구성입니다.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Dev Spaces 시작 시 “오류: could not find a ready tiller pod(준비된 Tiller Pod를 찾을 수 없음)”

### <a name="reason"></a>이유
이 오류는 Helm 클라이언트가 클러스터에서 실행 중인 Tiller Pod에 더 이상 연결할 수 없는 경우에 발생합니다.

### <a name="try"></a>다음을 시도해 보세요.
클러스터에서 에이전트 노드를 다시 시작하면 대체로 이 문제가 해결됩니다.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>"오류: release azds-\<identifier\>-\<spacename\>servicenamefailed\> : services 'servicename\<-\<\>' 이미 있음 ' 또는 "servicename \<\>에 대 한 끌어오기 액세스가 거부 되었습니다. 리포지토리가 존재 하지 않거나 ' docker 로그인 ' '가 필요할 수 있습니다.

### <a name="reason"></a>이유
이러한 오류는 같은 개발 공간 `helm install`내에서, 또는와 같은 dev Spaces 명령 (예: `azds down` `azds up` , `helm upgrade`또는 `helm delete`)을 함께 실행 하는 경우 발생할 수 있습니다. 개발자 공간에는 동일한 개발 공간에서 실행 되는 고유한 Tiller 인스턴스와 충돌 하는 자체 Tiller 인스턴스가 있기 때문에 발생 합니다.

### <a name="try"></a>다음을 시도해 보세요.
동일한 AKS 클러스터에 대해 투구 명령과 Dev Spaces 명령을 모두 사용 하는 것이 좋지만 각 개발 공간 사용 네임 스페이스는 하나 또는 다른를 사용 해야 합니다.

예를 들어, 투구 명령을 사용 하 여 부모 개발 공간에서 전체 응용 프로그램을 실행 한다고 가정 합니다. 해당 부모에서 자식 dev 공간을 만들고 Dev 공간을 사용 하 여 자식 dev 공간 내에서 개별 서비스를 실행 하 고 서비스를 함께 테스트할 수 있습니다. 변경 내용을 체크 인할 준비가 되 면 투구 명령을 사용 하 여 업데이트 된 코드를 부모 개발 공간에 배포 합니다. 처음에 `azds up` 는 투구를 사용 하 여 실행 되는 서비스와 충돌 하므로 부모 dev 공간에서 업데이트 된 서비스를 실행 하는 데를 사용 하지 마세요.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev Spaces 프록시는 개발 공간에서 실행되는 다른 Pod를 방해할 수 있습니다.

### <a name="reason"></a>이유
AKS 클러스터의 네임스페이스에서 Dev Spaces를 활성화하면 _mindaro-proxy_라는 추가 컨테이너가 해당 네임스페이스 내에서 실행되는 각 Pod에 설치됩니다. 이 컨테이너는 Pod의 서비스에 대한 호출을 가로채며, Dev Spaces의 팀 개발 기능에 필수적입니다. 그러나 이러한 Pod에서 실행 중인 특정 서비스를 방해할 수 있습니다. Redis에 대해 Azure Cache를 실행 하는 pod를 방해 하 여 기본/보조 통신에서 연결 오류 및 실패를 유발 하는 것으로 알려져 있습니다.

### <a name="try"></a>다음을 시도해 보세요.
영향을 받는 Pod를 Dev Spaces가 ‘사용되지 않는’ 클러스터 내의 네임스페이스로 이동할 수 있습니다. 애플리케이션의 나머지 부분은 Dev Spaces 사용 네임스페이스 내에서 계속 실행할 수 있습니다. Dev Spaces는 비 Dev Spaces 사용 네임스페이스 내에 _mindaro-proxy_ 컨테이너를 설치하지 않습니다.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces에서 컨테이너를 빌드하는 데 내 기존 Dockerfile을 사용하지 않는 것처럼 보임

### <a name="reason"></a>이유
프로젝트에서 특정 _Dockerfile_을 가리키도록 Azure Dev Spaces를 구성할 수 있습니다. Azure Dev Spaces가 컨테이너를 빌드하는 데 필요한 _Dockerfile_을 사용하지 않는 것 같으면 Azure Dev Spaces에서 사용할 Dockerfile을 명시적으로 지정해야 할 수 있습니다. 

### <a name="try"></a>다음을 시도해 보세요.
Azure Dev Spaces가 사용자 프로젝트에 생성한 _azds.yaml_ 파일을 엽니다. *configurations->develop->build->dockerfile* 지시문을 사용하여 사용할 Dockerfile을 가리킵니다.

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Node.js 응용 프로그램에 디버깅을 연결 하는 경우 "내부 조사식 실패: watch ENOSPC" 오류

### <a name="reason"></a>이유

디버거를 사용 하 여 연결 하려고 하는 node.js 응용 프로그램을 사용 하 여 pod를 실행 하는 노드가 *inotify* 값을 초과 했습니다. 경우에 따라 [ *inotify* 의 기본값은 디버거를 pod에 직접 연결 하는 것을 처리 하기에는 너무 작을 수 있습니다](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>시도해 보기
이 문제에 대 한 임시 해결 방법은 클러스터의 각 노드에서 *inotify* 의 값을 늘리고 해당 노드를 다시 시작 하 여 변경 내용을 적용 하는 것입니다.

## <a name="new-pods-are-not-starting"></a>새 pod을 시작 하지 않습니다.

### <a name="reason"></a>이유

Kubernetes 이니셜라이저는 클러스터의 *클러스터 관리자* 역할에 대 한 RBAC 권한 변경으로 인해 new Pod에 PodSpec을 적용할 수 없습니다. 또한 새 pod에 잘못 된 PodSpec 있을 수 있습니다. 예를 들어 pod와 연결 된 서비스 계정이 더 이상 존재 하지 않습니다. 이니셜라이저 문제로 인해 *보류* 상태에 있는 pod을 보려면 다음 `kubectl get pods` 명령을 사용 합니다.

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

이 문제는 Azure Dev Spaces 사용 하도록 설정 되지 않은 네임 스페이스를 포함 하 여 클러스터의 *모든 네임 스페이스* 에 pod 영향을 줄 수 있습니다.

### <a name="try"></a>시도해 보기

[Dev SPACES CLI를 최신 버전으로 업데이트](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) 한 다음 Azure Dev Spaces 컨트롤러에서 *Azds initializerconfiguration* 을 삭제 합니다.

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Azure Dev Spaces 컨트롤러에서 *azds initializerconfiguration* 을 제거한 후을 사용 `kubectl delete` 하 여 *보류 중* 상태의 pod를 제거 합니다. 보류 중인 모든 pod을 제거한 후 pod를 다시 배포 합니다.

다시 배포 후 새 pod *보류 중* 상태로 유지 되는 경우를 사용 `kubectl delete` 하 여 *보류 중* 상태에서 pod를 제거 합니다. 보류 중인 모든 pod 제거 된 후 클러스터에서 컨트롤러를 삭제 하 고 다시 설치 합니다.

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

컨트롤러를 다시 설치한 후 pod를 다시 배포 합니다.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Dev Spaces 컨트롤러 및 Api 호출에 대 한 RBAC 권한이 잘못 되었습니다.

### <a name="reason"></a>이유
Azure Dev Spaces 컨트롤러에 액세스 하는 사용자는 AKS 클러스터에서 admin *kubeconfig* 를 읽을 수 있는 권한이 있어야 합니다. 예를 들어이 권한은 [기본 제공 Azure Kubernetes Service 클러스터 관리자 역할](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)에서 사용할 수 있습니다. 또한 Azure Dev Spaces 컨트롤러에 액세스 하는 사용자에 게는 컨트롤러에 대 한 *참가자* 또는 *소유자* RBAC 역할이 있어야 합니다.

### <a name="try"></a>시도해 보기
AKS 클러스터에 대 한 사용자 권한 업데이트에 대 한 자세한 내용은 [여기](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)에서 제공 됩니다.

컨트롤러에 대 한 사용자의 RBAC 역할을 업데이트 하려면:

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
1. 일반적으로 AKS 클러스터와 동일한 컨트롤러를 포함 하는 리소스 그룹으로 이동 합니다.
1. *숨겨진 형식 표시* 확인란을 사용 하도록 설정 합니다.
1. 컨트롤러를 클릭 합니다.
1. *Access Control (IAM)* 창을 엽니다.
1. *역할 할당* 탭을 클릭 합니다.
1. *추가* 를 클릭 하 고 *역할 할당 추가*를 클릭 합니다.
    * *역할* 에 대해 *참가자* 또는 *소유자*를 선택 합니다.
    * *액세스 권한을 할당 하 여* *Azure AD 사용자, 그룹 또는 서비스 주체*를 선택 합니다.
    * 에서 사용 권한을 부여 하려는 사용자에 대해 검색을 *선택* 합니다.
1. *Save*을 클릭합니다.

## <a name="controller-create-failing-due-to-controller-name-length"></a>컨트롤러 이름 길이 때문에 컨트롤러를 만들지 못했습니다.

### <a name="reason"></a>이유
Azure Dev Spaces 컨트롤러 이름은 31 자 보다 길 수 없습니다. AKS 클러스터에서 개발 공간을 사용 하도록 설정 하거나 컨트롤러를 만들 때 컨트롤러의 이름이 31 자를 초과 하는 경우 다음과 같은 오류가 표시 됩니다.

*클러스터 ' a-컨트롤러-이름-aks-미국-미국 '에 대해 Dev 공간 컨트롤러를 만들지 못했습니다. Azure Dev Spaces 컨트롤러 이름 ' a-컨트롤러-이름-매우 긴-aks-미국 '이 잘못 되었습니다. 위반 된 제약 조건: Azure Dev Spaces 컨트롤러 이름은 최대 31 자 까지만 사용할 수 있습니다.*

### <a name="try"></a>시도해 보기

대체 이름으로 컨트롤러를 만듭니다.

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>AKS 클러스터에 Windows 노드 풀이 추가 될 때 Dev 공간 실패

### <a name="reason"></a>이유
현재 Azure Dev Spaces는 Linux pod 및 노드에서만 실행 하기 위한 것입니다. Windows 노드 풀을 포함 하는 AKS 클러스터가 있는 경우 Azure Dev Spaces pod이 Linux 노드에서만 예약 되어 있는지 확인 해야 합니다. Azure Dev Spaces pod가 Windows 노드에서 실행 되도록 예약 된 경우 해당 pod가 시작 되지 않고 개발 공간을 사용할 수 없게 됩니다.

### <a name="try"></a>시도해 보기
AKS 클러스터에 [taint를 추가](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) 하 여 Linux pod Windows 노드에서 실행 되도록 예약 되지 않았는지 확인 합니다.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>오류 "클러스터의 준비 상태에서 untainted Linux 노드를 찾을 수 없습니다. ' Azds ' 네임 스페이스에 pod을 배포 하려면 준비 상태에 untainted Linux 노드가 하나 이상 있어야 합니다. "

### <a name="reason"></a>이유

AKS 클러스터에서 untainted 노드를 찾을 수 없어서 *준비* 상태에서 pod를 예약할 수 없습니다. Azure Dev Spaces Azure Dev Spaces에는 tolerations를 지정 하지 않고 pod 예약을 허용 하는 하나 이상의 Linux 노드가 *준비* 상태 여야 합니다.

### <a name="try"></a>시도해 보기
AKS 클러스터에서 [taint 구성을 업데이트](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) 하 여 하나 이상의 Linux 노드에서 tolerations를 지정 하지 않고 pod 예약을 허용 하는지 확인 합니다. 또한 tolerations를 지정 하지 않고 pod 예약을 허용 하는 하나 이상의 Linux 노드가 *준비* 상태 인지 확인 합니다. 노드가 *준비* 상태에 도달 하는 데 오랜 시간이 걸리는 경우 노드를 다시 시작 해 볼 수 있습니다.

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>실행 시 "Azure Dev Spaces CLI가 제대로 설치 되지 않았습니다." 오류가 발생 합니다.`az aks use-dev-spaces`

### <a name="reason"></a>이유
Azure Dev Spaces CLI 업데이트가 설치 경로를 변경 했습니다. 2\.0.63 이전 버전의 Azure CLI을 사용 하는 경우이 오류가 표시 될 수 있습니다. Azure CLI 버전을 표시 하려면를 사용 `az --version`합니다.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

2\.0.63 이전 버전의 Azure CLI를 `az aks use-dev-spaces` 실행 하는 경우 오류 메시지에도 불구 하 고 설치에 성공 합니다. 아무런 문제 없이 계속 사용할 `azds` 수 있습니다.

### <a name="try"></a>시도해 보기
[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 설치를 2.0.63 이상으로 업데이트 합니다. 이렇게 하면를 실행할 `az aks use-dev-spaces`때 표시 되는 오류 메시지가 해결 됩니다. 또는 현재 버전의 Azure CLI 및 Azure Dev Spaces CLI를 계속 사용할 수 있습니다.


## <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>개발 공간에서 작업 하지 않는 수평 pod 자동 크기 조정

### <a name="reason"></a>이유

개발 공간에서 서비스를 실행 하는 경우 해당 서비스의 pod가 [계측을 위한 추가 컨테이너와 함께 삽입](how-dev-spaces-works.md#prepare-your-aks-cluster) 되며 pod의 모든 컨테이너에는 수평 pod 자동 크기 조정에 대해 설정 된 리소스 제한 및 요청이 있어야 합니다. 


Pod 사양에 `azds.io/proxy-resources` 주석을 추가 하 여 삽입 된 컨테이너 (devspaces-프록시)에 리소스 요청 및 제한을 적용할 수 있습니다. 프록시에 대 한 컨테이너 사양의 리소스 섹션을 나타내는 JSON 개체로 값을 설정 해야 합니다.

### <a name="try"></a>시도해 보기

다음은 pod 사양에 적용 되는 프록시 리소스 주석의 예입니다.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

## <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>개인 레지스트리에서 Docker 이미지를 사용 하려고 할 때 "권한이 없음: 인증 필요" 오류

### <a name="reason"></a>이유

인증을 요구 하는 개인 레지스트리에서 Docker 이미지를 사용 하 고 있습니다. 개발 공간에서 [Imagepullsecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)사용 하 여이 개인 레지스트리에서 이미지를 인증 하 고 가져올 수 있습니다.

### <a name="try"></a>시도해 보기

ImagePullSecrets을 사용 하려면 이미지를 사용 하는 네임 스페이스에 [Kubernetes secret을 만듭니다](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) . 그런 다음에서 `azds.yaml`암호를 imagePullSecret로 제공 합니다.

다음은에서 `azds.yaml`imagePullSecrets 지정 하는 예입니다.

```
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> 에서 `azds.yaml` imagepullsecrets 설정 하면에 지정 된 `values.yaml`imagepullsecrets 재정의 됩니다.
