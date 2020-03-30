---
title: 문제 해결
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Azure 개발자 공간을 활성화하고 사용할 때 일반적인 문제를 해결하고 해결하는 방법을 알아보십시오.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: c12dfd385962d8dd7de8239a0d4ecd46746499c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239778"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure 개발자 공간 문제 해결

이 가이드는 Azure Dev Spaces를 사용할 때 발생할 수 있는 일반적인 문제에 대한 정보를 포함합니다.

Azure 개발자 공간을 사용할 때 문제가 있는 경우 [Azure 개발자 공간 GitHub 리포지토리에서 문제를](https://github.com/Azure/dev-spaces/issues)만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

자세한 검토 로그를 만들면 더 효과적으로 문제를 해결하는 데 도움이 될 수 있습니다.

Visual Studio 확장의 경우 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 환경 변수를 1로 설정합니다. 환경 변수가 적용되도록 Visual Studio를 다시 시작해야 합니다. 이와 같이 환경 변수가 사용되면 자세한 로그가 사용자 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 디렉터리에 작성됩니다.

CLI에서 `--verbose` 전환을 사용하여 명령을 실행하는 동안 자세한 정보를 출력할 수 있습니다. `%TEMP%\Azure Dev Spaces`에서 자세한 로그를 찾아볼 수도 있습니다. Mac에서 *임시* 디렉터리는 터미널 창에서 `echo $TMPDIR` 실행하여 찾을 수 있습니다. Linux 컴퓨터에서 *TEMP* `/tmp`디렉터리입니다. 또한 [Azure CLI 구성 파일에서](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables)로깅이 활성화되어 있는지 확인합니다.

Azure 개발자 공간은 단일 인스턴스 또는 포드를 디버깅할 때도 가장 효과적입니다. 파일에는 `azds.yaml` Kubernetes가 서비스에 대해 실행하는 포드 수를 나타내는 *설정인 replicaCount가*포함되어 있습니다. *replicaCount를* 변경하여 응용 프로그램이 지정된 서비스에 대해 여러 포드를 실행하도록 구성하면 디버거가 사전순으로 나열될 때 첫 번째 포드에 연결됩니다. 원래 Pod가 재순환될 때는 디버거가 다른 Pod에 연결되므로 예기치 않은 동작이 발생할 수 있습니다.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Azure 개발자 공간을 사용하도록 설정할 때 의 일반적인 문제

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>오류 "Azure 개발자 공간 컨트롤러를 만들지 못했습니다."

컨트롤러를 만들 때 무언가 잘못되면 이 오류가 표시됩니다. 일시적인 오류인 경우 컨트롤러를 삭제하고 다시 만들면 문제가 해결됩니다.

컨트롤러를 삭제해 볼 수도 있습니다.

```bash
azds remove -g <resource group name> -n <cluster name>
```

Azure 개발자 공간 CLI를 사용하여 컨트롤러를 삭제합니다. Visual Studio에서 컨트롤러를 삭제할 수 없습니다. 또한 Azure 클라우드 셸에서 컨트롤러를 삭제할 수 없도록 Azure 클라우드 셸에 Azure 개발자 공간 CLI를 설치할 수 없습니다.

Azure 개발자 공간 CLI가 설치되어 있지 않은 경우 먼저 다음 명령을 사용하여 설치한 다음 컨트롤러를 삭제할 수 있습니다.

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

CLI 또는 Visual Studio에서 컨트롤러를 다시 만들 수 있습니다. 예를 들어 [.NET Core 퀵스타트를 통해](quickstart-netcore-visualstudio.md) [팀 개발](quickstart-team-development.md) 또는 개발을 확인하십시오.

### <a name="controller-create-failing-because-of-controller-name-length"></a>컨트롤러 이름 길이 때문에 컨트롤러 생성 실패

Azure 개발자 공간 컨트롤러의 이름은 31자 보다 길수 없습니다. AKS 클러스터에서 개발자 공백을 활성화하거나 컨트롤러를 만들 때 컨트롤러 이름이 31자를 초과하면 오류가 발생합니다. 예를 들어:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

이 문제를 해결하려면 대체 이름으로 컨트롤러를 만듭니다. 예를 들어:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Windows 노드 풀이 AKS 클러스터에 추가될 때 개발자 공간 실패 설정

현재 Azure 개발자 공간은 Linux 포드 및 노드에서만 실행됩니다. Windows 노드 풀이 있는 AKS 클러스터가 있는 경우 Azure 개발자 공간 포드가 Linux 노드에서만 예약되어 있는지 확인해야 합니다. Azure 개발자 공간 포드가 Windows 노드에서 실행되도록 예약된 경우 해당 포드가 시작되지 않고 개발자 공간을 사용하도록 설정하면 실패합니다.

이 문제를 해결하려면 AKS [클러스터에 더럽히기를 추가하여](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) Linux 포드가 Windows 노드에서 실행되도록 예약되지 않도록 합니다.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>오류 "클러스터의 준비 상태에서 오염되지 않은 Linux 노드를 찾을 수 없습니다. 'azds' 네임스페이스에 포드를 배포하려면 Ready 상태에 적어도 하나의 오염되지 않은 Linux 노드가 있어야 합니다."

Azure 개발자 공간은 준비 상태에서 포드를 예약할 *수* 있는 오염되지 않은 노드를 찾을 수 없기 때문에 AKS 클러스터에서 컨트롤러를 만들 수 없습니다. Azure 개발자 공간에는 허용 을 지정하지 않고 포드를 예약할 수 있는 *준비* 상태에서 하나 이상의 Linux 노드가 필요합니다.

이 문제를 해결하려면 AKS 클러스터의 [오염 된 구성을 업데이트하여](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) 하나 이상의 Linux 노드가 허용을 지정하지 않고 포드를 예약 할 수 있도록합니다. 또한 허용 을 지정하지 않고 포드를 예약할 수 있는 하나 이상의 Linux 노드가 *준비* 상태인지 확인합니다. 노드가 *준비* 상태에 도달하는 데 시간이 오래 걸리는 경우 노드를 다시 시작할 수 있습니다.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>az aks 사용 -dev-spaces를 실행할 때 오류 "Azure 개발자 공간 CLI가 제대로 설치되지 않았습니다"

Azure 개발자 공간 CLI에 대한 업데이트로 설치 경로가 변경되었습니다. 2.0.63 이전 버전의 Azure CLI를 사용하는 경우 이 오류가 표시될 수 있습니다. Azure CLI 버전을 표시하려면 을 `az --version`사용합니다.

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

2.0.63 `az aks use-dev-spaces` 이전에 Azure CLI 버전으로 실행할 때 오류 메시지에도 불구하고 설치는 성공합니다. 문제 없이 계속 `azds` 사용할 수 있습니다.

이 문제를 해결하려면 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 설치를 2.0.63 이상으로 업데이트합니다. 이 업데이트는 을 실행할 `az aks use-dev-spaces`때 받는 오류 메시지를 해결합니다. 또는 현재 버전의 Azure CLI 및 Azure 개발자 공간 CLI를 계속 사용할 수 있습니다.

### <a name="error-unable-to-reach-kube-apiserver"></a>오류 "쿠베 apiserver에 연결할 수 없습니다"

Azure 개발자 공간이 AKS 클러스터의 API 서버에 연결할 수 없는 경우 이 오류가 표시될 수 있습니다. 

AKS 클러스터 API 서버에 대한 액세스가 잠겨 있거나 [AKS](../aks/api-server-authorized-ip-ranges.md) 클러스터에 대해 API 서버 권한 부여 IP 주소 범위가 활성화된 경우 지역에 따라 추가 범위를 [허용하도록 클러스터를](https://github.com/Azure/dev-spaces/tree/master/public-ips) [만들거나](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) [업데이트해야](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) 합니다.

kubectl 명령을 실행하여 API 서버를 사용할 수 있는지 확인합니다. API 서버를 사용할 수 없는 경우 AKS 지원에 문의하여 API 서버가 작동할 때 다시 시도하십시오.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Azure 개발자 공간에 대 한 프로젝트를 준비 하는 경우 일반적인 문제

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>경고 "지원되지 않는 언어로 인해 Dockerfile을 생성할 수 없습니다."
Azure Dev Spaces는 C# 및 Node.js에 대해 네이티브 지원을 제공합니다. 이러한 언어 `azds prep` 중 하나로 작성된 코드를 사용 하 여 디렉터리에서 실행 하는 경우 Azure 개발자 공간 자동으로 적절 한 Dockerfile를 만듭니다.

다른 언어로 작성된 코드로 Azure 개발자 공간을 계속 사용할 수 있지만 처음으로 실행하기 `azds up` 전에 Dockerfile을 수동으로 만들어야 합니다.

응용 프로그램이 Azure 개발자 공간에서 기본적으로 지원하지 않는 언어로 작성된 경우 코드를 실행하는 컨테이너 이미지를 빌드하기 위해 적절한 Dockerfile을 제공해야 합니다. Docker는 요구에 맞는 Dockerfile을 작성하는 데 도움이 되는 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/) 및 [Dockerfile 작성 모범 사례 목록](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)을 제공합니다.

적절한 Dockerfile이 있으면 Azure 개발자 `azds up` 공간에서 응용 프로그램을 실행합니다.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Azure 개발자 공간으로 서비스를 시작하거나 중지할 때 의 일반적인 문제

### <a name="error-config-file-not-found"></a>오류 "구성 파일을 찾을 수 없습니다:"

실행 `azds up`하면 이 오류가 표시 될 수 있습니다. 둘 `azds up` `azds prep` 다 개발 공간에서 실행하려는 프로젝트의 루트 디렉터리에서 실행되어야 합니다.

이 문제를 해결하려면
1. 현재 디렉터리를 서비스 코드를 포함하는 루트 폴더로 변경합니다. 
1. 코드 폴더에 _azds.yaml_ 파일이 없는 경우 실행하여 `azds prep` Docker, Kubernetes 및 Azure 개발자 공간 자산을 생성합니다.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>"컨테이너 이미지 빌드 대기 중..." AKS 가상 노드를 사용하면 단계

이 시간 아웃은 개발자 공간을 사용하여 [AKS 가상 노드에서](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)실행되도록 구성된 서비스를 실행하려고 할 때 발생합니다. 개발자 공간은 현재 가상 노드에서 서비스 빌드 또는 디버깅을 지원하지 않습니다.

`--verbose` 스위치를 사용하여 `azds up`을 실행하거나 Visual Studio에서 자세한 정보 로깅을 사용하도록 설정하면 추가 정보가 표시됩니다.

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

위의 명령은 서비스의 포드가 *가상 노드-aci-linux에*할당된 것을 보여 주며, 이는 가상 노드입니다.

이 문제를 해결하려면 서비스가 가상 노드에서 실행될 수 있도록 하는 *노드선택또는* *허용* 값을 제거하도록 서비스에 대한 Helm 차트를 업데이트합니다. 이러한 값은 일반적으로 차트의 `values.yaml` 파일에서 정의됩니다.

Dev Spaces를 통해 빌드하거나 디버깅하려는 서비스가 VM 노드에서 실행되는 경우 가상 노드 기능이 활성화된 AKS 클러스터를 계속 사용할 수 있습니다. VM 노드에서 개발자 공백으로 서비스를 실행하는 것이 기본 구성입니다.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>데브 스페이스를 시작할 때 오류 "준비 된 틸러 포드를 찾을 수 없습니다"

이 오류는 Helm 클라이언트가 클러스터에서 실행 중인 Tiller Pod에 더 이상 연결할 수 없는 경우에 발생합니다.

이 문제를 해결하려면 클러스터에서 에이전트 노드를 다시 시작합니다.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>오류 "릴리스\<azds-식별자\>-\<-\<\>스페이스\> 네임\<서비스\>이름 실패: 서비스 ' 서비스 \<이름\>' 이미 존재" 또는 "서비스 이름에 대 한 액세스 가져오기 거부, 저장소가 존재 하지 않거나 '도커 로그인'을 요구할 수 있습니다."

이러한 `helm install`오류는 실행 중인 직접 Helm 명령(예: `helm upgrade` `helm delete`또는) 개발자 공백 명령(예: `azds up` 및)을 `azds down`동일한 개발 공간 내에 혼합하는 경우에 발생할 수 있습니다. 데브 스페이스에는 동일한 개발 공간에서 실행되는 고유한 틸러 인스턴스와 충돌하는 자체 틸러 인스턴스가 있기 때문에 발생합니다.

동일한 AKS 클러스터에 대해 Helm 명령과 개발자 공간 명령을 모두 사용하는 것은 좋지만, 각 개발자 공간 지원 네임스페이스는 둘 중 하나를 사용해야 합니다.

예를 들어 Helm 명령을 사용하여 부모 개발 공간에서 전체 응용 프로그램을 실행한다고 가정합니다. 해당 부모에서 자식 개발 공간을 만들고, 개발자 공간을 사용하여 자식 개발 공간 내에서 개별 서비스를 실행하고, 함께 서비스를 테스트할 수 있습니다. 변경 내용을 체크 인할 준비가 되면 Helm 명령을 사용하여 업데이트된 코드를 부모 개발 공간에 배포합니다. 부모 개발 `azds up` 공간에서 업데이트된 서비스를 실행하는 데 사용하지 마십시오.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>컨테이너를 빌드하는 데 사용되지 않는 기존 Dockerfile

Azure 개발자 공간은 프로젝트의 특정 _Dockerfile을_ 가리키도록 구성할 수 있습니다. Azure Dev Spaces가 컨테이너를 빌드하는 데 필요한 _Dockerfile_을 사용하지 않는 것 같으면 Azure Dev Spaces에서 사용할 Dockerfile을 명시적으로 지정해야 할 수 있습니다. 

이 문제를 해결하려면 프로젝트에서 Azure 개발자 공백이 생성한 _azds.yaml_ 파일을 엽니다. 업데이트 *구성: 개발: 빌드: 도커파일을* 사용하여 사용할 Dockerfile을 가리킵니다. 예를 들어:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>개인 레지스트리에서 Docker 이미지를 사용 하려고 할 때 오류 "무단: 인증 필요"

인증이 필요한 개인 레지스트리의 Docker 이미지를 사용하고 있습니다.

이 문제를 해결하려면 개발자 공백이 [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)를 사용하여 이 개인 레지스트리에서 이미지를 인증하고 가져올 수 있도록 허용할 수 있습니다. ImagePullSecrets를 사용하려면 이미지를 사용하는 네임스페이스에서 [Kubernetes 비밀을 만듭니다.](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) 그런 다음 이미지로 비밀을 제공풀 시크릿 에서 `azds.yaml`.

다음은 에서 끌어비밀을 지정하는 이미지의 `azds.yaml`예입니다.

```yaml
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
> 이미지PullSecrets를 `azds.yaml` 설정하면 이미지에 지정된 풀시크릿이 재정의됩니다. `values.yaml`

### <a name="error-service-cannot-be-started"></a>오류 "서비스를 시작할 수 없습니다."

서비스 코드를 시작하지 못하면 이 오류가 발생할 수 있습니다. 사용자 코드에 원인이 있는 경우가 많습니다. 더 많은 진단 정보를 얻으려면 서비스를 시작할 때 더 자세한 로깅을 사용하도록 설정합니다.

명령줄에서 를 `--verbose` 사용하여 보다 자세한 로깅을 활성화합니다. 을 사용하여 `--output`출력 형식을 지정할 수도 있습니다. 예를 들어:

```cmd
azds up --verbose --output json
```

Visual Studio에서

1. **도구 > 옵션**을 열고 **프로젝트 및 솔루션** 아래에서 **빌드 및 실행**을 선택합니다.
2. **MSBuild 프로젝트 빌드 출력 세부 정보 표시**의 설정을 **세부 내용** 또는 **진단**으로 변경합니다.

    ![도구 옵션 대화 상자 스크린샷](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>컨트롤러를 다시 만든 후 서비스 다시 실행

이 클러스터와 연결된 Azure Dev Spaces 컨트롤러를 제거했다가 다시 만든 후 서비스를 다시 실행하려고 하면 ‘서비스를 시작할 수 없습니다.’라는 오류가 표시됩니다.** 이 경우 자세한 정보 출력에 다음 텍스트가 포함됩니다.

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

이 오류는 Dev Spaces 컨트롤러를 제거해도 해당 컨트롤러에서 이전에 설치한 서비스가 제거되지 않기 때문에 발생합니다. 이전 서비스가 그대로 있기 때문에 컨트롤러를 다시 만든 후, 새 컨트롤러를 사용해서 서비스를 실행하려고 하면 실패합니다.

이 문제를 해결하려면 `kubectl delete` 명령을 사용하여 클러스터에서 이전 서비스를 수동으로 제거하고 Dev Spaces를 다시 실행하여 새 서비스를 설치합니다.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>오류 "서비스를 시작할 수 없습니다." 다단계 Dockerfiles를 사용하는 경우

다단계 Dockerfile을 사용하는 경우 ‘서비스를 시작할 수 없습니다.’라는 오류가 표시됩니다.** 이 경우 자세한 정보 출력에 다음 텍스트가 포함됩니다.

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

이 오류는 Azure 개발자 공간에서 현재 다단계 빌드를 지원하지 않기 때문에 발생합니다. 다단계 빌드를 방지하려면 Dockerfile을 다시 작성합니다.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>개발 컴퓨터를 연결할 때 네트워크 트래픽이 AKS 클러스터로 전달되지 않습니다.

Azure 개발자 공간을 사용하여 [AKS 클러스터를 개발 컴퓨터에 연결하는](how-to/connect.md)경우 개발 컴퓨터와 AKS 클러스터 간에 네트워크 트래픽이 전달되지 않는 문제가 발생할 수 있습니다.

개발 컴퓨터를 AKS 클러스터에 연결할 때 Azure 개발자 공간은 개발 컴퓨터의 `hosts` 파일을 수정하여 AKS 클러스터와 개발 컴퓨터 간의 네트워크 트래픽을 전달합니다. Azure 개발자 공간호스트 이름으로 `hosts` 대체하려는 Kubernetes 서비스의 주소가 있는 항목을 만듭니다. 이 항목은 개발 컴퓨터와 AKS 클러스터 간의 네트워크 트래픽을 직접 전달하기 위한 포트 포워딩과 함께 사용됩니다. 개발 컴퓨터의 서비스가 대체하는 Kubernetes 서비스의 포트와 충돌하는 경우 Azure 개발자 공간은 Kubernetes 서비스에 대한 네트워크 트래픽을 전달할 수 없습니다. 예를 들어 *Windows BranchCache* 서비스는 일반적으로 *0.0.0.0:80에*바인딩되어 충돌로 인해 모든 로컬 IP에서 포트 80에 대한 충돌이 발생합니다.

이 문제를 해결하려면 대체하려는 Kubernetes 서비스의 포트와 충돌하는 서비스 또는 프로세스를 중지해야 합니다. *netstat*와 같은 도구를 사용하여 개발 컴퓨터에서 충돌하는 서비스 또는 프로세스를 검사할 수 있습니다.

예를 들어 *Windows BranchCache* 서비스를 중지하고 사용하지 않도록 설정하려면 다음을 수행하십시오.
* 명령 `services.msc` 프롬프트에서 실행합니다.
* *BranchCache를* 마우스 오른쪽 단추로 클릭하고 *속성을*선택합니다.
* *중지*를 클릭합니다.
* 선택적으로 *시작 유형을* 사용 *안 함으로*설정하여 비활성화할 수 있습니다.
* *확인*을 클릭합니다.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Azure 개발자 공간을 사용하여 Visual Studio 및 Visual Studio 코드를 사용하는 일반적인 문제

### <a name="error-required-tools-and-configurations-are-missing"></a>오류 "필수 도구 및 구성이 누락되었습니다."

VS Code를 시작할 때 다음 오류가 발생할 수 있습니다. "[Azure Dev Spaces] '[프로젝트 이름]'을 빌드하고 디버그할 필수 도구 및 구성이 누락되었습니다."
이 오류는 VS Code에서 볼 수 있듯이 PATH 환경 변수에 azds.exe가 없음을 의미합니다.

PATH 환경 변수가 제대로 설정된 명령 프롬프트에서 VS Code를 시작해 보십시오.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>"'projectname' 빌드 및 디버그에 필요한 도구가 최신 상태가 아닙니다." 오류가 표시됩니다.

Azure Dev Spaces용 VS Code 확장은 최신 버전이지만 Azure Dev Spaces CLI가 이전 버전인 경우 Visual Studio Code에 이 오류가 표시됩니다.

Azure 개발자 공간 CLI의 최신 버전을 다운로드하고 설치해 보십시오.

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>오류: "type:coreclr에 대한 디버거 확장을 찾지 못했습니다."

Visual Studio 코드 디버거를 실행할 때 이 오류가 표시될 수 있습니다. 개발 컴퓨터에 C#에 대한 VS 코드 확장이 설치되어 있지 않을 수 있습니다. C# 확장에는 .NET 코어(CoreCLR)에 대한 디버깅 지원이 포함됩니다.

이 문제를 해결하려면 [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)에 대한 VS 코드 확장을 설치합니다.

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>오류 "구성된 디버그 유형 'coreclr'가 지원되지 않습니다."

Visual Studio 코드 디버거를 실행할 때 이 오류가 표시될 수 있습니다. 개발 컴퓨터에 Azure 개발자 공간에 대한 VS 코드 확장이 설치되어 있지 않을 수 있습니다.

이 문제를 해결 하려면 Azure 개발자 공간에 대 한 VS 코드 확장을 설치 [합니다.](get-started-netcore.md)

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>오류 "잘못된 'cwd' 값 '/src'. 시스템은 지정된 파일을 찾을 수 없습니다." 또는 "launch: program '/src/[프로젝트 이진 경로]'이(가) 존재하지 않습니다."가 발생합니다.

Visual Studio 코드 디버거를 실행할 때 이 오류가 표시될 수 있습니다. 기본적으로 VS Code 확장은 컨테이너에 대한 프로젝트의 작업 디렉터리로 여 `src`를 사용합니다. 다른 작업 디렉터리를 지정하도록 `Dockerfile`을 업데이트한 경우에 다음 오류가 표시될 수 있습니다.

이 문제를 해결하려면 `launch.json` 프로젝트 폴더의 `.vscode` 하위 디렉터리 에서 파일을 업데이트합니다. 프로젝트의 `Dockerfile`에 정의된 `WORKDIR`과 동일한 디렉터리를 가리키도록 `configurations->cwd` 지시문을 변경합니다. `configurations->program` 지시문도 업데이트해야 할 수 있습니다.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>오류 "파이프 프로그램 'azds' 코드 126으로 예기치 않게 종료되었습니다."

Visual Studio 코드 디버거를 실행할 때 이 오류가 표시될 수 있습니다.

이 문제를 해결하려면 Visual Studio 코드를 닫고 다시 엽니다. 디버거를 다시 시작합니다.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Node.js 응용 프로그램에 디버깅을 첨부할 때 오류 "내부 시계 실패: ENOSPC 보기"

이 오류는 디버거를 사용하여 연결하려는 Node.js 응용 프로그램을 사용하여 포드를 실행하는 노드가 *fs.inotify.max_user_watches* 값을 초과했을 때 발생합니다. 경우에 따라 [ *fs.inotify.max_user_watches* 기본값이 너무 작아서 디버거를 포드에 직접 연결하는 것을 처리할 수 없습니다.](https://github.com/Azure/AKS/issues/772)

이 문제에 대한 임시 해결 방법은 클러스터의 각 노드에서 *fs.inotify.max_user_watches* 값을 늘리고 변경 사항이 적용되도록 해당 노드를 다시 시작하는 것입니다.

## <a name="other-common-issues"></a>기타 일반적인 문제

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>오류 "azds"가 내부 또는 외부 명령, 작동 가능한 프로그램 또는 일괄 처리 파일로 인식되지 않음

이 오류는 `azds.exe` 올바르게 설치하거나 구성되지 않은 경우 발생할 수 있습니다.

이 문제를 해결하려면

1. 위치 %ProgramFiles%/Microsoft SDKs\Azure\Azure 개발자 공간 `azds.exe`CLI를 확인합니다. 위치가 있는 경우 해당 위치를 PATH 환경 변수에 추가합니다.
2. 설치되지 않은 경우 `azds.exe` 다음 명령을 실행합니다.

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>권한 부여 오류 "Microsoft.DevSpaces/레지스터/작업"

Azure Dev Spaces를 관리하려면 Azure 구독에서 ‘owner’ 또는 ‘contributor’ 액세스 권한이 있어야 합니다.**** 개발자 공간을 관리하려고 하는 데 사용 중이며 연결된 Azure 구독에 대한 *소유자* 또는 *기여자* 액세스 권한이 없는 경우 권한 부여 오류가 표시될 수 있습니다. 예를 들어:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

이 문제를 해결하려면 Azure 구독에 대한 *소유자* 또는 *기여자* 액세스 `Microsoft.DevSpaces` 권한이 있는 계정을 사용하여 네임스페이스를 수동으로 등록합니다.

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>새 창이 시작되지 않습니다.

Kubernetes 초기화자는 클러스터의 *클러스터 관리자* 역할에 대한 RBAC 권한 변경으로 인해 새 포드에 대해 PodSpec을 적용할 수 없습니다. 새 포드에는 잘못된 PodSpec(예: 포드와 연결된 서비스 계정)이 더 이상 존재하지 않을 수도 있습니다. 초기화자 문제로 인해 *보류 중인* 상태인 창을 보려면 `kubectl get pods` 명령을 사용합니다.

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

이 문제는 Azure 개발자 공백을 사용할 수 없는 네임스페이스를 포함하여 클러스터의 *모든 네임스페이스의* 포드에 영향을 미칠 수 있습니다.

이 문제를 해결하려면 [개발자 공간 CLI를 최신 버전으로 업데이트한](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) 다음 Azure 개발자 공간 컨트롤러에서 *azds InitializerConfiguration를* 삭제합니다.

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Azure 개발자 공간 컨트롤러에서 *azds InitializerConfiguration을* 제거한 `kubectl delete` 후에는 *보류 중인* 상태의 모든 창을 제거하는 데 사용합니다. 보류 중인 모든 포드가 제거된 후 창을 다시 배포합니다.

새 포드가 재배포 후에도 *보류 중인* 상태로 남아 `kubectl delete` 있으면 *보류 중* 상태의 모든 창을 제거하는 데 사용합니다. 보류 중인 모든 포드가 제거된 후 클러스터에서 컨트롤러를 삭제하고 다시 설치합니다.

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

컨트롤러를 다시 설치한 후 포드를 다시 배포합니다.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>개발자 공간 컨트롤러 및 API호출에 대한 잘못된 RBAC 권한

Azure 개발자 공간 컨트롤러에 액세스하는 사용자는 AKS 클러스터에서 관리자 *kubeconfig를* 읽을 수 있는 액세스 권한이 있어야 합니다. 예를 들어 이 권한은 [기본 제공 Azure Kubernetes 서비스 클러스터 관리자 역할에서](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)사용할 수 있습니다. Azure 개발자 공간 컨트롤러에 액세스하는 사용자는 컨트롤러에 대한 *기여자* 또는 *소유자* RBAC 역할도 가져야 합니다. AKS 클러스터에 대한 사용자 사용 권한 업데이트에 대한 자세한 내용은 [여기에서](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)확인할 수 있습니다.

컨트롤러에 대한 사용자의 RBAC 역할을 업데이트하려면 다음을 수행하십시오.

1. [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.
1. 일반적으로 AKS 클러스터와 동일한 컨트롤러가 포함된 리소스 그룹으로 이동합니다.
1. 숨겨진 유형 표시 확인란을 *활성화합니다.*
1. 컨트롤러를 클릭합니다.
1. *IAM(액세스 제어) 창을 엽니다.*
1. *역할 할당 탭을 클릭합니다.*
1. 다음 *역할 할당 추가를*클릭합니다. *Add*
    * *역할에서* *기여자* 또는 *소유자*를 선택합니다.
    * *액세스 할당*에서 *Azure AD 사용자, 그룹 또는 서비스 보안 주체*를 선택합니다.
    * *선택*- 사용 권한을 부여하려는 사용자를 검색합니다.
1. *저장*을 클릭합니다.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces 서비스에 연결된 공용 URL에 대한 DNS 이름 확인 실패

`azds prep` 명령에 `--enable-ingress` 스위치를 지정하거나 Visual Studio에서 `Publicly Accessible` 확인란을 선택하여 서비스에 대해 공용 URL 엔드포인트를 구성할 수 있습니다. Dev Spaces에서 서비스를 실행하면 공용 DNS 이름이 자동으로 등록됩니다. 이 DNS 이름이 등록되지 않은 경우 공용 URL에 연결할 때 웹 브라우저에 ‘페이지를 표시할 수 없습니다.’ 또는 ‘사이트에 연결할 수 없습니다.’라는 오류가 표시됩니다.****

이 문제를 해결하려면

* 개발자 공간 서비스와 연결된 모든 URL의 상태를 확인합니다.

  ```console
  azds list-uris
  ```

* URL이 *보류 중인* 상태인 경우 개발자 공간은 여전히 DNS 등록이 완료되기를 기다리고 있습니다. 경우에 따라 등록을 완료하려면 몇 분 정도 걸립니다. Dev Spaces는 각 서비스마다 localhost 터널도 엽니다. 이것을 DNS 등록을 대기하는 동안 사용할 수 있습니다.
* URL이 5분 넘게 ‘보류 중’ 상태로 있으면 공용 엔드포인트를 만드는 외부 DNS Pod 또는 공용 엔드포인트를 획득하는 nginx 수신 컨트롤러에 문제가 있는 것일 수 있습니다.** 다음 명령을 사용하여 이러한 창을 삭제하고 AKS가 자동으로 다시 만들 수 있도록 합니다.
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>오류 "업스트림 연결 오류 또는 헤더 전에 연결 해제/재설정"

서비스에 액세스하려고 할 때 이 오류가 표시될 수 있습니다. 예를 들어 브라우저에서 서비스의 URL로 이동하는 경우입니다. 이 오류는 컨테이너 포트를 사용할 수 없음을 의미합니다. 이는 다음과 같은 이유로 인해 가능합니다.

* 컨테이너가 아직 빌드 및 배포되는 중입니다. `azds up`을 시작하거나 디버거를 시작한 다음, 성공적으로 배포하기 전에 컨테이너에 액세스하려고 하는 경우 이 문제가 발생할 수 있습니다.
* 포트 구성은 _Dockerfile,_ 헬름 차트 및 포트를 여는 모든 서버 코드에서 일관되지 않습니다.

이 문제를 해결하려면

1. 컨테이너가 빌드/배포되는 중인 경우 2-3초 기다렸다가 서비스에 액세스를 다시 시도할 수 있습니다. 
1. 다음 에셋에서 포트 구성을 확인합니다.
    * **[투구 차트](https://docs.helm.sh):** `service.port` 및 `deployment.containerPort` value.yaml 명령에 의해 `azds prep` 스캐폴드에 의해 지정됩니다.
    * 애플리케이션 코드에서 열리는 모든 포트(예: Node.js에서 `var server = app.listen(80, function () {...}`)

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>형식 또는 네임스페이스 이름 "MyLibrary"를 찾을 수 없습니다.

사용 중이던 라이브러리 프로젝트를 찾을 수 없습니다. 개발자 공간을 사용하면 빌드 컨텍스트가 기본적으로 프로젝트/서비스 수준에 있습니다.  

이 문제를 해결하려면

1. 빌드 `azds.yaml` 컨텍스트를 솔루션 수준으로 설정하려면 파일을 수정합니다.
2. 예를 `Dockerfile` 들어 `Dockerfile.develop` `.csproj`새 빌드 컨텍스트를 기준으로 올바르게 프로젝트 파일을 참조하도록 및 파일을 수정합니다.
3. 파일과 `.dockerignore` 동일한 디렉터리에 `.sln` 를 추가합니다.
4. 필요에 `.dockerignore` 따라 추가 항목으로 업데이트합니다.

[여기에서](https://github.com/sgreenmsft/buildcontextsample)예제를 찾을 수 있습니다.

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>개발 공간에서 가로 포드 자동 크기 조정이 작동하지 않음

개발 공간에서 서비스를 실행하면 해당 서비스의 포드에 [계측을 위한 추가 컨테이너가 주입되고](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) 포드의 모든 컨테이너에는 수평 포드 자동 크기 조정에 대한 리소스 제한 및 요청이 설정되어 있어야 합니다.

이 문제를 해결하려면 리소스 요청 및 주입된 개발자 공간 컨테이너에 제한을 적용합니다. 리소스 요청 및 제한은 포드 사양에 추가하여 `azds.io/proxy-resources` 주입된 컨테이너(devspaces-proxy)에 적용할 수 있습니다. 값은 프록시에 대한 컨테이너 사양의 리소스 섹션을 나타내는 JSON 개체로 설정되어야 합니다.

다음은 포드 사양에 적용할 프록시 리소스 추가의 예입니다.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>실행 중인 포드가 있는 기존 네임스페이스에서 Azure 개발자 공간 활성화

Azure 개발자 공간을 사용하도록 설정하려는 실행 중인 포드가 있는 기존 AKS 클러스터 및 네임스페이스가 있을 수 있습니다.

AKS 클러스터의 기존 네임스페이스에서 Azure 개발자 `use-dev-spaces` 공간을 `kubectl` 활성화하려면 실행하고 사용하여 해당 네임스페이스의 모든 포드를 다시 시작합니다.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

포드를 다시 시작한 후 Azure 개발자 공간에서 기존 네임스페이스사용을 시작할 수 있습니다.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>클러스터 노드에 대한 트래픽이 제한된 AKS 클러스터에서 Azure 개발자 공간 사용

클러스터 노드에서 송신 트래픽이 제한된 AKS 클러스터에서 Azure 개발자 공간을 사용하려면 다음 FQDN을 허용해야 합니다.

| FQDN                                    | 포트      | 사용      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 리눅스 알파인 및 기타 Azure 개발자 공간 이미지를 끌어 |
| gcr.io | HTTP:443 | 투구/틸러 이미지를 당기려면|
| storage.googleapis.com | HTTP:443 | 투구/틸러 이미지를 당기려면|
| azds-<guid>. <location>.azds.io | HTTPS:443 | 컨트롤러에 대한 Azure 개발자 공간 백 엔드 서비스와 통신합니다. 정확한 FQDN은 %USERPROFILE %\.azds\settings.json의 "데이터 플레인Fqdn"에서 찾을 수 있습니다.|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>\<오류 "구독\> \<구독Id에서\>클러스터 클러스터를 찾을 수 없습니다"

kubeconfig 파일이 Azure Dev Spaces 클라이언트 측 툴링과 다른 클러스터 또는 구독을 대상으로 하는 경우 이 오류가 표시될 수 있습니다. Azure Dev Spaces 클라이언트 측 도구는 [하나 이상의 kubeconfig 파일을](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) 사용하여 클러스터를 선택하고 통신하는 *kubectl의*동작을 복제합니다.

이 문제를 해결하려면

* 현재 `az aks use-dev-spaces -g <resource group name> -n <cluster name>` 컨텍스트를 업데이트하는 데 사용합니다. 또한 이 명령을 사용하면 AKS 클러스터의 Azure 개발자 공간을 사용할 수 없습니다. 또는 현재 컨텍스트를 `kubectl config use-context <cluster name>` 업데이트하는 데 사용할 수 있습니다.
* 대상으로 `az account show` 하는 현재 Azure 구독을 표시 하 고 이것이 올바른지 확인 하는 데 사용 합니다. 을 사용하여 `az account set`대상으로 하는 구독을 변경할 수 있습니다.

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>AKS 인증서를 회전한 후 개발자 공백을 사용하는 오류

[AKS 클러스터에서 인증서를 회전한](../aks/certificate-rotation.md)후 특정 `azds space list` 작업(예: 및 `azds up` 실패)이 실패합니다. 또한 클러스터에서 인증서를 회전한 후 Azure 개발자 공간 컨트롤러에서 인증서를 새로 고쳐야 합니다.

이 문제를 해결하려면 *kubeconfig에* 명령을 실행하여 `az aks get-credentials` 업데이트된 `azds controller refresh-credentials` 인증서가 있는지 확인합니다. 예를 들어:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
