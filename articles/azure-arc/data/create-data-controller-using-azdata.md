---
title: Azure Data CLI(azdata)를 사용하여 데이터 컨트롤러 만들기
description: Azure Data CLI(azdata)를 사용하여 이미 만든 일반 다중 노드 Kubernetes 클러스터에 Azure Arc 데이터 컨트롤러를 만듭니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: f7bc90f2748d230ad50868cff5d7a8f7b69d850a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029542"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]를 사용하여 Azure Arc 데이터 컨트롤러 만들기

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>필수 구성 요소

개요 정보는 [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md) 토픽을 검토하세요.

[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]를 사용하여 Azure Arc 데이터 컨트롤러를 만들려면 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]가 설치되어 있어야 합니다.

   [[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 설치](install-client-tools.md)

선택한 대상 플랫폼에 관계없이 데이터 컨트롤러 관리자 사용자를 만들기 전에 다음 환경 변수를 설정해야 합니다. 필요에 따라 데이터 컨트롤러에 대한 관리자 권한이 필요한 다른 사용자에게 이러한 자격 증명을 제공할 수 있습니다.

**AZDATA_USERNAME** - 데이터 컨트롤러 관리자 사용자에 대해 선택한 사용자 이름입니다. 예: `arcadmin`

**AZDATA_PASSWORD** - 데이터 컨트롤러 관리자 사용자에 대해 선택한 암호입니다. 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 기호 등의 4가지 집합 중 3가지 집합의 문자를 포함해야 합니다.

### <a name="linux-or-macos"></a>Linux 또는 macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Azure Arc 데이터 컨트롤러를 만들기 전에 Kubernetes 클러스터에 연결하여 인증하고 기존 Kubernetes 컨텍스트를 선택해야 합니다. Kubernetes 클러스터 또는 서비스에 연결하는 방법은 다양합니다. Kubernetes API 서버에 연결하는 방법에 대해서는 사용 중인 Kubernetes 배포 또는 서비스에 대한 문서를 참조하세요.

현재 Kubernetes 연결이 있는지 확인하고 다음 명령을 사용하여 현재 컨텍스트를 확인할 수 있습니다.

```console
kubectl get namespace
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기

> [!NOTE]
> 아래 예에서는 zdata arch dc create 명령의 `--namespace` 매개 변수에 다른 값을 사용할 수 있지만, 아래의 다른 모든 명령에서는 `--namespace parameter`에 해당 네임스페이스 이름을 사용해야 합니다.

- [AKS(Azure Kubernetes Service)에서 만들기](#create-on-azure-kubernetes-service-aks)
- [Azure Stack Hub의 AKS 엔진에서 만들기](#create-on-aks-engine-on-azure-stack-hub)
- [Azure Stack HCI의 AKS에서 만들기](#create-on-aks-on-azure-stack-hci)
- [ARO(Azure Red Hat OpenShift)에서 만들기](#create-on-azure-red-hat-openshift-aro)
- [Red Hat OCP(OpenShift Container Platform)에서 만들기](#create-on-red-hat-openshift-container-platform-ocp)
- [오픈 소스, 업스트림 Kubernetes(kubeadm)에서 만들기](#create-on-open-source-upstream-kubernetes-kubeadm)
- [AWS EKS(Elastic Kubernetes Service)에서 만들기](#create-on-aws-elastic-kubernetes-service-eks)
- [GKE(Google Cloud Kubernetes Engine) 서비스에서 만들기](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 만들기

기본적으로 AKS 배포 프로필은 `managed-premium` 스토리지 클래스를 사용합니다. `managed-premium` 스토리지 클래스는 프리미엄 디스크가 있는 VM 이미지를 사용하여 배포된 VM이 있는 경우에만 작동합니다.

`managed-premium`을 스토리지 클래스로 사용하려는 경우 다음 명령을 실행하여 데이터 컨트롤러를 만들 수 있습니다. 명령의 자리 표시자를 리소스 그룹 이름, 구독 ID 및 Azure 위치로 대체합니다.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

사용할 스토리지 클래스가 확실하지 않은 경우 사용 중인 VM 유형에 관계없이 지원되는 `default` 스토리지 클래스를 사용해야 합니다. 가장 빠른 성능을 제공하지는 않습니다.

`default` 스토리지 클래스를 사용하려는 경우 다음 명령을 실행할 수 있습니다.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitoring-the-creation-status)으로 계속 진행합니다.

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Azure Stack Hub의 AKS 엔진에서 만들기

기본적으로 배포 프로필은 `managed-premium` 스토리지 클래스를 사용합니다. `managed-premium` 스토리지 클래스는 Azure Stack Hub에 프리미엄 디스크가 있는 VM 이미지를 사용하여 배포된 작업자 VM이 있는 경우에만 작동합니다.

다음 명령을 실행하여 관리형 프리미엄 스토리지 클래스를 사용하여 데이터 컨트롤러를 만들 수 있습니다.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

사용할 스토리지 클래스가 확실하지 않은 경우 사용 중인 VM 유형에 관계없이 지원되는 `default` 스토리지 클래스를 사용해야 합니다. Azure Stack Hub에서 프리미엄 디스크와 표준 디스크는 동일한 스토리지 인프라에서 지원됩니다. 따라서 동일한 일반 성능을 제공하지만 IOPS 제한이 있을 것으로 예상됩니다.

`default` 스토리지 클래스를 사용하려는 경우 다음 명령을 실행할 수 있습니다.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitoring-the-creation-status)으로 계속 진행합니다.

### <a name="create-on-aks-on-azure-stack-hci"></a>Azure Stack HCI의 AKS에서 만들기

기본적으로 배포 프로필은 `default`라는 스토리지 클래스와 서비스 유형 `LoadBalancer`를 사용합니다.

다음 명령을 실행하여 `default` 스토리지 클래스와 서비스 유형 `LoadBalancer`를 사용하여 데이터 컨트롤러를 만들 수 있습니다.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitoring-the-creation-status)으로 계속 진행합니다.


### <a name="create-on-azure-red-hat-openshift-aro"></a>ARO(Azure Red Hat OpenShift)에서 만들기

Azure Red Hat OpenShift에는 보안 컨텍스트 제약 조건이 필요합니다.

#### <a name="apply-the-security-context"></a>보안 컨텍스트 설정

Azure Red Hat OpenShift에서 데이터 컨트롤러를 만들기 전에 특정 SCC(보안 컨텍스트 제약)를 적용해야 합니다. 미리 보기 릴리스의 경우 이를 통해 보안 제약 조건이 완화됩니다. 이후 릴리스에서는 업데이트된 SCC를 제공합니다.

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="create-custom-deployment-profile"></a>사용자 지정 배포 프로필 만들기

Azure Red Hat OpenShift에 `azure-arc-azure-openshift` 프로필을 사용합니다.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>데이터 컨트롤러 만들기

다음 명령을 실행하여 데이터 컨트롤러를 만들 수 있습니다.

> [!NOTE]
> 여기와 위의 `oc adm policy add-scc-to-user` 명령에 동일한 네임스페이스를 사용합니다. 예를 들면 `arc`입니다.

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitoring-the-creation-status)으로 계속 진행합니다.

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Red Hat OCP(OpenShift Container Platform)에서 만들기

> [!NOTE]
> Azure에서 Red Hat OpenShift Container Platform을 사용하는 경우, 사용 가능한 최신 버전을 사용하는 것이 좋습니다.

Red Hat OCP에서 데이터 컨트롤러를 만들기 전에 특정 보안 컨텍스트 제약 조건을 적용해야 합니다. 

#### <a name="apply-the-security-context-constraint"></a>보안 컨텍스트 제약 조건 적용

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="determine-storage-class"></a>스토리지 클래스 결정

또한 다음 명령을 실행하여 사용할 스토리지 클래스를 결정해야 합니다.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>사용자 지정 배포 프로필 만들기

다음 명령을 실행하여 `azure-arc-openshift` 배포 프로필을 기반으로 새 사용자 지정 배포 프로필 파일을 만듭니다. 이 명령은 현재 작업 디렉터리에 `custom` 디렉터리를 만들고 해당 디렉터리에 사용자 지정 배치 프로필 파일 `control.json`을 만듭니다.

OpenShift Container Platform에 `azure-arc-openshift` 프로필을 사용합니다.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>스토리지 클래스 설정 

이제 아래 명령의 `<storageclassname>`을 위의 `kubectl get storageclass` 명령을 실행하여 결정된 스토리지 클래스의 이름으로 대체하여 원하는 스토리지 클래스를 설정합니다.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>LoadBalancer 설정(선택 사항)

기본적으로 `azure-arc-openshift` 배포 프로필은 `NodePort`를 서비스 유형으로 사용합니다. 부하 분산 장치와 통합된 OpenShift 클러스터를 사용하는 경우 다음 명령을 사용하여 `LoadBalancer` 서비스 유형을 사용하도록 구성을 변경할 수 있습니다.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>보안 정책 확인

OpenShift를 사용할 때 OpenShift의 기본 보안 정책으로 실행하거나 일반적으로 환경을 일반적인 환경보다 많이 잠그려고 할 수 있습니다. 선택적으로 다음 명령을 실행하여 배포 시간 및 런타임에 필요한 권한을 최소화하기 위해 일부 기능을 사용하지 않도록 선택할 수 있습니다.

이 명령은 Pod에 대한 메트릭 수집을 사용하지 않도록 설정합니다. 이 기능을 사용하지 않으면 Grafana 대시보드에서 Pod에 대한 메트릭을 볼 수 없습니다. 기본값은 true입니다.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

이 명령은 노드에 대한 메트릭 수집을 사용하지 않도록 설정합니다. 이 기능을 사용하지 않으면 Grafana 대시보드에서 노드에 대한 메트릭을 볼 수 없습니다. 기본값은 true입니다.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

이 명령은 문제 해결을 위해 메모리 덤프를 가져오는 기능을 사용하지 않도록 설정합니다.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>데이터 컨트롤러 만들기

이제 다음 명령을 사용하여 데이터 컨트롤러를 만들 준비가 되었습니다.

> [!NOTE]
>   여기와 위의 `oc adm policy add-scc-to-user` 명령에 동일한 네임스페이스를 사용합니다. 예를 들면 `arc`입니다.

> [!NOTE]
>   `--path` 매개 변수는 control.json 파일 자체가 아니라 control.json 파일이 포함된 _디렉터리_ 를 가리켜야 합니다.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitoring-the-creation-status)으로 계속 진행합니다.

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>오픈 소스, 업스트림 Kubernetes(kubeadm)에서 만들기

기본적으로 kubeadm 배치 프로파일은 `local-storage`라는 스토리지 클래스와 서비스 유형 `NodePort`를 사용합니다. 이것이 허용되는 경우 원하는 스토리지 클래스 및 서비스 유형을 설정하는 아래 지침을 건너뛰고 바로 아래의 `azdata arc dc create` 명령을 실행할 수 있습니다.

특정 스토리지 클래스 및/또는 서비스 유형을 지정하도록 배포 프로필을 사용자 지정하려면 다음 명령을 실행하여 kubeadm 배포 프로필을 기반으로 새 사용자 지정 배포 프로필 파일을 만듭니다. 이 명령은 현재 작업 디렉터리에 `custom` 디렉터리를 만들고 해당 디렉터리에 사용자 지정 배치 프로필 파일 `control.json`을 만듭니다.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

다음 명령을 실행하여 사용 가능한 스토리지 클래스를 조회할 수 있습니다.

```console
kubectl get storageclass
```

이제 아래 명령의 `<storageclassname>`을 위의 `kubectl get storageclass` 명령을 실행하여 결정된 스토리지 클래스의 이름으로 대체하여 원하는 스토리지 클래스를 설정합니다.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

기본적으로 kubeadm 배포 프로필은 `NodePort`를 서비스 유형으로 사용합니다. 부하 분산 장치와 통합된 Kubernetes 클러스터를 사용하는 경우 다음 명령을 사용하여 구성을 변경할 수 있습니다.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

이제 다음 명령을 사용하여 데이터 컨트롤러를 만들 준비가 되었습니다.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitoring-the-creation-status)으로 계속 진행합니다.

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>AWS EKS(Elastic Kubernetes Service)에서 만들기

기본적으로 EKS 스토리지 클래스는 `gp2`이고 서비스 유형은 `LoadBalancer`입니다.

제공된 EKS 배포 프로필을 사용하여 데이터 컨트롤러를 만들려면 다음 명령을 실행합니다.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitoring-the-creation-status)으로 계속 진행합니다.

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>GKE(Google Cloud Kubernetes Engine) 서비스에서 만들기

기본적으로 GKE 스토리지 클래스는 `standard`이고 서비스 유형은 `LoadBalancer`입니다.

제공된 GKE 배포 프로필을 사용하여 데이터 컨트롤러를 만들려면 다음 명령을 실행합니다.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행한 후 [만들기 상태 모니터링](#monitoring-the-creation-status)으로 계속 진행합니다.

## <a name="monitoring-the-creation-status"></a>만들기 상태 모니터링

컨트롤러 만들기를 완료하려면 몇 분 정도 걸립니다. 다음 명령을 사용하여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래 예제 명령에서는 `arc`라는 이름으로 데이터 컨트롤러 및 Kubernetes 네임스페이스를 만들었다고 가정합니다. 다른 네임스페이스/데이터 컨트롤러 이름을 사용한 경우 `arc`를 해당 이름으로 바꿀 수 있습니다.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

아래와 같은 명령을 실행하여 특정 Pod의 생성 상태를 확인할 수도 있습니다. 이 기능은 문제를 해결하는 데 특히 유용합니다.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>만들기 문제 해결

만들기 관련 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조하세요.