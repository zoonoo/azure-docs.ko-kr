---
title: 데이터 컨트롤러 만들기
description: 이미 배포한 일반적인 다중 노드 Kubernetes 클러스터에서 Azure Arc 데이터 컨트롤러를 만듭니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ba2d0acec37d0f59240381cdea04f4d53ded0b1c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273044"
---
# <a name="create-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기 개요

여러 가지 방법을 사용 하 여 다양 한 유형의 Kubernetes 클러스터 및 관리 Kubernetes services에서 Azure Arc enabled 데이터 서비스를 만들 수 있습니다.

현재 지원 되는 Kubernetes 서비스 및 배포 목록은 다음과 같습니다.

- AKS(Azure Kubernetes Service)
- Azure Stack의 AKS 엔진 (Azure Kubernetes Service Engine)
- Azure Stack HCI의 Azure Kubernetes Service
- ARO(Azure RedHat OpenShift)
- OpenShift Container Platform (OCP)
- AWS EKS(Elastic Kubernetes Service)
- Google Cloud Kubernetes Engine (GKE)
- 오픈 소스, 업스트림 Kubernetes 일반적으로 kubeadm를 사용 하 여 배포

> [!IMPORTANT]
> * Kubernetes의 지원 되는 최소 버전은 v 1.14입니다.
> * 환경 및 Azure 간에 필요한 연결을 이해 하려면 [연결 요구 사항](connectivity.md) 을 참조 하세요.
> * 영구 저장소를 구성 하는 방법에 대 한 세부 정보를 이해 하려면 [저장소 구성 지침](storage-configuration.md) 을 참조 하세요.
> * Azure Kubernetes Service를 사용 하는 경우 클러스터의 작업자 노드 VM 크기는 **Standard_D8s_v3** 이상 이어야 하며 **프리미엄 디스크** 를 사용 해야 합니다. 
> * 다른 Kubernetes 배포 또는 서비스를 사용 하는 경우 모든 Kubernetes 노드에서 사용할 수 있는 최소 노드 크기는 8gb RAM, 4 코어, 32 GB RAM의 총 용량을 사용 하도록 해야 합니다. 예를 들어 1 개의 노드가 32 g b RAM 및 4 코어에 있을 수 있으며, 각각 16GB RAM 및 4 개 코어를 포함 하는 2 개의 노드가 있을 수 있습니다.

> [!NOTE]
> Azure에서 Red Hat OpenShift Container Platform을 사용 하는 경우 사용 가능한 최신 버전을 사용 하는 것이 좋습니다.

선택한 옵션에 따라 특정 도구가 _필요_하지만, Azure Arc 데이터 컨트롤러 만들기를 시작 하기 전에 [모든 클라이언트 도구를 설치](./install-client-tools.md) 하는 것이 좋습니다.

사용자가 선택 하는 옵션에 관계 없이 만들기 프로세스 중에 다음 정보를 제공 해야 합니다.

- **데이터 컨트롤러 이름** -데이터 컨트롤러에 대 한 설명이 포함 된 이름입니다 (예: "프로덕션 데이터 컨트롤러", "시애틀 데이터 컨트롤러").
- **데이터 컨트롤러 사용자 이름** -데이터 컨트롤러 관리자 사용자에 대 한 사용자 이름입니다.
- **Data controller password** -데이터 컨트롤러 관리자 사용자에 대 한 암호입니다.
- **Kubernetes 네임 스페이스의 이름** -데이터 컨트롤러를 만들려는 Kubernetes 네임 스페이스의 이름입니다.
- **연결 모드** -클러스터의 [연결 모드](./connectivity.md) 입니다. 현재 "간접"만 지원 됩니다.
- Azure **구독 ID** -azure의 데이터 컨트롤러 리소스를 만들려는 AZURE 구독 GUID입니다.
- **Azure 리소스 그룹 이름** -azure의 데이터 컨트롤러 리소스를 만들려는 리소스 그룹의 이름입니다.
- Azure **location** -데이터 컨트롤러 리소스 메타 데이터가 azure에 저장 되는 azure 위치입니다. 사용 가능한 지역 목록은 [지역별 Azure 글로벌 인프라/제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Arc 데이터 컨트롤러를 만드는 방법에는 여러 가지가 있습니다.

> **작업을 시도해보시겠습니까?**  
> Azure Kubernetes 서비스 (AKS), AWS 탄력적 Kubernetes 서비스 (EKS), Google Cloud Kubernetes Engine (GKE) 또는 Azure VM에서 [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) 를 빠르게 시작 하세요.
> 
- [Azure Data CLI를 사용 하 여 데이터 컨트롤러 만들기 (azdata)](create-data-controller-using-azdata.md)
- [Azure Data Studio를 사용 하 여 데이터 컨트롤러 만들기](create-data-controller-azure-data-studio.md)
- [Azure Data Studio에서 Jupyter 노트북을 통해 Azure Portal에서 데이터 컨트롤러를 만듭니다.](create-data-controller-resource-in-azure-portal.md)
- [Kubectl 또는 oc와 같은 Kubernetes 도구를 사용 하 여 데이터 컨트롤러 만들기](create-data-controller-using-kubernetes-native-tools.md)
- [테스트 배포의 가속화 된 환경을 위한 Azure Arc Jumpstart를 사용 하 여 데이터 컨트롤러 만들기](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)
