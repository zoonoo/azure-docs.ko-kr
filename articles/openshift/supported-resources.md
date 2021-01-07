---
title: Azure Red Hat OpenShift 3.11을 지원하는 리소스
description: Red Hat OpenShift Microsoft Azure에서 지 원하는 Azure 지역 및 가상 머신 크기를 이해 합니다.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 38203cede755d776ba9142ad16e1bea1fd1637a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82203678"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift 리소스

이 항목에서는 Microsoft Azure Red Hat OpenShift 3.11 서비스에서 지 원하는 Azure 지역 및 가상 머신 크기를 나열 합니다.

## <a name="azure-regions"></a>Azure 지역

Azure Red Hat OpenShift 클러스터를 배포할 수 있는 현재 지역 목록은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) 을 참조 하세요.

## <a name="virtual-machine-sizes"></a>가상 머신 크기

Azure Red Hat OpenShift 클러스터에서 계산 노드에 대해 지정할 수 있는 지원 되는 가상 머신 크기는 다음과 같습니다.

> [!Important]
> 각 VM에는 서로 다른 수의 드라이브를 연결할 수 있습니다. 이는 메모리 또는 CPU 크기와는 즉시 명확 하지 않을 수 있습니다.
> 일부 VM 크기는 일부 지역에서 사용할 수 없습니다. 지정 된 크기를 API에서 지원 하더라도 지정 된 지역에서 크기를 사용할 수 없는 경우 오류가 발생할 수 있습니다.
> 자세한 내용은 [지역 당 지원 되는 VM 크기의 현재 목록](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 을 참조 하세요.

## <a name="compute-node-sizes"></a>계산 노드 크기

Azure Red Hat OpenShift REST API에서 지 원하는 계산 노드 크기는 다음과 같습니다.

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

Azure Red Hat OpenShift REST API에서 지 원하는 마스터/인프라 노드 크기는 다음과 같습니다.

|크기|vCPU|RAM|
|-|-|-|
|표준 D4s v3|4|16GB|
|표준 D8s v3|8|32GB|
|표준 D16s v3|16|64GB|
|표준 D32s v3|32|128GB|

## <a name="next-steps"></a>다음 단계

[Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md) 자습서를 사용해 보세요.
