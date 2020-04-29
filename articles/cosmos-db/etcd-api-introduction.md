---
title: Azure Cosmos DB etcd API 소개
description: 이 문서에서는 etcd API의 개요 및 주요 이점을 제공 Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79498587"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure Cosmos DB etcd API 소개 (미리 보기)

Azure Cosmos DB는 업무에 중요한 애플리케이션에 대한 Microsoft의 전역 분산 다중 모델 데이터베이스 서비스입니다. 턴키 전역 배포, 처리량 및 저장소의 탄력적인 확장, 99 번째 백분위 수에서 1 자리 밀리초 대기 시간, 보장 된 고가용성을 제공 하며, 모두 업계 최고의 SLA로 지원 됩니다.

[Etcd](https://github.com/etcd-io/etcd) 는 분산 키/값 저장소입니다. [Kubernetes](https://kubernetes.io/)에서는 etcd를 사용 하 여 Kubernetes 클러스터의 상태와 구성을 저장 합니다. Etcd의 가용성, 안정성 및 성능 보장은 전체 클러스터 상태, 확장성, 탄력성 가용성 및 Kubernetes 클러스터의 성능에 매우 중요 합니다. 

Azure Cosmos DB의 etcd API를 사용 하 여 [Azure Kubernetes](../aks/index.yml)에 대 한 백 엔드 저장소로 Azure Cosmos DB을 사용할 수 있습니다. Azure Cosmos DB의 etcd API는 현재 미리 보기로 제공됩니다. Azure Cosmos DB는 etcd 유선 프로토콜을 구현 합니다. Azure Cosmos DB에서 etcd API를 사용 하 여 개발자는 안정적이 고, [사용 가능한](high-availability.md), [전 세계적으로 분산](distribute-data-globally.md) 된 Kubernetes을 자동으로 가져옵니다. 개발자는이 API를 사용 하 여 완전히 관리 되는 클라우드 네이티브 PaaS 서비스에서 Kubernetes 상태 관리를 확장할 수 있습니다. 

> [!NOTE]
> Azure Cosmos DB의 다른 Api와 달리 Azure Portal, CLI 또는 Sdk를 통해 etcd API 계정을 프로 비전 할 수 없습니다. 리소스 관리자 템플릿만 배포 하 여 etcd API 계정을 프로 비전 할 수 있습니다. 자세한 단계 [는 Azure Cosmos DB을 사용 하 여 Azure Kubernetes를 프로 비전 하는 방법](bootstrap-kubernetes-cluster.md) 문서를 참조 하세요. Azure Cosmos DB etcd API는 현재 제한 된 미리 보기로 제공 됩니다. 등록 양식을 작성 하 여 [미리 보기에 등록할](https://aka.ms/cosmosetcdapi-signup)수 있습니다.

## <a name="wire-level-compatibility"></a>유선 수준 호환성

Azure Cosmos DB은 etcd 버전 3의 유선 프로토콜을 구현 하며, [마스터 노드의](https://kubernetes.io/docs/concepts/overview/components/) API 서버가 로컬로 설치 된 etcd 환경에서와 마찬가지로 Azure Cosmos DB를 사용할 수 있도록 합니다. Etcd API는 TLS 상호 인증을 지원 합니다. 

다음 다이어그램에서는 Kubernetes 클러스터의 구성 요소를 보여 줍니다. 클러스터 마스터에서 API 서버는 로컬에 설치 된 etcd 대신 Azure Cosmos DB etcd API를 사용 합니다. 

![Etcd 유선 프로토콜을 구현 하는 Azure Cosmos DB](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>주요 이점

### <a name="no-etcd-operations-management"></a>Etcd 작업 관리 없음

완전히 관리 되는 네이티브 클라우드 서비스인 Azure Cosmos DB Kubernetes 개발자가 etcd를 설정 하 고 관리할 필요가 없습니다. Azure Cosmos DB의 etcd API는 확장 가능 하 고 가용성이 뛰어나고 내결함성이 있으며 높은 성능을 제공 합니다. 여러 노드에 걸쳐 복제를 설정 하 고, 롤링 업데이트를 수행 하 고, 보안 패치를 수행 하 고, etcd 상태를 모니터링 하는 오버 헤드는 Azure Cosmos DB에 의해 처리 됩니다.

### <a name="global-distribution--high-availability"></a>전역 배포 & 고가용성 

Etcd API Azure Cosmos DB를 사용 하 여 단일 지역에서 데이터 읽기 및 쓰기에 대해 99.99%의 가용성을 보장 하 고 여러 지역에서 99.999%의 가용성을 보장 합니다. 

### <a name="elastic-scalability"></a>탄력적 확장성

Azure Cosmos DB는 서로 다른 지역에서 읽기 및 쓰기 요청에 대 한 탄력적 확장성을 제공 합니다.
Kubernetes 클러스터가 증가 함에 따라 Azure Cosmos DB 탄력적으로의 etcd API 계정은 가동 중지 시간 없이 확장 됩니다. Kubernetes 마스터 노드가 아닌 Azure Cosmos DB에 etcd 데이터를 저장 하면 보다 유연한 마스터 노드 크기 조정도 가능 합니다. 

### <a name="security--enterprise-readiness"></a>보안 & 엔터프라이즈 준비

Etcd 데이터가 Azure Cosmos DB에 저장 되 면 Kubernetes 개발자가 자동으로 [기본 제공 암호화](database-encryption-at-rest.md), [인증 및 규정 준수](compliance.md), Azure Cosmos DB에서 지 원하는 [백업 및 복원 기능](online-backup-and-restore.md) 을 자동으로 가져옵니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 Azure Kubernetes를 사용하는 방법](bootstrap-kubernetes-cluster.md)
* [Azure Cosmos DB의 주요 이점](introduction.md)
* [AKS 엔진 빠른 시작 가이드](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)