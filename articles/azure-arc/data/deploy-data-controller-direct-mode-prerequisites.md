---
title: 필수 구성 요소 | 직접 연결 모드
description: 직접 연결 모드에서 데이터 컨트롤러를 배포하기 위한 필수 구성 요소.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716296"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>데이터 컨트롤러 배포 - 직접 연결 모드(필수 구성 요소)

이 문서에서는 직접 연결 모드에서 Azure Arc 지원 데이터 서비스에 대한 데이터 컨트롤러 배포를 준비하는 방법을 설명합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

높은 수준의 요약에서 필수 구성 요소는 다음과 같습니다.

1. 도구 설치
1. 확장 추가
1. 서비스 주체 만들기 및 메트릭에 대한 역할 구성
1. Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 클러스터를 Azure에 연결

이러한 필수 구성 요소를 완료한 후 [Azure Arc 데이터 컨트롤러 | 직접 연결 모드를 배포](deploy-data-controller-direct-mode.md)할 수 있습니다.

이 문서의 나머지 섹션에서는 필수 구성 요소를 식별합니다.

## <a name="install-tools"></a>도구 설치

- Helm 버전 3.3 이상([설치](https://helm.sh/docs/intro/install/))
- Azure CLI([설치](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Azure CLI에 대한 확장 추가

또한 다음 az extensions도 필요합니다.
- Azure CLI `k8s-extension` 확장(0.2.0)
- Azure CLI `customlocation`(0.1.0)

샘플 `az` 및 해당 CLI 확장은 다음과 같습니다.

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>서비스 주체 만들기 및 메트릭에 대한 역할 구성

[메트릭 업로드](upload-metrics-and-logs-to-azure-monitor.md) 문서에 설명된 단계에 따라 서비스 주체를 만들고 문서에 설명된 대로 역할을 부여합니다. 

SPN ClientID, TenantID 및 클라이언트 암호 정보는 [Azure Arc 데이터 컨트롤러를 배포](deploy-data-controller-direct-mode.md)할 때 필요합니다. 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 클러스터를 Azure에 연결

이 작업을 완료하려면 [Azure arc에 기존 Kubernetes 클러스터 연결](../kubernetes/quickstart-connect-cluster.md)의 단계를 따르세요.

## <a name="next-steps"></a>다음 단계

이러한 필수 구성 요소를 완료한 후 [Azure Arc 데이터 컨트롤러 | 직접 연결 모드를 배포](deploy-data-controller-direct-mode.md)할 수 있습니다.
