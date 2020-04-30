---
title: Azure Red Hat OpenShift 4 클러스터 지원 정책
description: Red Hat OpenShift 4의 지원 정책 요구 사항을 이해 합니다.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7bdcccee3270f9d2b611682a9a59505158a494d2
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205210"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Azure Red Hat OpenShift 지원 정책

Azure Red Hat OpenShift 4 클러스터에 대 한 특정 구성은 클러스터의 지원 가능성에 영향을 줄 수 있습니다. Azure Red Hat OpenShift 4를 사용 하면 클러스터 관리자가 내부 클러스터 구성 요소를 변경할 수 있지만 일부 변경은 지원 되지 않습니다. 아래 지원 정책에서는 Microsoft 및 Red Hat의 정책 및 void 지원을 위반 하는 수정 사항을 공유 합니다.

> [!NOTE]
> OpenShift Container Platform에서 기술 미리 보기로 표시 된 기능은 Azure Red Hat OpenShift에서 지원 되지 않습니다.

## <a name="cluster-configuration-requirements"></a>클러스터 구성 요구 사항

* 모든 OpenShift 클러스터 연산자는 관리 되는 상태로 유지 되어야 합니다. 클러스터 연산자 목록은를 실행 `oc get clusteroperators`하 여 반환할 수 있습니다.
* 클러스터 프로메테우스 및 Alertmanager 서비스를 제거 하거나 수정 하지 마십시오.
* 서비스 Alertmanager 규칙을 제거 하지 않습니다.
* OpenShift 클러스터 버전을 수정 하지 마세요.
* Azure Red Hat OpenShift 서비스 로깅 (mdsd pod)을 제거 하거나 수정 하지 마세요.
* ' Arosvc.azurecr.io ' 클러스터 끌어오기 암호를 제거 하거나 수정 하지 마십시오.
* 모든 클러스터 가상 컴퓨터에는 적어도 Azure Resource Manager (ARM) 및 서비스 로깅 (Geneva) 끝점에 대 한 아웃 바운드 인터넷 액세스가 있어야 합니다.
* Azure Red Hat OpenShift 서비스는 개인 링크 서비스를 통해 클러스터에 액세스 합니다.  서비스 액세스를 제거 하거나 수정 하지 마십시오.
* 비 RHCOS 계산 노드는 지원 되지 않습니다. 예를 들어 RHEL 계산 노드를 사용할 수 없습니다.

## <a name="supported-virtual-machine-sizes"></a>지원 되는 가상 머신 크기

Azure Red Hat OpenShift 4는 다음 가상 머신 크기에 대 한 작업자 노드 인스턴스를 지원 합니다.

### <a name="general-purpose"></a>범용 가상 컴퓨터

|계열|Size|vCPU|메모리: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>메모리에 최적화

|계열|Size|vCPU|메모리: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>컴퓨팅 최적화

|계열|Size|vCPU|메모리: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|
