---
title: Azure Cosmos DB etcd API 소개
description: 이 문서에서는 Azure Cosmos DB에서 etcd API의 개요 및 주요 이점을 제공합니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 16aac5c765c36c49919685ee58e8034786ddf1ae
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797368"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure Cosmos DB etcd API(미리 보기) 소개

Azure Cosmos DB는 업무에 중요한 애플리케이션에 대한 Microsoft의 전역 분산 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB는 업계 최고의 SLA로 지원되는 턴키 글로벌 분산, 처리량 및 스토리지의 탄력적인 확장, 99번째 백분위수의 1자리 수 밀리초 크기 대기 시간, 보장된 고가용성을 제공합니다.

[Etcd](https://github.com/etcd-io/etcd)는 분산 키/값 저장소입니다. [Kubernetes](https://kubernetes.io/)에서 etcd를 사용하여 Kubernetes 클러스터의 상태 및 구성을 저장합니다. etcd의 가용성, 안정성 및 성능 보장은 전체 클러스터 상태, 확장성, 탄력성 가용성 및 Kubernetes 클러스터의 성능에 매우 중요합니다.

Azure Cosmos DB의 etcd API를 사용하면 Azure Cosmos DB를 Azure Kubernetes의 백 엔드 저장소로 사용할 수 있습니다. Azure Cosmos DB의 etcd API는 현재 미리 보기로 제공됩니다. Azure Cosmos DB는 etcd 유선 프로토콜을 구현합니다. Azure Cosmos DB에서 etcd API를 사용하면 개발자는 안정적이고, [사용 가능한](high-availability.md), [전역적으로 분산된](distribute-data-globally.md) Kubernetes를 자동으로 받게 됩니다. 개발자는 이 API를 사용하여 완전히 관리되는 클라우드 네이티브 PaaS 서비스에서 Kubernetes 상태 관리를 확장할 수 있습니다. 

> [!NOTE]
> Azure Cosmos DB의 다른 API와 달리 Azure Portal, CLI 또는 SDK를 통해 etcd API 계정을 프로비저닝할 수 없습니다. Resource Manager 템플릿만 배포하여 etcd API 계정을 프로비저닝할 수 있습니다. 자세한 단계는 [Azure Cosmos DB를 사용하여 Azure Kubernetes를 프로비저닝하는 방법](bootstrap-kubernetes-cluster.md) 문서를 참조하세요. Azure Cosmos DB etcd API는 현재 제한된 미리 보기로 제공됩니다. 가입 양식을 작성하여 [미리 보기에 가입](https://aka.ms/cosmosetcdapi-signup)할 수 있습니다.

## <a name="wire-level-compatibility"></a>유선 수준 호환성

Azure Cosmos DB는 etcd 버전 3의 유선 프로토콜을 구현하고, [마스터 노드](https://kubernetes.io/docs/concepts/overview/components/)의 API 서버가 로컬에 설치된 etcd 환경에서 수행하는 것과 마찬가지로 Azure Cosmos DB를 사용할 수 있도록 합니다. etcd API는 TLS 상호 인증을 지원합니다. 

다음 다이어그램에서는 Kubernetes 클러스터의 구성 요소를 보여 줍니다. 클러스터 마스터에서 API 서버는 로컬로 설치된 etcd 대신 Azure Cosmos DB etcd API를 사용합니다. 

![etcd 유선 프로토콜을 구현하는 Azure Cosmos DB](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>주요 이점

### <a name="no-etcd-operations-management"></a>etcd 작업 관리 없음

완전히 관리되는 네이티브 클라우드 서비스인 Azure Cosmos DB는 Kubernetes 개발자가 etcd를 설정하고 관리할 필요가 없습니다. Azure Cosmos DB의 etcd API는 확장 가능하고 가용성이 뛰어나고 내결함성이 있으며 높은 성능을 제공합니다. 여러 노드에 걸쳐 복제를 설정하고, 롤링 업데이트 및 보안 패치를 수행하고, etcd 상태를 모니터링하는 오버헤드는 Azure Cosmos DB에 의해 처리됩니다.

### <a name="global-distribution--high-availability"></a>글로벌 배포 및 고가용성 

etcd API를 사용하여 Azure Cosmos DB는 단일 지역에서 데이터 읽기 및 쓰기에 대해 99.99%의 가용성을 보장하고, 여러 지역에서 99.999%의 가용성을 보장합니다. 

### <a name="elastic-scalability"></a>탄력적 확장성

Azure Cosmos DB는 서로 다른 지역에서 읽기 및 쓰기 요청에 대한 탄력적 확장성을 제공합니다.
Kubernetes 클러스터가 증가함에 따라 Azure Cosmos DB의 etcd API 계정은 가동 중지 시간 없이 탄력적으로 확장됩니다. Kubernetes 마스터 노드 대신 Azure Cosmos DB에 etcd 데이터를 저장하면 보다 유연한 마스터 노드 크기 조정도 가능합니다. 

### <a name="security--enterprise-readiness"></a>보안 및 엔터프라이즈 준비

etcd 데이터가 Azure Cosmos DB에 저장되면 Kubernetes 개발자는 Azure Cosmos DB에서 지원되는 [기본 제공 미사용 암호화](database-encryption-at-rest.md), [인증 및 규정 준수](compliance.md) 및 [백업 및 복원 기능](online-backup-and-restore.md)을 자동으로 가져옵니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 Azure Kubernetes를 사용하는 방법](bootstrap-kubernetes-cluster.md)
* [Azure Cosmos DB의 주요 이점](introduction.md)
* [AKS 엔진 빠른 시작 가이드](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)