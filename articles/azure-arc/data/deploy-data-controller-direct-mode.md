---
title: Azure Arc 데이터 컨트롤러 배포 | 직접 연결 모드
description: 직접 연결 모드에서 데이터 컨트롤러를 배포하는 방법을 설명합니다.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 91bf9674bfc49458dd2e8b1824b521a37b446a0a
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108289285"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Azure Arc 데이터 컨트롤러 배포 | 직접 연결 모드

이 문서에서는 이 기능의 현재 미리 보기 기간 동안 직접 연결 모드에서 Azure Arc 데이터 컨트롤러를 배포하는 방법을 설명합니다. 

현재 Azure Portal에서 Azure Arc 데이터 컨트롤러를 만들 수 있습니다. Azure Arc 지원 데이터 서비스를 위한 다른 도구는 직접 연결 모드에서 데이터 컨트롤러 만들기를 지원하지 않습니다. 자세한 내용은 [릴리스 정보](release-notes.md)를 참조하세요.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>필수 조건 완료

시작하기 전에 [데이터 컨트롤러 배포 - 직접 연결 모드 - 필수 조건](deploy-data-controller-direct-mode-prerequisites.md)에서 필수 조건을 완료 했는지 확인합니다.

이 문서는 대략적으로 다음 작업을 완료하는 방법을 설명합니다.

1. Azure Arc 지원 데이터 서비스 확장을 만듭니다. 
1. 사용자 지정 위치를 만듭니다.
1. 포털에서 데이터 컨트롤러를 배포합니다.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Azure Arc 지원 데이터 서비스 확장 만들기

k8s-extension CLI를 사용하여 데이터 서비스 확장을 만듭니다.

### <a name="set-environment-variables"></a>환경 변수 설정

다음 단계에서 사용할 다음 환경 변수를 설정합니다.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Arc 데이터 서비스 확장 만들기

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> Arc 데이터 서비스 확장 설치를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Arc 데이터 서비스 확장이 생성되었는지 확인

포털에서 또는 Arc 지원 Kubernetes 클러스터에 직접 연결하여 Arc 지원 데이터 서비스 확장이 생성되었는지 확인할 수 있습니다. 

#### <a name="azure-portal"></a>Azure portal
1. Azure Portal에 로그인하고 Kubernetes 연결 된 클러스터 리소스가 있는 리소스 그룹을 찾습니다.
1. 확장이 배포된 Arc 지원 kubernetes 클러스터(유형 = "Kubernetes - Azure Arc")를 선택합니다.
1. 왼쪽의 탐색 메뉴에서 **설정** 아래에 있는 "확장(미리 보기)"을 선택합니다.
1. 앞서 만든 확장이 "설치됨" 상태로 표시됩니다.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="확장 대시보드":::

#### <a name="kubectl-cli"></a>kubectl CLI

1. 터미널 창을 통해 Kubernetes 클러스터에 연결합니다.
1. 아래 명령을 실행하고 (1) 위에서 언급한 네임스페이스가 생성되었는지, (2) `bootstrapper` pod가 '실행 중' 상태인지 확인한 후 다음 단계를 진행합니다.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

예를 들어 `arc` 네임스페이스에서 pod를 가져오는 명령은 다음과 같습니다.

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>사용자 지정 위치 CLI 확장을 사용하여 사용자 지정 위치 만들기

사용자 지정 위치는 Kubernetes 클러스터의 네임스페이스에 해당하는 Azure 리소스입니다.  사용자 지정 위치는 Azure에서 리소스를 배포하는 대상으로 사용됩니다. [Azure Arc 지원 Kubernetes 설명서의 맨 위에 있는 사용자 지정 위치](../kubernetes/conceptual-custom-locations.md)에서 사용자 지정 위치에 대해 자세히 알아보세요.

### <a name="set-environment-variables"></a>환경 변수 설정

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>사용자 지정 위치가 생성되었는지 확인

터미널에서 아래 명령을 실행하여 사용자 지정 위치를 나열하고 **프로비저닝 상태** 가 성공으로 표시되는지 확인합니다.

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기

확장 및 사용자 지정 위치가 생성되었으면 Azure Portal로 이동하여 Azure Arc 데이터 컨트롤러를 배포합니다.

1. Azure Portal에 로그인합니다.
1. Azure Marketplace에서 "Azure Arc 데이터 컨트롤러"를 검색하고 만들기 흐름을 시작합니다.
1. **필수 조건** 섹션에서 Azure Arc 지원 Kubernetes 클러스터(직접 모드)가 선택되어 있는지 확인하고 다음 단계를 진행합니다.
1. **데이터 컨트롤러 세부 정보** 섹션에서 구독 및 리소스 그룹을 선택합니다.
1. 데이터 컨트롤러의 이름을 입력합니다.
1. 배포할 Kubernetes 배포 공급자에 따라 구성 프로필을 선택합니다.
1. 이전 단계에서 만든 사용자 지정 위치를 선택합니다.
1. 데이터 컨트롤러 관리자 로그인 및 암호에 대한 세부 정보를 제공합니다.
1. Azure 개체를 만드는 데 사용되는 서비스 주체의 ClientId, TenantId 및 클라이언트 암호에 대한 세부 정보를 제공합니다. 서비스 주체 계정 및 계정에 부여해야 하는 역할을 만드는 방법에 대한 자세한 지침은 [메트릭 업로드](upload-metrics-and-logs-to-azure-monitor.md)를 참조하세요.
1. **다음** 을 클릭하고 모든 세부 정보에 대한 요약 페이지를 검토한 다음, **만들기** 를 클릭합니다.

## <a name="monitor-the-creation"></a>만들기 모니터링

Azure Portal 배포 상태에 배포가 성공적으로 완료된 것으로 표시되면 다음과 같이 클러스터에서 Arc 데이터 컨트롤러 배포의 상태를 확인할 수 있습니다.

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)

[Azure Arc에서 Azure SQL Managed Instance 만들기](create-sql-managed-instance.md)
