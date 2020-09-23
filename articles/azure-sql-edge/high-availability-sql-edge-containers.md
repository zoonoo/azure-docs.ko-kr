---
title: Azure SQL Edge 컨테이너의 고가용성-Azure SQL Edge
description: Azure SQL Edge 컨테이너의 고가용성에 대 한 자세한 정보
keywords: SQL Edge, 컨테이너, 고가용성
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937629"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Azure SQL Edge 컨테이너의 고가용성

Kubernetes에서 기본적으로 Azure SQL Edge 인스턴스를 만들고 관리 합니다. [Kubernetes](https://kubernetes.io/)에서 관리 하는 docker 컨테이너에 Azure SQL Edge를 배포 합니다. Kubernetes에서는 클러스터 노드가 실패 하는 경우 Azure SQL Edge 인스턴스가 있는 컨테이너가 자동으로 복구 될 수 있습니다. Kubernetes 영구 볼륨 클레임 (PVC)을 사용 하 여 SQL Edge 컨테이너 이미지를 구성할 수 있습니다. Kubernetes는 컨테이너의 Azure SQL Edge 프로세스를 모니터링 합니다. 프로세스, Pod, 컨테이너 또는 노드가 실패하는 경우 Kubernetes는 자동으로 다른 인스턴스를 부트스트랩하고 스토리지에 다시 연결합니다.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Kubernetes의 Azure SQL Edge 컨테이너

Kubernetes 1.6 이상에서는 [*저장소 클래스*](https://kubernetes.io/docs/concepts/storage/storage-classes/) [*영구 볼륨 클레임*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)을 지원 합니다.

이 구성에서 Kubernetes는 컨테이너 오케스트레이터의 역할을 수행합니다. 

![Kubernetes 클러스터의 Azure SQL Edge 다이어그램](media/deploy-kubernetes/kubernetes-sql-edge.png)

위 다이어그램에서 `azure-sql-edge`는 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)의 컨테이너입니다. Kubernetes는 클러스터의 리소스를 오케스트레이션합니다. [복제본 세트](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)는 노드 실패 후에 Pod가 자동으로 복구되도록 합니다. 애플리케이션이 서비스에 연결됩니다. 이 경우 서비스는 `azure-sql-edge` 실패 후에도 동일하게 유지되는 IP 주소를 호스트하는 부하 분산 장치를 나타냅니다.

다음 다이어그램에서는 `azure-sql-edge` 컨테이너에서 오류가 발생했습니다. 오케스트레이터인 Kubernetes는 복제본 세트에 올바른 개수의 정상적인 인스턴스가 있도록 보장하고 구성에 따라 새 컨테이너를 시작합니다. 오케스트레이터가 동일한 노드에서 새 Pod를 시작하고, `azure-sql-edge`가 동일한 영구적 스토리지에 다시 연결됩니다. 서비스가 다시 생성된 `azure-sql-edge`에 연결됩니다.

![Pod 실패 후 Kubernetes 클러스터의 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

다음 다이어그램에서는 `azure-sql-edge` 컨테이너를 호스트하는 노드에서 오류가 발생했습니다. 오케스트레이터가 다른 노드에서 새 Pod를 시작하고, `azure-sql-edge`가 동일한 영구적 스토리지에 다시 연결됩니다. 서비스가 다시 생성된 `azure-sql-edge`에 연결됩니다.

![노드 실패 후 Kubernetes 클러스터의 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Kubernetes에서 컨테이너를 만들려면 [Kubernetes에서 AZURE SQL Edge 컨테이너 배포](deploy-Kubernetes.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Kubernetes Service (AKS)에서 Azure SQL Edge 컨테이너를 배포 하려면 다음 문서를 참조 하세요.
- [Kubernetes에서 Azure SQL Edge 컨테이너 배포](deploy-Kubernetes.md)
- [SQL Edge에 ONNX를 사용하는 Machine Learning 및 AI](onnx-overview.md)
- [IoT Edge를 사용하여 SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md).
- [Azure SQL Edge에서 데이터 스트리밍](stream-data.md)