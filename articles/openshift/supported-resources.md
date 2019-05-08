---
title: Azure Red Hat OpenShift에 대 한 지원 되는 리소스 | Microsoft Docs
description: Microsoft Azure의 Red Hat OpenShift에서 지원 되는 Azure 지역 및 가상 머신 크기를 이해 합니다.
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 22cc195b7808ad4a9111aafcf883a68c51f3709f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076107"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure의 Red Hat OpenShift 리소스

이 항목에서는 Azure 지역 및 Microsoft Azure의 Red Hat OpenShift 서비스에서 지 원하는 가상 머신 크기를 나열 합니다.

## <a name="azure-regions"></a>Azure 지역

전 세계 Azure 지역에 Azure Red Hat OpenShift 클러스터를 배포할 수 있습니다.

|지역|CLI 코드|
|-|-|
|🇦🇺 오스트레일리아 동부|`australiaeast`|
|🇨🇦 캐나다 중부|`canadacentral`|
|🇨🇦 캐나다 동부|`canadaeast`|
|🇺🇸 미국 동부|`eastus`|
|🇺🇸 미국 서 부|`westus`|
|🇪🇺 유럽 서 부|`westeurope`|
|🇪🇺 북유럽|`northeurope`|

## <a name="virtual-machine-sizes"></a>가상 머신 크기

Azure Red Hat OpenShift 클러스터의 계산 노드에 대해 지정할 수 있습니다 하는 지원 되는 가상 컴퓨터 크기는 다음과 같습니다.

> [!Important]
> 각 VM에 연결할 수 있는 드라이브의 수를 다르게 합니다. 이 아닐으로 즉시 지우기 메모리 또는 CPU 크기입니다.
> 일부 VM 크기는 일부 지역에서 사용할 수 없습니다. API를 지정 하는 크기를 지 원하는 경우에 오류가 발생할 수 있습니다는 지정한 지역에서 크기를 사용할 수 없는 경우.
> 참조 [지역당 현재 목록은 지원 되는 VM 크기](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 자세한 내용은 합니다.

## <a name="compute-node-sizes"></a>계산 노드 크기

다음 계산 노드 크기는 Azure의 Red Hat OpenShift REST API에서 지원 됩니다.

|크기|vCPU|RAM|
|-|-|-|
|표준 D4s v3|4|16GB|
|표준 D8s v3|8|32GB|
|표준 D16s v3|16|64GB|
|표준 D32s v3|32|128GB|
|-|-|-|
|표준 E4s v3|4|32GB|
|표준 E8s v3|8|64GB|
|표준 E16s v3|16|128GB|
|표준 E32s v3|32|256GB|
|-|-|-|
|표준 F8s v2|8|16GB|
|표준 F16s v2|16|32GB|
|표준 F32s v2|32|64GB|

## <a name="master-node-sizes"></a>마스터 노드 크기

다음 마스터 / 인프라 노드 크기 Azure Red Hat OpenShift REST API에서 지원 됩니다.

|크기|vCPU|RAM|
|-|-|-|
|표준 D4s v3|4|16GB|
|표준 D8s v3|8|32GB|
|표준 D16s v3|16|64GB|
|표준 D32s v3|32|128GB|

## <a name="next-steps"></a>다음 단계

시도 된 [Azure Red Hat OpenShift 클러스터를 만드는](tutorial-create-cluster.md) 자습서입니다.