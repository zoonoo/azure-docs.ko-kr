---
title: Arc 지원 Kubernetes 클러스터에서 GitOps를 사용하여 구성 배포(미리 보기)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: GitOps를 사용 하 여 Azure Arc enabled Kubernetes 클러스터 구성 (미리 보기)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, AKS, 컨테이너
ms.openlocfilehash: 072bfc8c243eb9b69e06366961019b88b67e0941
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392241"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Arc 지원 Kubernetes 클러스터에서 GitOps를 사용하여 구성 배포(미리 보기)

Kubernetes와 관련해 서 GitOps는 Git 리포지토리에서 Kubernetes 클러스터 구성 (배포, 네임 스페이스 등)의 desired 상태를 선언 하는 방법입니다. 이 선언은 다음에 연산자를 사용 하 여 이러한 클러스터 구성의 폴링 및 끌어오기 기반 배포를 수행 합니다. 

이 문서에서는 Azure Arc 사용 Kubernetes 클러스터에서 GitOps 워크플로를 설정 하는 방법을 설명 합니다.

클러스터와 Git 리포지토리 간의 연결은 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` Azure Resource Manager 확장 리소스로 생성 됩니다. `sourceControlConfiguration` 리소스 속성은 Kubernetes 리소스가 Git에서 클러스터로 이동하는 위치와 방법을 나타냅니다. 데이터는 `sourceControlConfiguration` 데이터의 기밀성을 보장 하기 위해 암호화 된 상태로 Azure Cosmos DB 데이터베이스에 저장 됩니다.

`config-agent`클러스터에서 실행 되는는 다음을 담당 합니다.
* `sourceControlConfiguration`Azure Arc Enabled Kubernetes 리소스에서 새로운 또는 업데이트 된 확장 리소스를 추적 합니다.
* Flux 연산자를 배포 하 여 각에 대 한 Git 리포지토리를 시청 `sourceControlConfiguration` 합니다.
* 모든 업데이트 적용 `sourceControlConfiguration` . 

`sourceControlConfiguration`다중 테 넌 트를 얻기 위해 동일한 Azure Arc 사용 Kubernetes 클러스터에서 여러 리소스를 만들 수 있습니다. 각 네임 스페이스 내에서 서로 다른 범위를 만들어 배포를 제한 `sourceControlConfiguration` `namespace` 합니다.

Git 리포지토리에는 다음이 포함 될 수 있습니다.
* 네임 스페이스, ConfigMaps, 배포, DaemonSets 등을 비롯 한 모든 유효한 Kubernetes 리소스를 설명 하는 YAML 형식 매니페스트 
* 응용 프로그램 배포를 위한 투구 차트 

일반적인 시나리오 집합에는 일반적인 Azure 역할 및 바인딩, 모니터링 또는 로깅 에이전트 또는 클러스터 전체 서비스와 같은 조직의 기준 구성 정의를 포함 합니다.

동일한 패턴을 사용 하 여 더 큰 클러스터 컬렉션을 관리할 수 있습니다 .이는 다른 유형의 환경에서 배포 될 수 있습니다. 예를 들어 한 번에 여러 Kubernetes 클러스터에 적용 되는 조직의 기준 구성을 정의 하는 리포지토리가 하나 있습니다. [Azure Policy](use-azure-policy.md) 는 `sourceControlConfiguration` 범위 (구독 또는 리소스 그룹) 내에서 모든 Azure Arc 사용 Kubernetes 리소스에 대 한 특정 매개 변수 집합을 사용 하 여 만들기를 자동화할 수 있습니다.

범위를 사용 하 여 구성 집합을 적용 하는 방법을 알아보려면 다음 단계를 수행 `cluster-admin` 합니다.

## <a name="before-you-begin"></a>시작하기 전에

기존 Azure Arc enabled Kubernetes 연결 된 클러스터가 있는지 확인 합니다. 연결 된 클러스터가 필요한 경우 [연결 a Azure Arc Enabled Kubernetes cluster 빠른](./connect-cluster.md)시작을 참조 하세요.

## <a name="create-a-configuration"></a>구성 만들기

이 문서에 사용 된 [예제 리포지토리](https://github.com/Azure/arc-k8s-demo) 는 몇 가지 네임 스페이스를 프로 비전 하 고, 일반적인 워크 로드를 배포 하 고, 몇 가지 팀 특정 구성을 제공 하고자 하는 클러스터 연산자의 가상 사용자를 중심으로 구성 됩니다. 이 리포지토리를 사용하면 클러스터에 다음 리소스가 만들어집니다.


* **네임 스페이스:** `cluster-config` , `team-a` , `team-b`
* **배포:**`cluster-config/azure-vote`
* **Configmap:**`team-a/endpoints`

는 `config-agent` 새로운 또는 업데이트를 위해 Azure를 폴링합니다 `sourceControlConfiguration` . 이 작업에는 최대 30 초가 소요 됩니다.

개인 리포지토리를와 연결 하는 경우 `sourceControlConfiguration` [개인 Git 리포지토리에서 구성 적용](#apply-configuration-from-a-private-git-repository)의 단계를 완료 합니다.

### <a name="using-azure-cli"></a>Azure CLI 사용

의 Azure CLI 확장을 사용 `k8sconfiguration` 하 여 연결 된 클러스터를 [예제 Git 리포지토리에](https://github.com/Azure/arc-k8s-demo)연결 합니다. 
1. 이 구성의 이름을로 설정 `cluster-config` 합니다.
1. 네임 스페이스에 연산자를 배포 하도록 에이전트에 지시 합니다 `cluster-config` .
1. 운영자 권한을 부여 `cluster-admin` 합니다.

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**출력:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Pending",
    "lastConfigApplied": "0001-01-01T00:00:00",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": null,
  "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
  "resourceGroup": "MyRG",
  "sshKnownHostsContents": "",
  "systemData": {
    "createdAt": "2020-11-24T21:22:01.542801+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
  ```

#### <a name="use-a-public-git-repo"></a>공용 Git 리포지토리 사용

| 매개 변수 | 형식 |
| ------------- | ------------- |
| `--repository-url` | http [s]:/server/ss [. git] 또는 git:/server/wers [. git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>SSH 및 Flux에서 만든 키로 개인 Git 리포지토리 사용

| 매개 변수 | 형식 | 참고
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. git] 또는 user@server:repo [. git] | `git@` 바꿀 수 있습니다. `user@`

> [!NOTE]
> Flux에서 생성 된 공개 키를 Git 서비스 공급자의 사용자 계정에 추가 해야 합니다. 키가 사용자 계정 대신 리포지토리에 추가 되 면 URL에서 대신를 사용 `git@` `user@` 합니다. 자세한 내용은 [개인 Git 리포지토리에서 구성 적용](#apply-configuration-from-a-private-git-repository) 섹션으로 이동 합니다.

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>SSH 및 사용자 제공 키가 포함 된 개인 Git 리포지토리 사용

| 매개 변수 | 형식 | 참고 |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] 또는 user@server:repo [. git] | `git@` 바꿀 수 있습니다. `user@` |
| `--ssh-private-key` | [PEM 형식의](https://aka.ms/PEMformat) base64 인코딩 키 | 키 직접 제공 |
| `--ssh-private-key-file` | 로컬 파일의 전체 경로 | PEM 형식 키를 포함 하는 로컬 파일의 전체 경로를 제공 합니다.

> [!NOTE]
> 직접 또는 파일에 사용자 고유의 개인 키를 제공 합니다. 키는 [PEM 형식](https://aka.ms/PEMformat) 이어야 하며 줄 바꿈 (\n)으로 끝나야 합니다.  Git 서비스 공급자의 사용자 계정에 연결 된 공개 키를 추가 해야 합니다. 키가 사용자 계정 대신 리포지토리에 추가 되는 경우 대신를 사용 `git@` `user@` 합니다. 자세한 내용은 [개인 Git 리포지토리에서 구성 적용](#apply-configuration-from-a-private-git-repository) 섹션으로 이동 합니다.

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>SSH 및 사용자가 제공한 알려진 호스트에서 개인 Git 호스트 사용

| 매개 변수 | 형식 | 참고 |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. git] 또는 user@server:repo [. git] | `git@` 바꿀 수 있습니다. `user@` |
| `--ssh-known-hosts` | base64 인코딩 | 알려진 호스트 콘텐츠 직접 제공 |
| `--ssh-known-hosts-file` | 로컬 파일의 전체 경로 | 알려진 호스트 콘텐츠를 로컬 파일에 제공 |

> [!NOTE]
> SSH 연결을 설정 하기 전에 Git 리포지토리를 인증 하기 위해 Flux 연산자는 알려진 호스트 파일의 일반 Git 호스트 목록을 유지 관리 합니다. 드물게 사용 되는 Git 리포지토리 또는 자체 Git 호스트를 사용 하는 경우 Flux가 리포지토리를 식별할 수 있도록 호스트 키를 제공 해야 할 수 있습니다. Known_hosts 콘텐츠를 직접 또는 파일에 제공할 수 있습니다. 사용자 고유의 콘텐츠를 제공 하는 경우 위에서 설명한 SSH 키 시나리오 중 하 나와 함께 [known_hosts 콘텐츠 형식 사양을](https://aka.ms/KnownHostsFormat) 사용 합니다.

#### <a name="use-a-private-git-repo-with-https"></a>HTTPS를 사용 하 여 개인 Git 리포지토리 사용

| 매개 변수 | 형식 | 참고 |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. git] | 기본 인증을 사용 하는 HTTPS |
| `--https-user` | 원시 또는 b a s e 64로 인코딩된 | HTTPS 사용자 이름 |
| `--https-key` | 원시 또는 b a s e 64로 인코딩된 | HTTPS 개인용 액세스 토큰 또는 암호

> [!NOTE]
> HTTPS 투구 릴리스 개인 인증은 투구 operator chart 버전 1.2.0 + (기본값) 에서만 지원 됩니다.
> HTTPS 투구 릴리스 개인 인증은 현재 Azure Kubernetes Services 관리 클러스터에 대해 지원 되지 않습니다.
> 프록시를 통해 Git 리포지토리에 액세스 하는 데 Flux가 필요한 경우 프록시 설정을 사용 하 여 Azure Arc 에이전트를 업데이트 해야 합니다. 자세한 내용은 [아웃 바운드 프록시 서버를 사용 하 여 연결](./connect-cluster.md#connect-using-an-outbound-proxy-server)을 참조 하세요.

#### <a name="additional-parameters"></a>추가 매개 변수

다음과 같은 선택적 매개 변수를 사용 하 여 구성을 사용자 지정 합니다.

| 매개 변수 | 설명 |
| ------------- | ------------- |
| `--enable-helm-operator`| 투구 차트 배포에 대 한 지원을 사용 하도록 전환 합니다. |
| `--helm-operator-params` | 투구 연산자 (설정 된 경우)에 대 한 차트 값입니다. 예들 들어 `--set helm.versions=v3`입니다. |
| `--helm-operator-version` | 투구 연산자의 차트 버전입니다 (설정 된 경우). 버전 1.2.0 +를 사용 합니다. 기본값: ' 1.2.0 '. |
| `--operator-namespace` | Operator 네임 스페이스의 이름입니다. 기본값: ' default '. 최대: 23 자. |
| `--operator-params` | 연산자의 매개 변수입니다. 작은따옴표 안에 지정해야 합니다. 예를 들어 ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ``` 

##### <a name="options-supported-in----operator-params"></a>에서 지원 되는 옵션  `--operator-params` :

| 옵션 | 설명 |
| ------------- | ------------- |
| `--git-branch`  | Kubernetes 매니페스트에 사용할 Git 리포지토리의 분기입니다. 기본값은 'master'입니다. 최신 리포지토리에는 이라는 루트 분기가 있습니다 `main` .이 경우를 설정 해야 `--git-branch=main` 합니다. |
| `--git-path`  | Kubernetes 매니페스트를 찾는 Flux에 대한 Git 리포지토리 내의 상대 경로입니다. |
| `--git-readonly` | Git 리포지토리는 읽기 전용으로 간주됩니다. Flux는 리포지토리에 쓰기를 시도하지 않습니다. |
| `--manifest-generation`  | 사용 설정된 경우 Flux는 .flux.yaml을 찾고 Kustomize 또는 기타 매니페스트 생성기를 실행합니다. |
| `--git-poll-interval`  | Git 리포지토리에서 새 커밋을 폴링하는 주기입니다. 기본값은 `5m` (5 분)입니다. |
| `--sync-garbage-collection`  | 사용 설정된 경우 Flux는 만들었지만 더 이상 Git에 존재하지 않는 리소스를 삭제합니다. |
| `--git-label`  | 동기화 진행률을 추적 하기 위한 레이블입니다. Git 분기에 태그를 사용 하는 데 사용 됩니다.  기본값은 `flux-sync`입니다. |
| `--git-user`  | Git 커밋에 대 한 사용자 이름입니다. |
| `--git-email`  | Git 커밋에 사용할 전자 메일입니다. 

Flux에서 리포지토리에 쓰거나 설정 하지 않으려는 경우가 `--git-user` `--git-email` `--git-readonly` 자동으로 설정 됩니다.

자세한 내용은 [Flux 설명서](https://aka.ms/FluxcdReadme)를 참조 하세요.

> [!TIP]
> `sourceControlConfiguration`Azure Arc Enabled Kubernetes 리소스의 **Gitops** 탭에 있는 Azure Portal에서를 만들 수 있습니다.

## <a name="validate-the-sourcecontrolconfiguration"></a>sourceControlConfiguration 유효성 검사

Azure CLI를 사용 하 여 `sourceControlConfiguration` 이 성공적으로 만들어졌는지 확인 합니다.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`리소스는 준수 상태, 메시지 및 디버깅 정보로 업데이트 됩니다.

**출력:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
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
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

`sourceControlConfiguration`가 만들어지거나 업데이트 되 면 내부적으로 몇 가지 작업이 수행 됩니다.

1. Azure Arc는 `config-agent` 새로운 구성 또는 업데이트 된 구성 ()에 대 한 Azure Resource Manager를 모니터링 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` 하 고 새 구성을 공지 합니다 `Pending` .
1. 는 `config-agent` 구성 속성을 읽고 대상 네임 스페이스를 만듭니다.
1. Azure Arc는 `controller-manager` 적절 한 권한 (또는 범위)을 사용 하 여 Kubernetes 서비스 계정을 준비한 `cluster` `namespace` 후 인스턴스를 배포 `flux` 합니다.
1. Flux에서 생성 된 키와 함께 SSH 옵션을 사용 하는 경우은 `flux` ssh 키를 생성 하 고 공개 키를 로깅합니다.
1. 는 `config-agent` Azure의 리소스로 상태를 보고 합니다 `sourceControlConfiguration` .

프로비전 프로세스가 진행되는 동안 `sourceControlConfiguration`이 몇 가지 상태 변경을 거칩니다. 위의 `az k8sconfiguration show ...` 명령을 사용하여 진행률을 모니터링합니다.

| 단계 변경 | 설명 |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | 초기 및 진행 중인 상태를 나타냅니다. |
| `complianceStatus` -> `Installed`  | `config-agent` 에서 클러스터를 성공적으로 구성 하 고 오류 없이 배포할 수 있습니다 `flux` . |
| `complianceStatus` -> `Failed` | `config-agent` 배포 오류가 발생 `flux` 하 여 응답 본문에서 세부 정보를 사용할 수 있습니다 `complianceStatus.message` . |

## <a name="apply-configuration-from-a-private-git-repository"></a>개인 Git 리포지토리에서 구성 적용

개인 Git 리포지토리를 사용 하는 경우 리포지토리의 SSH 공개 키를 구성 해야 합니다. SSH 공개 키는 Flux에서 생성 하는 키 또는 사용자가 제공 하는 키 중 하나입니다. 특정 Git 리포지토리 또는 리포지토리에 대 한 액세스 권한이 있는 Git 사용자에서 공개 키를 구성할 수 있습니다. 

### <a name="get-your-own-public-key"></a>사용자 고유의 공개 키 가져오기

사용자 고유의 SSH 키를 생성 한 경우에는 개인 키와 공개 키가 이미 있는 것입니다.

#### <a name="get-the-public-key-using-azure-cli"></a>Azure CLI를 사용하여 공개 키 가져오기 

Flux에서 키를 생성 하는 경우 다음을 유용 하 게 사용할 수 있습니다.

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Azure Portal에서 공개 키 가져오기

Flux에서 키를 생성 하는 경우 다음을 유용 하 게 사용할 수 있습니다.

1. Azure Portal에서 연결된 클러스터 리소스로 이동합니다.
2. 리소스 페이지에서 "GitOps"를 선택 하 고이 클러스터에 대 한 구성 목록을 확인 합니다.
3. 프라이빗 Git 리포지토리를 사용하는 구성을 선택합니다.
4. 열린 컨텍스트 창의 맨 아래에서 **리포지토리 공개 키** 를 복사합니다.

#### <a name="add-public-key-using-github"></a>GitHub를 사용 하 여 공개 키 추가

다음 옵션 중 하나를 사용합니다.

* 옵션 1: 사용자 계정에 공개 키를 추가 합니다 (계정의 모든 리포지토리에 적용 됨).  
    1. GitHub를 열고 페이지의 오른쪽 위 모퉁이에 있는 프로필 아이콘을 클릭 합니다.
    2. **설정** 을 클릭합니다.
    3. **SSH 및 GPG 키** 를 클릭 합니다.
    4. **새 SSH 키** 를 클릭 합니다.
    5. 제목을 입력 합니다.
    6. 바깥쪽 따옴표 없이 공개 키를 붙여넣습니다.
    7. **SSH 키 추가** 를 클릭 합니다.

* 옵션 2: 공개 키를 Git 리포지토리에 배포 키로 추가 합니다 (이 리포지토리에만 적용 됨).  
    1. GitHub를 열고 리포지토리로 이동 합니다.
    1. **설정** 을 클릭합니다.
    1. **키 배포** 를 클릭 합니다.
    1. **배포 키 추가** 를 클릭 합니다.
    1. 제목을 입력 합니다.
    1. **쓰기 액세스 허용** 을 선택 합니다.
    1. 바깥쪽 따옴표 없이 공개 키를 붙여넣습니다.
    1. **키 추가** 를 클릭 합니다.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Azure DevOps 리포지토리를 사용 하 여 공개 키 추가

SSH 키에 키를 추가 하려면 다음 단계를 사용 합니다.

1. 프로필 이미지 옆의 오른쪽 위에 있는 **사용자 설정** 에서 **SSH 공개 키** 를 클릭 합니다.
1. **+ 새 키** 를 선택 합니다.
1. 이름을 제공 합니다.
1. 바깥쪽 따옴표 없이 공개 키를 붙여넣습니다.
1. **추가** 를 클릭합니다.

## <a name="validate-the-kubernetes-configuration"></a>Kubernetes 구성의 유효성을 검사합니다.

`config-agent`에서 인스턴스를 설치한 후 `flux` Git 리포지토리에 저장 된 리소스는 클러스터로 전달 되기 시작 해야 합니다. 다음 명령을 사용 하 여 네임 스페이스, 배포 및 리소스를 만들었는지 확인 합니다.

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

다음을 사용 하 여 구성 리포지토리의 일부로 배포 된 다른 리소스를 탐색할 수 있습니다.

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>구성 삭제

`sourceControlConfiguration`Azure CLI 또는 Azure Portal를 사용 하 여를 삭제 합니다.  Delete 명령을 시작 하면 `sourceControlConfiguration` 리소스가 Azure에서 즉시 삭제 됩니다. 클러스터에서 연결 된 개체의 전체 삭제는 10 분 이내에 수행 되어야 합니다. `sourceControlConfiguration`이 제거 될 때가 실패 한 상태 이면 연결 된 개체를 완전히 삭제 하는 데 최대 한 시간이 걸릴 수 있습니다.

> [!NOTE]
> With scope를 만든 후에는 `sourceControlConfiguration` `namespace` `edit` 네임 스페이스에 대 한 역할 바인딩을 가진 사용자가이 네임 스페이스에 작업을 배포할 수 있습니다. `sourceControlConfiguration`네임 스페이스 범위를 사용 하 여이를 삭제 하는 경우 네임 스페이스는 그대로 유지 되므로 이러한 다른 작업의 중단을 방지 하기 위해 삭제 되지 않습니다. 필요한 경우를 사용 하 여이 네임 스페이스를 수동으로 삭제할 수 있습니다 `kubectl` .  
> 추적 된 Git 리포지토리에서 배포의 결과로 생성 된 클러스터에 대 한 변경 내용은이 삭제 될 때 삭제 되지 않습니다 `sourceControlConfiguration` .

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**출력:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>다음 단계

- [소스 제어 구성과 함께 투구 사용](./use-gitops-with-helm.md)
- [Azure Policy를 사용하여 클러스터 구성 관리](./use-azure-policy.md)