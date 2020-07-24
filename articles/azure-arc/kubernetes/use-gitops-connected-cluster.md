---
title: Arc enabled Kubernetes cluster (Preview)에서 GitOps를 사용 하 여 구성 배포
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc 지원 클러스터 구성에 GitOps 사용(미리 보기)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: e25fdf3a51b3e9264c85707df31d3a4d107b25ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049969"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Arc enabled Kubernetes cluster (Preview)에서 GitOps를 사용 하 여 구성 배포

GitOps는 Git 리포지토리에서 Kubernetes 구성 (배포, 네임 스페이스 등)의 desired 상태를 선언 하 고, 연산자를 사용 하 여 클러스터에 이러한 구성의 폴링 및 끌어오기 기반 배포를 수행 하는 방법입니다. 이 문서에서는 Azure Arc 사용 Kubernetes 클러스터에서 이러한 워크플로의 설정에 대해 설명 합니다.

클러스터와 하나 이상의 Git 리포지토리 간의 연결은 Azure Resource Manager에서 `sourceControlConfiguration` 확장 리소스로 추적됩니다. `sourceControlConfiguration` 리소스 속성은 Kubernetes 리소스가 Git에서 클러스터로 이동하는 위치와 방법을 나타냅니다. 데이터는 `sourceControlConfiguration` 데이터의 기밀성을 보장 하기 위해 Azure Cosmos DB 데이터베이스에 암호화 된 상태로 저장 됩니다.

`config-agent`클러스터에서 실행 되는는 `sourceControlConfiguration` Azure Arc enabled Kubernetes 리소스에서 새로운 또는 업데이트 된 확장 리소스를 감시 하 고, flux 운영자를 배포 하 여 Git 리포지토리를 시청 하 고,에 대 한 모든 업데이트를 전파 하는 작업을 담당 합니다 `sourceControlConfiguration` . `sourceControlConfiguration` `namespace` 다중 테 넌 트를 얻기 위해 동일한 Azure Arc 사용 Kubernetes 클러스터에서 범위를 사용 하 여 여러 리소스를 만들 수도 있습니다. 이러한 경우 각 연산자는 해당 네임 스페이스에만 구성을 배포할 수 있습니다.

Git 리포지토리에는 네임스페이스, ConfigMaps, 배포, DaemonSets 등을 비롯한 모든 유효한 Kubernetes 리소스가 포함될 수 있습니다.  애플리케이션 배포용 Helm 차트가 포함될 수도 있습니다. 공통적인 시나리오 집합에는 조직의 기본 구성을 정의하는 것이 포함됩니다. 여기에는 일반적인 RBAC 역할 및 바인딩, 모니터링 또는 로깅 에이전트, 또는 클러스터 전체 서비스가 포함될 수 있습니다.

동일한 패턴을 사용 하 여 더 큰 클러스터 컬렉션을 관리할 수 있습니다 .이는 다른 유형의 환경에서 배포 될 수 있습니다. 예를 들어 조직의 기준 구성을 정의하고 수십 개의 Kubernetes 클러스터에 한 번에 적용하는 하나의 리포지토리가 있을 수 있습니다. [Azure 정책은](use-azure-policy.md) `sourceControlConfiguration` 범위 (구독 또는 리소스 그룹)의 모든 Azure Arc 사용 Kubernetes 리소스에서 특정 매개 변수 집합을 사용 하 여 만들기를 자동화할 수 있습니다.

이 시작 가이드에서는 클러스터 관리자 범위를 사용하여 구성 집합을 적용하는 과정을 안내합니다.

## <a name="create-a-configuration"></a>구성 만들기

- 예제 리포지토리: <https://github.com/Azure/arc-k8s-demo>

예제 리포지토리는 몇 가지 네임스페이스를 프로비전하고, 공통 워크로드를 배포하고, 몇 가지 팀별 구성을 제공하려는 클러스터 연산자의 가상 사용자를 중심으로 구성됩니다. 이 리포지토리를 사용하면 클러스터에 다음 리소스가 만들어집니다.

**네임스페이스:** `cluster-config`, `team-a`, `team-b`
**배포:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

는 새 `config-agent` `sourceControlConfiguration` `config-agent` 구성 또는 업데이트 된 구성을 선택 하는 데 소요 되는 최대 시간인 30 초 마다 Azure를 새로 또는 업데이트 하도록 폴링합니다.
개인 리포지토리를와 연결 하는 경우 `sourceControlConfiguration` [개인 git 리포지토리에서 구성 적용](#apply-configuration-from-a-private-git-repository)의 단계도 완료 해야 합니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

의 Azure CLI 확장을 사용 하 여 `k8sconfiguration` 연결 된 클러스터를 [예제 git 리포지토리에](https://github.com/Azure/arc-k8s-demo)연결 해 보겠습니다. 이 구성에 이름 `cluster-config`를 지정하고, `cluster-config` 네임스페이스에 연산자를 배포하도록 에이전트에 지시하고, 연산자에게 `cluster-admin` 권한을 부여합니다.

```console
az k8sconfiguration create \
    --name cluster-config \
    --cluster-name AzureArcTest1 --resource-group AzureArcTest \
    --operator-instance-name cluster-config --operator-namespace cluster-config \
    --repository-url https://github.com/Azure/arc-k8s-demo \
    --scope cluster --cluster-type connectedClusters
```

**출력:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>repository-url 매개 변수

다음은 --repository url 매개 변수의 값에 대해 지원되는 시나리오입니다.

| 시나리오 | 형식 | Description |
| ------------- | ------------- | ------------- |
| 프라이빗 GitHub 리포지토리 - SSH | git@github.com:username/repo | Flux에 의해 생성된 SSH 키 쌍입니다.  사용자는 공개 키를 GitHub 계정에 배포 키로 추가해야 합니다. |
| 공용 GitHub 리포지토리 | `http://github.com/username/repo` 또는 git://github.com/username/repo   | 공용 Git 리포지토리  |

이러한 시나리오는 Flux에서 지원되지만 아직 sourceControlConfiguration에서는 지원되지 않습니다. 

| 시나리오 | 형식 | Description |
| ------------- | ------------- | ------------- |
| 프라이빗 GitHub 리포지토리 - HTTPS | `https://github.com/username/repo` | Flux는 SSH 키 쌍을 생성하지 않습니다.  [지침](https://docs.fluxcd.io/en/1.17.0/guides/use-git-https.html) |
| 프라이빗 Git 호스트 | user@githost:path/to/repo | [지침](https://docs.fluxcd.io/en/1.18.0/guides/use-private-git-host.html) |
| 프라이빗 GitHub 리포지토리 - SSH(사용자 고유 키 가져오기) | git@github.com:username/repo | [사용자 고유 SSH 키 쌍 사용](https://docs.fluxcd.io/en/1.17.0/guides/provide-own-ssh-key.html) |


#### <a name="additional-parameters"></a>추가 매개 변수

다음은 구성 만들기를 사용자 지정하는 몇 가지 추가 매개 변수입니다.

`--enable-helm-operator` : Helm 차트 배포에 대한 지원을 사용하도록 설정하는 선택적 스위치입니다.

`--helm-operator-chart-values` : Helm 연산자(사용 설정된 경우)에 대한 선택적 차트 값입니다.  예: '--set helm.versions=v3'.

`--helm-operator-chart-version` : Helm 연산자(사용 설정된 경우)에 대한 선택적 차트 버전입니다. Default: '0.6.0'.

`--operator-namespace` : 연산자 네임스페이스의 선택적 이름입니다. 기본값: 'default'

`--operator-params` : 연산자에 대한 선택적 매개 변수입니다. 작은따옴표 안에 지정해야 합니다. 예를 들어 ```--operator-params='--git-readonly --git-path=releases' ```

--operator-params에서 지원되는 옵션

| 옵션 | Description |
| ------------- | ------------- |
| --git-branch  | Kubernetes 매니페스트에 사용할 git 리포지토리의 분기입니다. 기본값은 'master'입니다. |
| --git-path  | Kubernetes 매니페스트를 찾는 Flux에 대한 Git 리포지토리 내의 상대 경로입니다. |
| --git-readonly | Git 리포지토리는 읽기 전용으로 간주됩니다. Flux는 리포지토리에 쓰기를 시도하지 않습니다. |
| --manifest-generation  | 사용 설정된 경우 Flux는 .flux.yaml을 찾고 Kustomize 또는 기타 매니페스트 생성기를 실행합니다. |
| --git-poll-interval  | Git 리포지토리에서 새 커밋을 폴링하는 주기입니다. 기본값은 '5m'(5분)입니다. |
| --sync-garbage-collection  | 사용 설정된 경우 Flux는 만들었지만 더 이상 Git에 존재하지 않는 리소스를 삭제합니다. |
| --git-label  | 동기화 진행률을 추적하는 레이블로, Git 분기에 태그를 지정하는 데 사용됩니다.  기본값은 'flux-sync'입니다. |
| --git-user  | Git 커밋의 사용자 이름입니다. |
| --git-email  | Git 커밋에 사용할 이메일입니다. |

* '--git-user' 또는 '--git-email'이 설정되지 않은 경우(즉, Flux가 리포지토리에 쓰지 않게 하려는 경우) --git-readonly가 자동으로 설정됩니다(아직 설정되지 않은 경우).

* enableHelmOperator가 true인 경우 operatorInstanceName + operatorNamespace 문자열이 총 47자를 초과할 수 없습니다.  이 한도를 준수 하지 않으면 다음과 같은 오류가 표시 됩니다.

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

자세한 내용은 [Flux 설명서](https://aka.ms/FluxcdReadme)를 참조 하세요.

> [!TIP]
> Azure Arc enabled Kubernetes 리소스 블레이드의 **구성** 탭 에서도 Azure Portal에 sourceControlConfiguration를 만들 수 있습니다.

## <a name="validate-the-sourcecontrolconfiguration"></a>sourceControlConfiguration 유효성 검사

Azure CLI를 사용하여 `sourceControlConfiguration`이 성공적으로 만들어졌는지 확인합니다.

```console
az k8sconfiguration show --resource-group AzureArcTest --name cluster-config --cluster-name AzureArcTest1 --cluster-type connectedClusters
```

`sourceControlConfiguration` 리소스는 준수 상태, 메시지 및 디버깅 정보로 업데이트됩니다.

**출력:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

`sourceControlConfiguration`이 만들어지면 내부적으로 몇 가지 작업이 수행됩니다.

1. Azure Arc `config-agent`가 Azure Resource Manager에서 새로운 구성 또는 업데이트된 구성(`Microsoft.KubernetesConfiguration/sourceControlConfiguration`)을 모니터링합니다.
1. `config-agent`가 새로운 `Pending` 구성을 알립니다.
1. `config-agent`가 구성 속성을 읽고 `flux`의 관리되는 인스턴스를 배포할 준비를 합니다.
    * `config-agent`가 대상 네임스페이스를 만듭니다.
    * `config-agent`가 적절한 권한(`cluster` 또는 `namespace` 범위)을 사용하여 Kubernetes 서비스 계정을 준비합니다.
    * `config-agent`가 `flux`의 인스턴스를 배포합니다.
    * `flux`SSH 키를 생성 하 고 공개 키를 로깅합니다.
1. `config-agent`가 상태를 `sourceControlConfiguration`에 다시 보고합니다.

프로비전 프로세스가 진행되는 동안 `sourceControlConfiguration`이 몇 가지 상태 변경을 거칩니다. 위의 `az k8sconfiguration show ...` 명령을 사용하여 진행률을 모니터링합니다.

1. `complianceStatus` -> `Pending`: 초기 및 진행 중인 상태를 나타냅니다.
1. `complianceStatus` -> `Installed`: `config-agent`가 클러스터를 성공적으로 구성하고 오류 없이 `flux`를 배포할 수 있었습니다.
1. `complianceStatus` -> `Failed`: `config-agent`가 `flux`를 배포하는 동안 오류가 발생했습니다. `complianceStatus.message` 응답 본문에서 세부 정보를 확인할 수 있습니다.

## <a name="apply-configuration-from-a-private-git-repository"></a>프라이빗 git 리포지토리에서 구성 적용

개인 git 리포지토리를 사용 하는 경우에는에서 생성 된 공개 키를 `flux` 리포지토리의 **배포 키** 로 추가 하는 작업을 하나 더 수행 해야 합니다.

**Azure CLI를 사용하여 공개 키 가져오기**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Azure Portal에서 공개 키 가져오기**

1. Azure Portal에서 연결된 클러스터 리소스로 이동합니다.
2. 리소스 페이지에서 "구성"을 선택하고 이 클러스터에 대한 구성 목록을 확인합니다.
3. 프라이빗 Git 리포지토리를 사용하는 구성을 선택합니다.
4. 열린 컨텍스트 창의 맨 아래에서 **리포지토리 공개 키**를 복사합니다.

**공개 키를 git 리포지토리에 배포 키로 추가**

1. GitHub를 열고 포크로 이동한 다음 **설정**, **배포 키**로 이동합니다.
2. **배포 키 추가**를 클릭합니다.
3. 제목을 제공합니다.
4. **쓰기 액세스 허용**을 선택합니다.
5. 공개 키를 붙여넣습니다(따옴표 제외).
6. **키 추가**를 클릭합니다.

이러한 키를 관리 하는 방법에 대 한 자세한 내용은 GitHub 문서를 참조 하세요.

**Azure DevOps 리포지토리를 사용하는 경우 SSH 키에 키를 추가합니다.**

1. 오른쪽 위(프로필 이미지 옆)에 있는 **사용자 설정**에서 **SSH 공개 키**를 클릭합니다.
1. **+ 새 키**를 선택합니다.
1. 이름을 제공합니다.
1. 따옴표를 제외하고 공개 키를 붙여넣습니다.
1. **추가**를 클릭합니다.

## <a name="validate-the-kubernetes-configuration"></a>Kubernetes 구성의 유효성을 검사합니다.

`config-agent`가 `flux` 인스턴스를 설치하면 git 리포지토리에 저장된 리소스가 클러스터로 이동하기 시작합니다. 네임스페이스, 배포 및 리소스가 만들어졌는지 확인합니다.

```console
kubectl get ns --show-labels
```

**출력:**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

`team-a`, `team-b`, `itops` 및 `cluster-config` 네임스페이스가 만들어진 것을 확인할 수 있습니다.

`flux` 연산자는 `sourceControlConfig`에서 지시한 대로 `cluster-config` 네임스페이스에 배포되었습니다.

```console
kubectl -n cluster-config get deploy  -o wide
```

**출력:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>추가 탐색

구성 리포지토리의 일부로 배포된 다른 리소스를 탐색할 수 있습니다.

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>구성 삭제

`sourceControlConfiguration`Azure CLI 또는 Azure Portal를 사용 하 여를 삭제 합니다.  Delete 명령을 시작 하면 `sourceControlConfiguration` 리소스가 Azure에서 즉시 삭제 되지만 클러스터에서 연결 된 개체를 전체 삭제 하는 데 최대 1 시간이 걸릴 수 있습니다 .이 시간 지연을 줄이기 위해 백로그 항목이 있습니다.

> [!NOTE]
> 네임 스페이스 범위가 있는 sourceControlConfiguration을 만든 후에는 네임 스페이스에 역할을 바인딩하는 사용자가 `edit` 이 네임 스페이스에 작업을 배포할 수 있습니다. `sourceControlConfiguration`네임 스페이스 범위를 사용 하 여이를 삭제 하는 경우 네임 스페이스는 그대로 유지 되므로 이러한 다른 작업의 중단을 방지 하기 위해 삭제 되지 않습니다.
> 추적 된 git 리포지토리에서 배포의 결과로 생성 된 클러스터에 대 한 변경 내용은이 삭제 될 때 삭제 되지 않습니다 `sourceControlConfiguration` .

```console
az k8sconfiguration delete --name '<config name>' -g '<resource group name>' --cluster-name '<cluster name>' --cluster-type connectedClusters
```

**출력:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>다음 단계

- [소스 제어 구성과 함께 투구 사용](./use-gitops-with-helm.md)
- [Azure Policy를 사용하여 클러스터 구성 관리](./use-azure-policy.md)
