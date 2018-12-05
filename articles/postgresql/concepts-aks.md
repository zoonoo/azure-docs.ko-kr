---
title: Azure Database for PostgreSQL과 AKS(Azure Kubernetes Service)를 연결
description: Azure Database for PostgreSQL과 Azure Kubernetes Service를 연결하는 방법 알아보기
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.date: 11/27/2018
ms.topic: article
ms.openlocfilehash: 86474fe612fb93f3a5853f9fea98eb9ab2dd00e5
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336703"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql"></a>Azure Database for PostgreSQL과 Azure Kubernetes Service를 연결

AKS(Azure Kubernetes Service)는 Azure에서 사용할 수 있는 관리형 Kubernetes 클러스터를 제공합니다. AKS 및 Azure Database for PostgreSQL을 함께 사용하여 애플리케이션을 만드는 경우 고려할 몇 가지 옵션은 다음과 같습니다.


## <a name="accelerated-networking"></a>가속된 네트워킹
AKS 클러스터에서 가속된 네트워킹이 활성화된 기본 VM을 사용합니다. VM에서 가속된 네트워킹을 사용하도록 설정하는 경우 VM에서 대기 시간, 지터 및 CPU 사용률이 감축됩니다. 가속된 네트워킹이 작동하는 방법, 지원되는 OS 버전 및 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md)에 대한 VM 인스턴스에 대해 자세히 알아봅니다.

2018년 11월부터 AKS는 지원되는 해당 VM 인스턴스에서 가속된 네트워킹을 지원합니다. 가속된 네트워킹은 해당 VM을 사용하는 새 AKS 클러스터에서 기본적으로 사용하도록 설정됩니다.

AKS 클러스터에 가속된 네트워킹이 있는지 여부를 확인할 수 있습니다.
1. Azure Portal로 이동하고 AKS 클러스터를 선택합니다.
2. 속성 탭을 선택합니다.
3. **인프라 리소스 그룹** 이름을 복사합니다.
4. 포털 검색 표시줄을 사용하여 인프라 리소스 그룹을 엽니다.
5. 해당 리소스 그룹에서 VM을 선택합니다.
6. VM의 **네트워킹** 탭으로 이동합니다.
7. **가속된 네트워킹**을 사용하도록 설정했는지 여부를 확인합니다.


## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/README.md)(Open Service Broker for Azure)를 사용하면 Kubernetes 또는 Cloud Foundry에서 직접 Azure 서비스를 프로비전할 수 있습니다. OSBA는 Azure에 대해 [Open Service Broker API](https://www.openservicebrokerapi.org/)가 구현된 것입니다.

OSBA를 사용하여 Azure Database for PostgreSQL 서버를 만들고 Kubernetes의 기본 언어를 사용하여 AKS 클러스터에 바인딩할 수 있습니다. [OSBA Github 페이지](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md)에서 OSBA 및 Azure Database for PostgreSQL을 함께 사용하는 방법에 대해 알아봅니다. 


## <a name="connection-pooling"></a>연결 풀링
연결 풀러는 데이터베이스에 대해 새 연결을 만들고 닫는 것과 관련된 시간과 비용을 최소화합니다. 풀은 다시 사용할 수 있는 연결의 컬렉션입니다. 

PostgreSQL에서 사용할 수 있는 여러 연결 풀러가 있습니다. 이중 하나가 [PgBouncer](https://pgbouncer.github.io/)입니다. Microsoft Container Registry에서 Microsoft는 AKS에서 Azure Database for PostgreSQL까지 연결을 풀링하기 위해 사이드카에서 사용할 수 있는 컨테이너화된 경량 PgBouncer를 제공합니다. 이 이미지에 액세스하고 사용하는 방법을 알아보려면 [docker 허브 페이지](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/)를 참조하세요. 


## <a name="next-steps"></a>다음 단계
-  [Azure Kubernetes Service 클러스터 만들기](../aks/kubernetes-walkthrough.md)
