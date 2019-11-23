---
title: 문제 해결
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Azure에서 컨테이너 및 마이크로 서비스가 있는 Kubernetes 개발 환경을 빠르게 만듭니다.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: f1d3c0aa9827582f0c928c4700ebef935ae2d1c3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424064"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces troubleshooting

이 가이드는 Azure Dev Spaces를 사용할 때 발생할 수 있는 일반적인 문제에 대한 정보를 포함합니다.

If you have a problem when using Azure Dev Spaces, create an [issue in the Azure Dev Spaces GitHub repository](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>시작하기 전에

자세한 검토 로그를 만들면 더 효과적으로 문제를 해결하는 데 도움이 될 수 있습니다.

Visual Studio 확장의 경우 `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 환경 변수를 1로 설정합니다. 환경 변수가 적용되도록 Visual Studio를 다시 시작해야 합니다. 이와 같이 환경 변수가 사용되면 자세한 로그가 사용자 `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` 디렉터리에 작성됩니다.

CLI에서 `--verbose` 전환을 사용하여 명령을 실행하는 동안 자세한 정보를 출력할 수 있습니다. `%TEMP%\Azure Dev Spaces`에서 자세한 로그를 찾아볼 수도 있습니다. Mac의 경우 터미널 창에서 `echo $TMPDIR`을 실행하면 TEMP 디렉터리를 찾을 수 있습니다. Linux 컴퓨터에서 TEMP 디렉터리는 일반적으로 `/tmp`입니다.

Azure Dev Spaces also works best when debugging a single instance, or pod. The `azds.yaml` file contains a setting, *replicaCount*, that indicates the number of pods that Kubernetes runs for your service. If you change the *replicaCount* to configure your application to run multiple pods for a given service, the debugger attaches to the first pod, when listed alphabetically. 원래 Pod가 재순환될 때는 디버거가 다른 Pod에 연결되므로 예기치 않은 동작이 발생할 수 있습니다.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Common issues when enabling Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Error "Failed to create Azure Dev Spaces controller"

컨트롤러를 만들 때 무언가 잘못되면 이 오류가 표시됩니다. 일시적인 오류인 경우 컨트롤러를 삭제하고 다시 만들면 문제가 해결됩니다.

You can also try deleting the controller:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Use the Azure Dev Spaces CLI to delete a controller. It’s not possible to delete a controller from Visual Studio. You also can't install the Azure Dev Spaces CLI in the Azure Cloud Shell so you can't delete a controller from the Azure Cloud Shell.

If you don't have the Azure Dev Spaces CLI installed, you can first install it using the following command then delete your controller:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

CLI 또는 Visual Studio에서 컨트롤러를 다시 만들 수 있습니다. See the [Team development](quickstart-team-development.md) or [Develop with .NET Core](quickstart-netcore-visualstudio.md) quickstarts for examples.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Controller create failing because of controller name length

An Azure Dev Spaces controller's name can't be longer than 31 characters. If your controller's name exceeds 31 characters when you enable Dev Spaces on an AKS cluster or create a controller, you'll receive an error. 다음은 그 예입니다.

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

To fix this issue, create a controller with an alternate name. 다음은 그 예입니다.

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Enabling Dev Spaces failing when Windows node pools are added to an AKS cluster

Currently, Azure Dev Spaces is intended to run on Linux pods and nodes only. When you have an AKS cluster with a Windows node pool, you must ensure that Azure Dev Spaces pods are only scheduled on Linux nodes. If an Azure Dev Spaces pod is scheduled to run on a Windows node, that pod won't start and enabling Dev Spaces will fail.

To fix this issue, [add a taint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) to your AKS cluster to ensure Linux pods aren't scheduled to run on a Windows node.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Error "Found no untainted Linux nodes in Ready state on the cluster. There needs to be at least one untainted Linux node in Ready state to deploy pods in 'azds' namespace."

Azure Dev Spaces couldn't create a controller on your AKS cluster because it couldn't find an untainted node in a *Ready* state to schedule pods on. Azure Dev Spaces requires at least one Linux node in a *Ready* state that allows for scheduling pods without specifying tolerations.

To fix this issue, [update your taint configuration](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) on your AKS cluster to ensure at least one Linux node allows for scheduling pods without specifying tolerations. Also, ensure that at least one Linux node that allows scheduling pods without specifying tolerations is in the *Ready* state. If your node is taking a long time to reach the *Ready* state, you can try restarting your node.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Error "Azure Dev Spaces CLI not installed properly" when running `az aks use-dev-spaces`

An update to the Azure Dev Spaces CLI changed its installation path. If you're using a version of the Azure CLI earlier than 2.0.63, you may see this error. To display your version of the Azure CLI, use `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Despite the error message when running `az aks use-dev-spaces` with a version of the Azure CLI before 2.0.63, the installation does succeed. You can continue to use `azds` without any issues.

To fix this issue, update your installation of the [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) to 2.0.63 or later. This update will resolve the error message you receive when running `az aks use-dev-spaces`. Alternatively, you can continue to use your current version of the Azure CLI and the Azure Dev Spaces CLI.

### <a name="error-unable-to-reach-kube-apiserver"></a>Error "Unable to reach kube-apiserver"

You might see this error when Azure Dev Spaces is unable to connect to your AKS cluster's API server. 

If access to your AKS cluster API server is locked down or if you have [API server authorized IP address ranges](../aks/api-server-authorized-ip-ranges.md) enabled for your AKS cluster, you must also [create](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) or [update](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) your cluster to [allow additional ranges based on your region](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Ensure that the API server is available by running kubectl commands. If the API server is unavailable, please contact AKS support and try again when the API server is working.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Common issues when preparing your project for Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Warning "Dockerfile could not be generated due to unsupported language"
Azure Dev Spaces는 C# 및 Node.js에 대해 네이티브 지원을 제공합니다. When you run `azds prep` in a directory with code written in one of these languages, Azure Dev Spaces automatically creates an appropriate Dockerfile for you.

You can still use Azure Dev Spaces with code written in other languages, but you need to manually create the Dockerfile before running `azds up` for the first time.

If your application is written in a language that Azure Dev Spaces doesn't natively support, you need to provide an appropriate Dockerfile to build a container image running your code. Docker는 요구에 맞는 Dockerfile을 작성하는 데 도움이 되는 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/) 및 [Dockerfile 작성 모범 사례 목록](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)을 제공합니다.

Once you have an appropriate Dockerfile in place, you run `azds up` to run your application in Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Common issues when starting or stopping services with Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Error "Config file not found:"

When running `azds up`, you may see this error. Both `azds up` and `azds prep` must be run from the root directory of the project you want to run in your dev space.

이 문제를 해결하려면
1. 현재 디렉터리를 서비스 코드를 포함하는 루트 폴더로 변경합니다. 
1. If you don't have a _azds.yaml_ file in the code folder, run `azds prep` to generate Docker, Kubernetes, and Azure Dev Spaces assets.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Timeout at "Waiting for container image build..." step with AKS virtual nodes

This timeout occurs when you attempt to use Dev Spaces to run a service that is configured to run on an [AKS virtual node](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Dev Spaces doesn't currently support building or debugging services on virtual nodes.

`--verbose` 스위치를 사용하여 `azds up`을 실행하거나 Visual Studio에서 자세한 정보 로깅을 사용하도록 설정하면 추가 정보가 표시됩니다.

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

The above command shows that the service's pod was assigned to *virtual-node-aci-linux*, which is a virtual node.

To fix this issue, update the Helm chart for the service to remove any *nodeSelector* or *tolerations* values that allow the service to run on a virtual node. 이러한 값은 일반적으로 차트의 `values.yaml` 파일에서 정의됩니다.

You can still use an AKS cluster that has the virtual nodes feature enabled, if the service you wish to build or debug via Dev Spaces runs on a VM node. Running a service with Dev Spaces on a VM node is the default configuration.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Error "could not find a ready tiller pod" when launching Dev Spaces

이 오류는 Helm 클라이언트가 클러스터에서 실행 중인 Tiller Pod에 더 이상 연결할 수 없는 경우에 발생합니다.

To fix this issue, restart the agent nodes in your cluster.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Error "release azds-\<identifier\>-\<spacename\>-\<servicename\> failed: services '\<servicename\>' already exists" or "Pull access denied for \<servicename\>, repository does not exist or may require 'docker login'"

These errors can occur if you mix running direct Helm commands (such as `helm install`, `helm upgrade`, or `helm delete`) with Dev Spaces commands (such as `azds up` and `azds down`) inside the same dev space. They occur because Dev Spaces has its own Tiller instance, which conflicts with your own Tiller instance running in the same dev space.

It's fine to use both Helm commands and Dev Spaces commands against the same AKS cluster, but each Dev Spaces-enabled namespace should use either one or the other.

For example, suppose you use a Helm command to run your entire application in a parent dev space. You can create child dev spaces off that parent, use Dev Spaces to run individual services inside the child dev spaces, and test the services together. When you're ready to check in your changes, use a Helm command to deploy the updated code to the parent dev space. Don't use `azds up` to run the updated service in the parent dev space, because it will conflict with the service initially run using Helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Existing Dockerfile not used to build a container

프로젝트에서 특정 _Dockerfile_을 가리키도록 Azure Dev Spaces를 구성할 수 있습니다. Azure Dev Spaces가 컨테이너를 빌드하는 데 필요한 _Dockerfile_을 사용하지 않는 것 같으면 Azure Dev Spaces에서 사용할 Dockerfile을 명시적으로 지정해야 할 수 있습니다. 

To fix this issue, open the _azds.yaml_ file that Azure Dev Spaces generated in your project. Update *configurations: develop: build: dockerfile* to point to the Dockerfile you want to use. 다음은 그 예입니다.

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Error "unauthorized: authentication required" when trying to use a Docker image from a private registry

You're using a Docker image from a private registry that requires authentication.

To fix this issue, you can allow Dev Spaces to authenticate and pull images from this private registry using [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). To use imagePullSecrets, [create a Kubernetes secret](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) in the namespace where you're using the image. Then provide the secret as an imagePullSecret in `azds.yaml`.

Below is an example of a specifying imagePullSecrets in `azds.yaml`.

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
> Setting imagePullSecrets in `azds.yaml` will override imagePullSecrets specified in the `values.yaml`.

### <a name="error-service-cannot-be-started"></a>Error "Service cannot be started."

서비스 코드를 시작하지 못하면 이 오류가 발생할 수 있습니다. 사용자 코드에 원인이 있는 경우가 많습니다. To get more diagnostic information, enable more detailed logging when starting your service.

From the command line, use the `--verbose` to enable more detailed logging. You can also specify an output format using `--output`. 다음은 그 예입니다.

```cmd
azds up --verbose --output json
```

Visual Studio에서

1. **도구 > 옵션**을 열고 **프로젝트 및 솔루션** 아래에서 **빌드 및 실행**을 선택합니다.
2. **MSBuild 프로젝트 빌드 출력 세부 정보 표시**의 설정을 **세부 내용** 또는 **진단**으로 변경합니다.

    ![도구 옵션 대화 상자 스크린샷](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>컨트롤러를 다시 만든 후 서비스 다시 실행

이 클러스터와 연결된 Azure Dev Spaces 컨트롤러를 제거했다가 다시 만든 후 서비스를 다시 실행하려고 하면 ‘서비스를 시작할 수 없습니다.’라는 오류가 표시됩니다. 이 경우 자세한 정보 출력에 다음 텍스트가 포함됩니다.

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

This error occurs because removing the Dev Spaces controller doesn't remove services previously installed by that controller. 이전 서비스가 그대로 있기 때문에 컨트롤러를 다시 만든 후, 새 컨트롤러를 사용해서 서비스를 실행하려고 하면 실패합니다.

이 문제를 해결하려면 `kubectl delete` 명령을 사용하여 클러스터에서 이전 서비스를 수동으로 제거하고 Dev Spaces를 다시 실행하여 새 서비스를 설치합니다.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Error "Service cannot be started." when using multi-stage Dockerfiles

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

This error occurs because AKS nodes run an older version of Docker that doesn't support multi-stage builds. 다단계 빌드를 방지하려면 Dockerfile을 다시 작성합니다.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Network traffic is not forwarded to your AKS cluster when connecting your development machine

When using [Azure Dev Spaces to connect your AKS cluster to your development machine](how-to/connect.md), you may encounter an issue where network traffic is not forwarded between your development machine and your AKS cluster.

When connecting your development machine to your AKS cluster, Azure Dev Spaces forwards network traffic between your AKS cluster and your development machine by modifying your development machine's `hosts` file. Azure Dev Spaces creates an entry in the `hosts` with the address of the Kubernetes service you are replacing as a host name. This entry is used with port forwarding to direct network traffic between your development machine and the AKS cluster. If a service on your development machine conflicts with the port of the Kubernetes service you are replacing, Azure Dev Spaces cannot forward network traffic for the Kubernetes service. For example, the *Windows BranchCache* service is usually bound to *0.0.0.0:80*, which conflicts will cause a conflict for port 80 on all local IPs.

To fix this issue, you need to stop any services or processes that conflict with port of the Kubernetes service you are trying to replace. You can use tools, such as *netstat*, to inspect what services or processes on your development machine are in conflict.

For example, to stop and disable the *Windows BranchCache* service:
* Run `services.msc` from the command prompt.
* Right click on *BranchCache* and select *Properties*.
* Click *Stop*.
* Optionally, you can disable it by setting *Startup type* to *Disabled*.
* *확인*을 클릭합니다.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Common issues using Visual Studio and Visual Studio Code with Azure Dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Error "Required tools and configurations are missing"

VS Code를 시작할 때 다음 오류가 발생할 수 있습니다. "[Azure Dev Spaces] '[프로젝트 이름]'을 빌드하고 디버그할 필수 도구 및 구성이 누락되었습니다."
이 오류는 VS Code에서 볼 수 있듯이 PATH 환경 변수에 azds.exe가 없음을 의미합니다.

Try launching VS Code from a command prompt where the PATH environment variable is set properly.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>"'projectname' 빌드 및 디버그에 필요한 도구가 최신 상태가 아닙니다." 오류가 표시됩니다.

Azure Dev Spaces용 VS Code 확장은 최신 버전이지만 Azure Dev Spaces CLI가 이전 버전인 경우 Visual Studio Code에 이 오류가 표시됩니다.

Try downloading and installing the latest version of the Azure Dev Spaces CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Error: "Failed to find debugger extension for type:coreclr"

You may see this error when running the Visual Studio Code debugger. You might not have the VS Code extension for C# installed on your development machine. The C# extension includes debugging support for .NET Core (CoreCLR).

To fix this issue, install the [VS Code extension for C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Error "Configured debug type 'coreclr' is not supported"

You may see this error when running the Visual Studio Code debugger. You might not have the VS Code extension for Azure Dev Spaces installed on your development machine.

To fix this issue, install the [VS Code extension for Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Error "Invalid 'cwd' value '/src'. 시스템은 지정된 파일을 찾을 수 없습니다." 또는 "launch: program '/src/[프로젝트 이진 경로]'이(가) 존재하지 않습니다."가 발생합니다.

You may see this error when running the Visual Studio Code debugger. 기본적으로 VS Code 확장은 컨테이너에 대한 프로젝트의 작업 디렉터리로 여 `src`를 사용합니다. 다른 작업 디렉터리를 지정하도록 `Dockerfile`을 업데이트한 경우에 다음 오류가 표시될 수 있습니다.

To fix this issue, update the `launch.json` file under the `.vscode` subdirectory of your project folder. 프로젝트의 `Dockerfile`에 정의된 `WORKDIR`과 동일한 디렉터리를 가리키도록 `configurations->cwd` 지시문을 변경합니다. `configurations->program` 지시문도 업데이트해야 할 수 있습니다.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Error "The pipe program 'azds' exited unexpectedly with code 126."

You may see this error when running the Visual Studio Code debugger.

To fix this issue, close and reopen Visual Studio Code. Restart the debugger.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Error "Internal watch failed: watch ENOSPC" when attaching debugging to a Node.js application

This error occurs when the node running the pod with the Node.js application you're trying to attach to with a debugger has exceeded the *fs.inotify.max_user_watches* value. In some cases, [the default value of *fs.inotify.max_user_watches* may be too small to handle attaching a debugger directly to a pod](https://github.com/Azure/AKS/issues/772).

A temporary workaround for this issue is to increase the value of *fs.inotify.max_user_watches* on each node in the cluster and restart that node for the changes to take effect.

## <a name="other-common-issues"></a>Other common issues

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Error "azds" is not recognized as an internal or external command, operable program, or batch file

This error can happen if `azds.exe` is not installed or configured correctly.

이 문제를 해결하려면

1. Check the location %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI for `azds.exe`. 위치가 있는 경우 해당 위치를 PATH 환경 변수에 추가합니다.
2. If `azds.exe` isn't installed, run the following command:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Authorization error "Microsoft.DevSpaces/register/action"

Azure Dev Spaces를 관리하려면 Azure 구독에서 ‘owner’ 또는 ‘contributor’ 액세스 권한이 있어야 합니다. If you're trying to manage Dev Spaces and you don't have *Owner* or *Contributor* access to the associated Azure subscription, you may see an authorization error. 다음은 그 예입니다.

```console
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

To fix this issue, using an account with *Owner* or *Contributor* access to the Azure subscription, manually register the `Microsoft.DevSpaces` namespace:

```console
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>New pods aren't starting

The Kubernetes initializer can't apply the PodSpec for new pods due to RBAC permission changes to the *cluster-admin* role in the cluster. The new pod may also have an invalid PodSpec, for example the service account associated with the pod no longer exists. To see the pods that are in a *Pending* state due to the initializer issue, use the `kubectl get pods` command:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

This issue can impact pods in *all namespaces* in the cluster including namespaces where Azure Dev Spaces is not enabled.

To fix this issue, [update the Dev Spaces CLI to the latest version](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) and then deleting the *azds InitializerConfiguration* from the Azure Dev Spaces controller:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Once you have removed the *azds InitializerConfiguration* from the Azure Dev Spaces controller, use `kubectl delete` to remove any pods in a *Pending* state. After all pending pods have been removed, redeploy your pods.

If new pods are still stuck in a *Pending* state after a redeployment, use `kubectl delete` to remove any pods in a *Pending* state. After all pending pods have been removed, delete the controller from the cluster and reinstall it:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

After your controller is reinstalled, redeploy your pods.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Incorrect RBAC permissions for calling Dev Spaces controller and APIs

The user accessing the Azure Dev Spaces controller must have access to read the admin *kubeconfig* on the AKS cluster. For example, this permission is available in the [built-in Azure Kubernetes Service Cluster Admin Role](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). The user accessing the Azure Dev Spaces controller must also have the *Contributor* or *Owner* RBAC role for the controller. More details on updating a user's permissions for an AKS cluster are available [here](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

To update the user's RBAC role for the controller:

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
1. Navigate to the Resource Group containing the controller, which is usually the same as your AKS cluster.
1. Enable the *Show hidden types* checkbox.
1. Click on the controller.
1. Open the *Access Control (IAM)* pane.
1. Click on the *Role Assignments* tab.
1. Click *Add* then *Add role assignment*.
    * For *Role*, select either *Contributor* or *Owner*.
    * *액세스 할당*에서 *Azure AD 사용자, 그룹 또는 서비스 보안 주체*를 선택합니다.
    * For *Select*, search for the user you want to give permissions.
1. 페이지 맨 아래에 있는 *저장*을 참조하세요.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces 서비스에 연결된 공용 URL에 대한 DNS 이름 확인 실패

`azds prep` 명령에 `--public` 스위치를 지정하거나 Visual Studio에서 `Publicly Accessible` 확인란을 선택하여 서비스에 대해 공용 URL 엔드포인트를 구성할 수 있습니다. Dev Spaces에서 서비스를 실행하면 공용 DNS 이름이 자동으로 등록됩니다. 이 DNS 이름이 등록되지 않은 경우 공용 URL에 연결할 때 웹 브라우저에 ‘페이지를 표시할 수 없습니다.’ 또는 ‘사이트에 연결할 수 없습니다.’라는 오류가 표시됩니다.

이 문제를 해결하려면

* Check the status of all URLs associated with your Dev Spaces services:

  ```console
  azds list-uris
  ```

* If a URL is in the *Pending* state, Dev Spaces is still waiting for DNS registration to complete. 경우에 따라 등록을 완료하려면 몇 분 정도 걸립니다. Dev Spaces는 각 서비스마다 localhost 터널도 엽니다. 이것을 DNS 등록을 대기하는 동안 사용할 수 있습니다.
* URL이 5분 넘게 ‘보류 중’ 상태로 있으면 공용 엔드포인트를 만드는 외부 DNS Pod 또는 공용 엔드포인트를 획득하는 nginx 수신 컨트롤러에 문제가 있는 것일 수 있습니다. Use the following commands to delete these pods and allow AKS to automatically recreate them:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Error "upstream connect error or disconnect/reset before headers"

서비스에 액세스하려고 할 때 이 오류가 표시될 수 있습니다. 예를 들어 브라우저에서 서비스의 URL로 이동하는 경우입니다. This error means the container port isn't available. This can for the follow reasons:

* 컨테이너가 아직 빌드 및 배포되는 중입니다. `azds up`을 시작하거나 디버거를 시작한 다음, 성공적으로 배포하기 전에 컨테이너에 액세스하려고 하는 경우 이 문제가 발생할 수 있습니다.
* 포트 구성이 _Dockerfile_, Helm 차트 및 포트를 여는 모든 서버 코드에서 일치하지 않습니다.

이 문제를 해결하려면

1. 컨테이너가 빌드/배포되는 중인 경우 2-3초 기다렸다가 서비스에 액세스를 다시 시도할 수 있습니다. 
1. 포트 구성을 확인합니다. 지정된 포트 번호는 다음과 같은 모든 자산에서 **동일**해야 합니다.
    * **Dockerfile:** `EXPOSE` 명령으로 지정됩니다.
    * **[Helm 차트](https://docs.helm.sh):** 서비스에 대해 `externalPort` 및 `internalPort` 값으로 지정됩니다(종종 `values.yml` 파일에 위치함).
    * 애플리케이션 코드에서 열리는 모든 포트(예: Node.js에서 `var server = app.listen(80, function () {...}`)

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>The type or namespace name "MyLibrary" couldn't be found

A library project you're using can't be found. With Dev Spaces, the build context is at the project/service level by default.  

이 문제를 해결하려면

1. Modify the `azds.yaml` file to set the build context to the solution level.
2. Modify the `Dockerfile` and `Dockerfile.develop` files to refer to the project files, for example `.csproj`, correctly relative to the new build context.
3. Add a `.dockerignore` in the same directory as the `.sln` file.
4. Update the `.dockerignore` with additional entries as needed.

You can find an example at [here](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Horizontal pod autoscaling not working in a dev space

When you run a service in a dev space, that service's pod is [injected with additional containers for instrumentation](how-dev-spaces-works.md#prepare-your-aks-cluster) and all the containers in a pod need to have resource limits and requests set for Horizontal Pod Autoscaling.

To fix this issue, apply a resource request and limit to the injected Dev Spaces containers. Resource requests and limits can be applied for the injected container (devspaces-proxy) by adding the `azds.io/proxy-resources` annotation to your pod spec. The value should be set to a JSON object representing the resources section of the container spec for the proxy.

Below is an example of a proxy-resources annotation that is to be applied to your pod spec.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Enable Azure Dev Spaces on an existing namespace with running pods

You may have an existing AKS cluster and namespace with running pods where you want to enable Azure Dev Spaces.

To enable Azure Dev Spaces on an existing namespace in an AKS cluster, run `use-dev-spaces` and use `kubectl` to restart all pods in that namespace.

```console
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
kubectl -n my-namespace delete pod --all
```

After your pods have restarted, you can begin using your existing namespace with Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Enable Azure Dev Spaces on AKS cluster with restricted egress traffic for cluster nodes

To enable Azure Dev Spaces on an AKS cluster for which the egress traffic from cluster nodes is restricted, you will have to allow following FQDNs:

| FQDN                                    | Port      | 사용      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | To pull linux alpine and other Azure Dev Spaces images |
| gcr.io | HTTP:443 | To pull helm/tiller images|
| storage.googleapis.com | HTTP:443 | To pull helm/tiller images|