---
title: Azure 코스모스 DB etcd API 소개
description: 이 문서에서는 Azure Cosmos DB에서 etcd API의 개요 및 주요 이점을 제공합니다.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498587"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure 코스모스 DB etcd API 소개(미리 보기)

Azure Cosmos DB는 업무에 중요한 애플리케이션에 대한 Microsoft의 전역 분산 다중 모델 데이터베이스 서비스입니다. 턴키 글로벌 배포, 처리량 및 스토리지의 탄력적인 확장, 99번째 백분위수의 한 자리 밀리초 대기 시간, 업계 를 선도하는 SLA의 지원을 받는 고가용성 보장을 제공합니다.

[Etcd는](https://github.com/etcd-io/etcd) 분산 키/값 저장소입니다. [Kubernetes에서](https://kubernetes.io/)etcd는 Kubernetes 클러스터의 상태 및 구성을 저장하는 데 사용됩니다. etcd의 가용성, 안정성 및 성능을 보장하는 것은 Kubernetes 클러스터의 전반적인 클러스터 상태, 확장성, 탄력성 가용성 및 성능에 매우 중요합니다. 

Azure Cosmos DB의 etcd API를 사용하면 Azure 코스모스 DB를 [Azure Kubernetes의](../aks/index.yml)백 엔드 저장소로 사용할 수 있습니다. Azure Cosmos DB의 etcd API는 현재 미리 보기로 제공됩니다. Azure 코스모스 DB는 etcd 와이어 프로토콜을 구현합니다. Azure Cosmos DB의 etcd API를 사용하면 개발자가 자동으로 매우 안정적이고 [사용 가능하며](high-availability.md) [전 세계적으로 분산된](distribute-data-globally.md) Kubernetes를 얻을 수 있습니다. 이 API를 통해 개발자는 완전히 관리되는 클라우드 네이티브 PaaS 서비스에서 Kubernetes 상태 관리를 확장할 수 있습니다. 

> [!NOTE]
> Azure Cosmos DB의 다른 API와 달리 Azure 포털, CLI 또는 SDK를 통해 etcd API 계정을 프로비전할 수 없습니다. 리소스 관리자 템플릿만 배포하여 etcd API 계정을 프로비전할 수 있습니다. 자세한 내용은 Azure 코스모스 DB 문서를 [사용하여 Azure Kubernetes를 프로비전하는 방법을](bootstrap-kubernetes-cluster.md) 참조하십시오. Azure 코스모스 DB etcd API는 현재 제한된 미리 보기상태입니다. 등록 양식을 작성하여 [미리 보기에 등록할](https://aka.ms/cosmosetcdapi-signup)수 있습니다.

## <a name="wire-level-compatibility"></a>와이어 레벨 호환성

Azure Cosmos DB는 etcd 버전 3의 와이어 프로토콜을 구현하고 [마스터 노드의](https://kubernetes.io/docs/concepts/overview/components/) API 서버가 로컬로 설치된 etcd 환경에서와 마찬가지로 Azure Cosmos DB를 사용할 수 있도록 합니다. etcd API는 TLS 상호 인증을 지원합니다. 

다음 다이어그램은 Kubernetes 클러스터의 구성 요소를 보여 주며 있습니다. 클러스터 마스터에서 API 서버는 로컬로 설치된 etcd 대신 Azure Cosmos DB etcd API를 사용합니다. 

![Azure 코스모스 DB 등 전선 프로토콜 구현](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>주요 이점

### <a name="no-etcd-operations-management"></a>ETCD 운영 관리 없음

완전히 관리되는 네이티브 클라우드 서비스인 Azure Cosmos DB는 Kubernetes 개발자가 etcd를 설정하고 관리할 필요가 없습니다. Azure Cosmos DB의 etcd API는 확장 가능하고 가용성이 높으며 내결함성이 있으며 높은 성능을 제공합니다. 여러 노드에서 복제를 설정하고, 롤링 업데이트, 보안 패치를 수행하고, etcd 상태를 모니터링하는 오버헤드는 Azure Cosmos DB에서 처리합니다.

### <a name="global-distribution--high-availability"></a>고가용성에 & 글로벌 배포 

Azure Cosmos DB는 etcd API를 사용하여 단일 리전에서 데이터 읽기 및 쓰기에 대해 99.99% 가용성을 보장하고 여러 리전에서 99.999%의 가용성을 보장합니다. 

### <a name="elastic-scalability"></a>탄력적 확장성

Azure Cosmos DB는 여러 지역에서 읽기 및 쓰기 요청을 위한 탄력적인 확장성을 제공합니다.
Kubernetes 클러스터가 증가함에 따라 Azure Cosmos DB의 etcd API 계정은 가동 중지 시간 없이 탄력적으로 확장됩니다. Kubernetes 마스터 노드 대신 Azure Cosmos DB에 etcd 데이터를 저장하면 보다 유연한 마스터 노드 크기 조정이 가능합니다. 

### <a name="security--enterprise-readiness"></a>보안 & 엔터프라이즈 준비

etcd 데이터가 Azure Cosmos DB에 저장되면 Kubernetes 개발자는 [미사용 시 기본 제공 암호화,](database-encryption-at-rest.md) [인증 및 규정 준수,](compliance.md)Azure Cosmos DB에서 지원하는 [백업 및 복원 기능을](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) 자동으로 가져옵니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 Azure Kubernetes를 사용하는 방법](bootstrap-kubernetes-cluster.md)
* [Azure 코스모스 DB의 주요 이점](introduction.md)
* [AKS 엔진 퀵스타트 가이드](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)