---
title: Azure Red Hat 오픈시프트에 지원되는 리소스
description: Microsoft Azure Red Hat OpenShift에서 지원하는 Azure 지역 및 가상 시스템 크기를 이해합니다.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243667"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure 레드햇 오픈시프트 리소스

이 항목에서는 Microsoft Azure Red Hat OpenShift 서비스에서 지원하는 Azure 지역 및 가상 시스템 크기를 나열합니다.

## <a name="azure-regions"></a>Azure 지역

Azure Red Hat OpenShift 클러스터를 배포할 수 있는 현재 지역 목록은 [지역별로 제공되는 제품을](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) 참조하세요.

## <a name="virtual-machine-sizes"></a>가상 머신 크기

Azure Red Hat OpenShift 클러스터의 계산 노드에 대해 지정할 수 있는 지원되는 가상 컴퓨터 크기는 다음과 같습니다.

> [!Important]
> 각 VM에는 연결할 수 있는 다른 수의 드라이브가 있습니다. 메모리 또는 CPU 크기만큼 즉시 명확하지 않을 수 있습니다.
> 일부 VM 크기는 일부 지역에서 사용할 수 없습니다. API가 지정한 크기를 지원하는 경우에도 지정한 지역에서 크기를 사용할 수 없는 경우 오류가 발생할 수 있습니다.
> 자세한 내용은 [지역별 지원되는 VM 크기의 현재 목록을](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 참조하십시오.

## <a name="compute-node-sizes"></a>노드 크기 계산

다음 계산 노드 크기는 Azure Red Hat OpenShift REST API에서 지원됩니다.

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

다음 마스터/인프라 노드 크기는 Azure Red Hat OpenShift REST API에서 지원됩니다.

|크기|vCPU|RAM|
|-|-|-|
|표준 D4s v3|4|16GB|
|표준 D8s v3|8|32GB|
|표준 D16s v3|16|64GB|
|표준 D32s v3|32|128GB|

## <a name="next-steps"></a>다음 단계

Azure [Red Hat 오픈시프트 클러스터 만들기 자습서를](tutorial-create-cluster.md) 사용해 보십시오.
