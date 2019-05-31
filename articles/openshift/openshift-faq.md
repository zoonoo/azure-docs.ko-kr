---
title: Azure Red Hat OpenShift에 대 한 질문과 대답 | Microsoft Docs
description: 다음은 Microsoft Azure의 Red Hat OpenShift에 대 한 일반적인 질문에 대답
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 2001b849e9c43d552889475ca237c52b141f3f04
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306275"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift FAQ

이 문서에서는 Microsoft Azure의 Red Hat OpenShift에 대 한 질문과 대답을 (Faq)를 다룹니다.

## <a name="how-do-i-get-started"></a>어떻게 시작하나요?

Azure Red Hat OpenShift를 사용 하려면 먼저 Azure Red Hat OpenShift 예약 응용 프로그램 노드 4 개를 구매 해야 합니다.

Azure 고객으로 라면[Azure Red Hat OpenShift 예약 인스턴스 구매](https://aka.ms/openshift/buy) Azure portal을 통해. 를 구입 후 수 클러스터 프로 비전을 24 시간 내 구독 활성화 됩니다.

Azure는 고객이 아닌 경우 [영업 팀에 문의](https://aka.ms/openshift/contact-sales) 고 프로세스를 시작 하려면 페이지 맨 아래에 판매 양식을 작성 합니다.

참조를 [가격 책정 페이지 Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) 자세한 합니다.

## <a name="which-azure-regions-are-supported"></a>어떤 Azure 지역이 지원 됩니까?

참조 [지원 되는 리소스](supported-resources.md#azure-regions) Azure Red Hat OpenShift의 지원 되는 전 세계 지역 목록은 합니다.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>기존 가상 네트워크에 클러스터를 배포할 수 있습니까?

아니요. 하지만 Azure Red Hat OpenShift 클러스터를 피어 링을 통해 기존 VNET을 연결할 수 있습니다. 참조 [기존 가상 네트워크는 클러스터의 가상 네트워크를 연결할 ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) 세부 정보에 대 한 합니다.

## <a name="what-cluster-operations-are-available"></a>클러스터 작업에 사용할 수 있습니까?

계산 노드 수를 위아래로 확장할 수 있습니다. 기타 수정 작업을 수 여 `Microsoft.ContainerService/openShiftManagedClusters` 를 만든 후 리소스. 계산 노드의 최대 수는 20 개로 제한 합니다.

## <a name="what-virtual-machine-sizes-can-i-use"></a>어떤 가상 머신 크기를 사용할 수 있나요?

참조 [Red Hat OpenShift Azure 가상 머신 크기](supported-resources.md#virtual-machine-sizes) Azure Red Hat OpenShift 클러스터를 사용할 수 있는 virtual machine 크기 목록에 대 한 합니다.

## <a name="is-data-on-my-cluster-encrypted"></a>암호화 된 클러스터에 데이터가 포함 되어 있습니까?

기본적으로 미사용 암호화에 있습니다. Azure Storage 플랫폼 및 해독 검색 전 해당 데이터를 유지 하기 전에 데이터를 자동으로 암호화 합니다. 참조 [미사용 데이터에 대 한 Azure Storage 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 세부 정보에 대 한 합니다.

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>Prometheus/Grafana를 사용 하 여 컨테이너를 모니터링 하 고 용량을 관리할 수 있습니다?

아니요, 현재 시간에 없습니다.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>이므로 Docker 레지스트리 사용 가능한 외부에서 Jenkins와 같은 도구를 사용할 수 있습니까?

그러나 Docker 레지스트리는에서 사용할 수 있는 `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` 강력한 저장소 내구성 보증을 제공 하지 않으면. 사용할 수도 있습니다 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)합니다.

## <a name="is-cross-namespace-networking-supported"></a>네임 스페이스 간 네트워킹 지원 되나요?

프로젝트별으로 사용 하 여 네임 스페이스 간 네트워킹 (거부) 사용자 지정할 수 있습니다 고객 및 개별 프로젝트 관리자 `NetworkPolicy` 개체입니다.

## <a name="can-an-admin-manage-users-and-quotas"></a>사용자 및 할당량 관리자로 관리할 수 있습니까?

예. 사용자 및 모든 사용자가 만든 프로젝트에 액세스 하는 것 외에도 할당량 Azure Red Hat OpenShift 관리자로 관리할 수 있습니다.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>특정 Azure AD 사용자만 클러스터를 제한할 수 있습니까?

예. Azure를 제한할 수 있습니다 AD 사용자가 Azure AD 응용 프로그램을 구성 하 여 클러스터에 서명할 수 있습니다. 세부 정보를 참조 하세요. [방법: 사용자 집합에 대 한 앱 제한](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>클러스터 수에 여러 Azure 지역에서 계산 노드가?

아니요. Azure Red Hat OpenShift 클러스터의 모든 노드는 동일한 Azure 지역에서 생성 되어야 합니다.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>추상화 된 경우 마스터와 인프라 노드 Azure Kubernetes Service (AKS)를 사용 하 여 그대로?

아니요. 모든 리소스를 클러스터 마스터를 포함 하 여 고객 구독에서 실행 합니다. 이러한 유형의 리소스는 읽기 전용 리소스 그룹에 배치 됩니다.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Open Service Broker에 대 한 Azure (OSBA) 지원 인지 확인 합니다.

예. Azure Red Hat OpenShift를 사용 하 여 OSBA를 사용할 수 있습니다. 참조 [Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) 자세한 내용은 합니다.
