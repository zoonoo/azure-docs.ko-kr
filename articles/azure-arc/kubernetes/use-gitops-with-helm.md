---
title: Kubernetes 클러스터에서 GitOps를 사용 하 여 투구 차트 배포 (미리 보기)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc 사용 클러스터 구성(미리 보기)을 위한 Helm에서 GitOps 사용
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: eea81d458ac6631c4a023134b3198e4cdb04526e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541614"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Kubernetes 클러스터에서 GitOps를 사용 하 여 투구 차트 배포 (미리 보기)

Helm은 Kubernetes 애플리케이션을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. APT, Yum 등의 Linux 패키지 관리자와 마찬가지로, 투구는 미리 구성 된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리 하는 데 사용 됩니다.

이 문서에서는 Azure Arc 사용 Kubernetes에서 Helm을 구성하고 사용하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 Azure Arc 사용 Kubernetes 연결 클러스터가 있다고 가정합니다. 연결된 클러스터가 필요한 경우 [클러스터 연결 빠른 시작](./connect-cluster.md)을 참조하세요.

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Azure Arc를 사용 하는 Kubernetes를 사용 하 여 GitOps 및 투구 사용에 대 한 개요

 Helm 연산자는 Helm 차트 릴리스를 자동화하는 Flux에 대한 확장을 제공합니다. 차트 릴리스는 HelmRelease라는 Kubernetes 사용자 지정 리소스를 통해 설명됩니다. Flux는 Git에서 클러스터로 이러한 리소스를 동기화하고, Helm 연산자는 리소스에 지정된 대로 Helm 차트가 릴리스되도록 합니다.

 이 문서에 사용 된 [예제 리포지토리](https://github.com/Azure/arc-helm-demo) 는 다음과 같은 방식으로 구성 됩니다.

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

Git 리포지토리에는 투구 차트를 포함 하는 디렉터리와 릴리스 구성을 포함 하는 두 개의 디렉터리가 있습니다. 디렉터리에서 `releases` 에는 `app.yaml` 아래에 표시 된 HelmRelease 구성이 포함 되어 있습니다.

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Helm 릴리스 구성에는 다음 필드가 포함되어 있습니다.

- `metadata.name`은 필수이며 Kubernetes 명명 규칙을 따라야 합니다.
- `metadata.namespace`는 선택 사항이며 릴리스가 생성되는 위치를 결정합니다.
- `spec.releaseName`은 선택 사항이며, 제공되지 않은 경우 릴리스 이름은 $namespace-$name이 됩니다.
- `spec.chart.path`는 차트를 포함하는 디렉터리로, 리포지토리 루트를 기준으로 지정됩니다.
- `spec.values`는 차트 자체의 기본 매개 변수 값에 대한 사용자 사용자 지정입니다.

HelmRelease spec.values에 지정된 옵션은 차트 원본의 values.yaml에 지정된 옵션을 재정의합니다.

HelmRelease에 대한 자세한 내용은 공식 [Helm 연산자 설명서](https://docs.fluxcd.io/projects/helm-operator/en/stable/)에서 확인할 수 있습니다.

## <a name="create-a-configuration"></a>구성 만들기

`k8sconfiguration`에 대한 Azure CLI 확장을 사용하여 연결된 클러스터를 예제 Git 리포지토리에 연결해 보겠습니다. 이 구성 이름을 `azure-arc-sample`으로 지정하고 `arc-k8s-demo` 네임스페이스에 Flux 연산자를 배포합니다.

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>구성 매개 변수

구성 만들기를 사용자 지정하려면 [사용할 수 있는 추가 매개 변수에 대해 자세히 알아보세요](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>구성 유효성 검사

Azure CLI를 사용하여 `sourceControlConfiguration`이 성공적으로 만들어졌는지 확인합니다.

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`리소스는 준수 상태, 메시지 및 디버깅 정보로 업데이트 됩니다.

**출력:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>애플리케이션 유효성 검사

다음 명령을 실행 하 고 브라우저에서로 이동 하 여 `localhost:8080` 응용 프로그램이 실행 중인지 확인 합니다.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy를 사용하여 클러스터 구성 관리](./use-azure-policy.md)
