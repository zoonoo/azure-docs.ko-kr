---
title: 필수 구성 요소 | 직접 연결 모드
description: 직접 연결 모드에서 데이터 컨트롤러를 배포하기 위한 필수 구성 요소.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 143a601f89301d3ed4302efa6e978cba53539e2c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734075"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>직접 연결 모드에서 데이터 컨트롤러를 배포하기 위한 필수 구성 요소.

이 문서에서는 직접 연결 모드에서 Azure Arc 지원 데이터 서비스에 대한 데이터 컨트롤러 배포를 준비하는 방법을 설명합니다. Azure Arc 데이터 컨트롤러를 배포하려면 [Azure Arc 지원 데이터 서비스 배포 계획](plan-azure-arc-data-services.md)에 설명된 대로 추가적인 이해와 개념이 필요합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

높은 수준에서 **직접** 연결 모드로 Azure Arc 데이터 컨트롤러를 만들기 위한 필수 조건은 다음과 같습니다.

1. Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 클러스터를 Azure에 연결
2. 서비스 주체 만들기 및 메트릭에 대한 역할 구성
3. Azure Arc 지원 데이터 서비스 데이터 컨트롤러를 만듭니다. 이 단계에는 만들기가 포함됩니다.
    - Azure Arc 데이터 서비스 확장
    - 사용자 지정 위치
    - Azure Arc 데이터 컨트롤러

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 클러스터를 Azure에 연결

```az``` CLI를 사용하여 kubernetes 클러스터를 Azure에 연결할 수 있으며 이와 함께 다음 확장과 Helm을 사용합니다.

#### <a name="install-tools"></a>도구 설치

- Helm 버전 3.3 이상([설치](https://helm.sh/docs/intro/install/))
- 최신 버전의 Azure CLI 설치 또는 업그레이드([설치](/sql/azdata/install/deploy-install-azdata))

#### <a name="add-extensions-for-azure-cli"></a>Azure CLI에 대한 확장 추가

다음 az 확장의 최신 버전을 설치합니다.
- ```k8s-extension```
- ```connectedk8s```
- ```k8s-configuration```
- `customlocation`

다음 명령을 실행하여 az CLI 확장을 설치합니다.

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

```k8s-extension```, ```connectedk8s```, ```k8s-configuration```, `customlocation` 확장을 이미 설치한 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다.

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```
#### <a name="connect-your-cluster-to-azure"></a>Azure에 클러스터 연결

이 작업을 완료하려면 [Azure arc에 기존 Kubernetes 클러스터 연결](../kubernetes/quickstart-connect-cluster.md)의 단계를 따르세요.

클러스터를 Azure에 연결한 후 서비스 주체를 계속 만듭니다. 

## <a name="2-create-service-principal-and-configure-roles-for-metrics"></a>2. 서비스 주체 만들기 및 메트릭에 대한 역할 구성

[메트릭 업로드](upload-metrics-and-logs-to-azure-monitor.md) 문서에 설명된 단계에 따라 서비스 주체를 만들고 문서에 설명된 대로 역할을 부여합니다. 

SPN ClientID, TenantID 및 클라이언트 암호 정보는 [Azure Arc 데이터 컨트롤러를 배포](create-data-controller-direct-azure-portal.md)할 때 필요합니다. 

## <a name="3-create-azure-arc-data-services"></a>3. Azure Arc 데이터 서비스 만들기

이러한 필수 구성 요소를 완료한 후 [Azure Arc 데이터 컨트롤러 | 직접 연결 모드를 배포](create-data-controller-direct-azure-portal.md)할 수 있습니다.


