---
title: Azure Data CLI를 사용 하 여 데이터 컨트롤러 만들기 (azdata)
description: Azdata (Azure Data CLI)를 사용 하 여 이미 만든 일반적인 다중 노드 Kubernetes 클러스터에서 Azure Arc 데이터 컨트롤러를 만듭니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a405a7a32b1f54732589fb14e34ff52db4992028
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761688"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Azure Data CLI를 사용 하 여 Azure Arc 데이터 컨트롤러 만들기 (azdata)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>필수 구성 요소

개요 정보는 [Azure Arc data Controller 만들기](create-data-controller.md) 항목을 검토 하세요.

Azure 데이터 CLI를 사용 하 여 azure Arc 데이터 컨트롤러를 만들려면 Azure Data CLI가 설치 되어 있어야 합니다.

   [Azure 데이터 CLI 설치](install-client-tools.md)

선택한 대상 플랫폼에 관계 없이 데이터 컨트롤러 관리자 사용자를 만들기 전에 다음 환경 변수를 설정 해야 합니다. 필요에 따라 데이터 컨트롤러에 대 한 관리자 액세스 권한을 보유 해야 하는 다른 사용자에 게 이러한 자격 증명을 제공할 수 있습니다.

**AZDATA_USERNAME** -데이터 컨트롤러 관리자 사용자에 대해 선택한 사용자 이름입니다. 예: `arcadmin`

**AZDATA_PASSWORD** -데이터 컨트롤러 관리자 사용자에 대해 선택한 암호입니다. 암호는 8 자 이상 이어야 하며 대문자, 소문자, 숫자 및 기호 중 세 가지를 포함 하는 다음 네 가지 문자 집합의 문자를 포함 해야 합니다.

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

Kubernetes 클러스터에 연결 하 여 인증 하 고 Azure Arc 데이터 컨트롤러 만들기를 시작 하기 전에 기존 Kubernetes 컨텍스트를 선택 해야 합니다. Kubernetes 클러스터 또는 서비스에 연결 하는 방법은 다양 합니다. Kubernetes API 서버에 연결 하는 방법에 사용 하는 Kubernetes 배포 또는 서비스에 대 한 설명서를 참조 하세요.

현재 Kubernetes 연결이 있는지 확인 하 고 다음 명령을 사용 하 여 현재 컨텍스트를 확인할 수 있습니다.

```console
kubectl get namespace

kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기

> [!NOTE]
> `--namespace`아래 예제에서 azdata arc dc create 명령의 매개 변수에 다른 값을 사용할 수 있지만 아래에 있는 `--namespace parameter` 다른 모든 명령의에는이 네임 스페이스 이름을 사용 해야 합니다.

만들기를 구성 하려면 대상 플랫폼에 따라 아래 해당 섹션을 따르세요.

[Azure Kubernetes 서비스 (AKS)에서 만들기](#create-on-azure-kubernetes-service-aks)

[Azure Stack Hub의 AKS 엔진에서 만들기](#create-on-aks-engine-on-azure-stack-hub)

[Azure Stack HCI의 AKS에서 만들기](#create-on-aks-on-azure-stack-hci)

[Azure Red Hat OpenShift (ARO)에서 만들기](#create-on-azure-red-hat-openshift-aro)

[Red Hat OpenShift Container Platform (OCP)에서 만들기](#create-on-red-hat-openshift-container-platform-ocp)

[오픈 소스에서 만들기, 업스트림 Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)

[EKS (AWS 탄력적 Kubernetes Service)에서 만들기](#create-on-aws-elastic-kubernetes-service-eks)

[Google Cloud Kubernetes Engine 서비스 (GKE)에서 만들기](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에서 만들기

기본적으로 AKS 배포 프로필은 저장소 클래스를 사용 합니다 `managed-premium` . `managed-premium`저장소 클래스는 프리미엄 디스크를 포함 하는 vm 이미지를 사용 하 여 배포 된 vm이 있는 경우에만 작동 합니다.

저장소 클래스로를 사용 하려는 경우 `managed-premium` 다음 명령을 실행 하 여 데이터 컨트롤러를 만들 수 있습니다. 명령의 자리 표시자를 리소스 그룹 이름, 구독 ID 및 Azure 위치로 대체 합니다.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

사용할 저장소 클래스를 모를 경우 `default` 사용 중인 VM 유형에 관계 없이 지원 되는 저장소 클래스를 사용 해야 합니다. 가장 빠른 성능을 제공 하지는 않습니다.

저장소 클래스를 사용 하려는 경우 `default` 다음 명령을 실행할 수 있습니다.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행 한 후에는 계속 해 서 [만들기 상태를 모니터링](#monitoring-the-creation-status)합니다.

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Azure Stack Hub의 AKS 엔진에서 만들기

기본적으로 배포 프로필은 저장소 클래스를 사용 합니다 `managed-premium` . `managed-premium`저장소 클래스는 Azure Stack 허브에 프리미엄 디스크가 있는 VM 이미지를 사용 하 여 배포 된 작업자 vm이 있는 경우에만 작동 합니다.

다음 명령을 실행 하 여 관리 되는 프리미엄 저장소 클래스를 사용 하 여 데이터 컨트롤러를 만들 수 있습니다.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

사용할 저장소 클래스를 모를 경우 `default` 사용 중인 VM 유형에 관계 없이 지원 되는 저장소 클래스를 사용 해야 합니다. Azure Stack 허브에서 프리미엄 디스크와 표준 디스크는 동일한 저장소 인프라에서 지원 됩니다. 따라서 동일한 일반 성능을 제공할 것으로 예상 되지만 IOPS 제한이 서로 다릅니다.

저장소 클래스를 사용 하려는 경우 `default` 이 명령을 실행할 수 있습니다.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행 한 후에는 계속 해 서 [만들기 상태를 모니터링](#monitoring-the-creation-status)합니다.

### <a name="create-on-aks-on-azure-stack-hci"></a>Azure Stack HCI의 AKS에서 만들기

기본적으로 배포 프로필은 라는 저장소 클래스 `default` 와 서비스 유형을 사용 합니다 `LoadBalancer` .

다음 명령을 실행 하 여 `default` 저장소 클래스 및 서비스 유형을 사용 하 여 데이터 컨트롤러를 만들 수 있습니다 `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행 한 후에는 계속 해 서 [만들기 상태를 모니터링](#monitoring-the-creation-status)합니다.


### <a name="create-on-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO)에서 만들기

Azure Red Hat OpenShift에서 데이터 컨트롤러를 만들려면 클러스터에 대해 다음 명령을 실행 하 여 보안 제약 조건을 완화 해야 합니다. 이는 나중에 제거 될 임시 요구 사항입니다.
> [!NOTE]
>   아래 명령에서 같은 네임 스페이스를 사용 `azdata arc dc create` 합니다. 예를 들면 `arc` 입니다.

먼저 [GitHub](https://github.com/microsoft/azure_arc/tree/master/arc_data_services/deploy/yaml) 에서 사용자 지정 SCC (보안 컨텍스트 제약 조건)를 다운로드 하 여 클러스터에 적용 합니다.

다음 명령을 실행 하 여 데이터 컨트롤러를 만들 수 있습니다.
> [!NOTE]
>   위의 명령에서 같은 네임 스페이스를 사용 합니다 `oc adm policy add-scc-to-user` . 예를 들면 `arc` 입니다.

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행 한 후에는 계속 해 서 [만들기 상태를 모니터링](#monitoring-the-creation-status)합니다.

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Red Hat OpenShift Container Platform (OCP)에서 만들기


> [!NOTE]
> Azure에서 Red Hat OpenShift Container Platform을 사용 하는 경우 사용 가능한 최신 버전을 사용 하는 것이 좋습니다.

Red Hat OpenShift Container Platform에서 데이터 컨트롤러를 만들려면 클러스터에 대해 다음 명령을 실행 하 여 보안 제약 조건을 완화 해야 합니다. 이는 나중에 제거 될 임시 요구 사항입니다.
> [!NOTE]
>   아래 명령에서 같은 네임 스페이스를 사용 `azdata arc dc create` 합니다. 예를 들면 `arc` 입니다.

```console
oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
```

또한 다음 명령을 실행 하 여 사용할 저장소 클래스를 결정 해야 합니다.

```console
kubectl get storageclass
```

먼저, 다음 명령을 실행 하 여 azure-arc-openshift 배포 프로필을 기반으로 새 사용자 지정 배포 프로필 파일을 만듭니다. 이 명령은 `custom` 현재 작업 디렉터리에 디렉터리를 만들고 `control.json` 해당 디렉터리에 사용자 지정 배포 프로필 파일을 만듭니다.

`azure-arc-openshift`OpenShift Container Platform에 대 한 프로필을 사용 합니다.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```
`azure-arc-azure-openshift`Open Shift에 Azure RedHat 프로필을 사용 합니다.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

이제 `<storageclassname>` 위의 명령을 실행 하 여 확인 된 사용 하려는 저장소 클래스의 이름으로 아래 명령에서 대체 하 여 원하는 저장소 클래스를 설정 합니다 `kubectl get storageclass` .

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

기본적으로 azure-arc-openshift 배포 프로필은를 `NodePort` 서비스 유형으로 사용 합니다. 부하 분산 장치와 통합 된 OpenShift 클러스터를 사용 하는 경우 다음 명령을 사용 하 여 LoadBalancer 서비스 유형을 사용 하도록 구성을 변경할 수 있습니다.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

OpenShift를 사용 하는 경우 OpenShift에서 기본 보안 정책을 사용 하 여 실행 하거나 일반적으로 환경을 일반적으로 잠그는 것이 좋습니다. 필요에 따라 다음 명령을 실행 하 여 배포 시간 및 런타임에 필요한 사용 권한을 최소화 하기 위해 일부 기능을 사용 하지 않도록 선택할 수 있습니다.

이 명령은 pod에 대 한 메트릭 컬렉션을 사용 하지 않도록 설정 합니다. 이 기능을 사용 하지 않도록 설정 하는 경우 Grafana 대시보드의 pod에 대 한 메트릭을 볼 수 없습니다. 기본값은 true입니다.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

이 명령은 노드에 대 한 메트릭 컬렉션을 사용 하지 않도록 설정 합니다. 이 기능을 사용 하지 않도록 설정 하는 경우 Grafana 대시보드의 노드에 대 한 메트릭을 볼 수 없습니다. 기본값은 true입니다.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

이 명령은 문제 해결을 위해 메모리 덤프를 가져오는 기능을 사용 하지 않도록 설정 합니다.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

이제 다음 명령을 사용 하 여 데이터 컨트롤러를 만들 준비가 되었습니다.
> [!NOTE]
>   위의 명령에서 같은 네임 스페이스를 사용 합니다 `oc adm policy add-scc-to-user` . 예를 들면 `arc` 입니다.

> [!NOTE]
>   `--path`매개 변수는 파일 자체의 control.js가 아닌 파일의 control.js포함 된 _디렉터리_ 를 가리켜야 합니다.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행 한 후에는 계속 해 서 [만들기 상태를 모니터링](#monitoring-the-creation-status)합니다.

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>오픈 소스에서 만들기, 업스트림 Kubernetes (kubeadm)

기본적으로 kubeadm 배포 프로필은 및 서비스 유형 이라는 저장소 클래스를 사용 합니다 `local-storage` `NodePort` . 이 경우 적절 한 저장소 클래스 및 서비스 유형을 설정 하 고 아래 명령을 즉시 실행 하는 아래 지침을 건너뛸 수 있습니다 `azdata arc dc create` .

배포 프로필을 사용자 지정 하 여 특정 저장소 클래스 및/또는 서비스 유형을 지정 하려면 다음 명령을 실행 하 여 kubeadm 배포 프로필을 기반으로 하는 새 사용자 지정 배포 프로필 파일을 만들어 시작 합니다. 이 명령은 `custom` 현재 작업 디렉터리에 디렉터리를 만들고 `control.json` 해당 디렉터리에 사용자 지정 배포 프로필 파일을 만듭니다.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

다음 명령을 실행 하 여 사용 가능한 저장소 클래스를 조회할 수 있습니다.

```console
kubectl get storageclass
```

이제 `<storageclassname>` 위의 명령을 실행 하 여 확인 된 사용 하려는 저장소 클래스의 이름으로 아래 명령에서 대체 하 여 원하는 저장소 클래스를 설정 합니다 `kubectl get storageclass` .

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

기본적으로 kubeadm 배포 프로필은를 `NodePort` 서비스 유형으로 사용 합니다. 부하 분산 장치와 통합 된 Kubernetes 클러스터를 사용 하는 경우 다음 명령을 사용 하 여 구성을 변경할 수 있습니다.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

이제 다음 명령을 사용 하 여 데이터 컨트롤러를 만들 준비가 되었습니다.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행 한 후에는 계속 해 서 [만들기 상태를 모니터링](#monitoring-the-creation-status)합니다.

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>EKS (AWS 탄력적 Kubernetes Service)에서 만들기

기본적으로 EKS 저장소 클래스는이 `gp2` 고 서비스 형식은 `LoadBalancer` 입니다.

다음 명령을 실행 하 여 제공 된 EKS 배포 프로필을 사용 하 여 데이터 컨트롤러를 만듭니다.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행 한 후에는 계속 해 서 [만들기 상태를 모니터링](#monitoring-the-creation-status)합니다.

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Google Cloud Kubernetes Engine 서비스 (GKE)에서 만들기

기본적으로 GKE 저장소 클래스는이 `standard` 고 서비스 형식은 `LoadBalancer` 입니다.

다음 명령을 실행 하 여 제공 된 GKE 배포 프로필을 사용 하 여 데이터 컨트롤러를 만듭니다.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

명령을 실행 한 후에는 계속 해 서 [만들기 상태를 모니터링](#monitoring-the-creation-status)합니다.

## <a name="monitoring-the-creation-status"></a>만들기 상태 모니터링

컨트롤러를 만드는 과정을 완료 하는 데 몇 분 정도 걸립니다. 다음 명령을 사용 하 여 다른 터미널 창에서 진행률을 모니터링할 수 있습니다.

> [!NOTE]
>  아래 예제 명령은 이름이 인 데이터 컨트롤러 및 Kubernetes 네임 스페이스를 만들었다고 가정 합니다 `arc` . 다른 네임 스페이스/데이터 컨트롤러 이름을 사용한 경우를 사용자의 이름으로 바꿀 수 있습니다 `arc` .

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

아래와 같은 명령을 실행 하 여 특정 pod의 생성 상태를 확인할 수도 있습니다. 이 기능은 문제를 해결 하는 데 특히 유용 합니다.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>생성 문제 해결

만든 문제가 발생 하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조 하세요.
