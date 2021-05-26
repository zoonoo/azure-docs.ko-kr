---
title: 사용자 지정 위치 - Azure Arc 지원 Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 지원 Kubernetes의 클러스터 사용자 지정 위치 기능의 개념적 개요를 제공합니다.
ms.openlocfilehash: fa707822ea385b0f9812e9d71a35bef8f8edfe1f
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376343"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernete 기반의 사용자 지정 위치

Azure 위치 구문의 확장으로, *사용자 지정 위치* 를 통해 테넌트 관리자는 Azure Arc 지원 Kubernetes 클러스터를 Azure 서비스 인스턴스 배포를 위한 대상 위치로 사용할 수 있습니다. Azure 리소스의 예제에는 Azure Arc 지원 SQL Managed Instance 및 Azure Arc 지원 PostgreSQL Hyperscale이 포함됩니다.

Azure 위치와 마찬가지로 사용자 지정 위치에 대한 액세스 권한이 있는 테넌트 내의 최종 사용자는 회사의 프라이빗 컴퓨팅을 사용하여 리소스를 배포할 수 있습니다.

[ ![Arc 플랫폼 계층](./media/conceptual-arc-platform-layers.png) ](./media/conceptual-arc-platform-layers.png#lightbox)

Azure Arc 지원 Kubernetes 클러스터, 클러스터 연결 및 클러스터 확장을 기반으로 사용자 지정 위치를 추상화 계층으로 시각화할 수 있습니다. 사용자 지정 위치는 다른 Azure 서비스가 클러스터에 액세스하는 데 필요한 세부적인 [RoleBindings 및 ClusterRoleBindings](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding)를 만듭니다. 이러한 다른 Azure 서비스는 고객이 클러스터에 배포하려는 리소스를 관리하기 위해 클러스터에 액세스해야 합니다.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architecture

관리자가 클러스터에서 사용자 지정 위치 기능을 사용하도록 설정하면 클러스터에서 ClusterRoleBinding이 만들어져 사용자 지정 위치 RP(리소스 공급자)에서 사용하는 Azure AD 애플리케이션에 권한을 부여합니다. 권한이 부여되면 사용자 지정 위치 RP는 다른 Azure RP에서 이 클러스터에 사용자 지정 리소스를 만드는 데 필요한 ClusterRoleBindings 또는 RoleBindings를 만들 수 있습니다. 클러스터에 설치된 클러스터 확장은 권한 부여에 대한 RP 목록을 결정합니다.

[ ![사용자 지정 위치 사용](./media/conceptual-custom-locations-usage.png) ](./media/conceptual-custom-locations-usage.png#lightbox)

사용자가 클러스터에 데이터 서비스 인스턴스를 만들 때: 
1. PUT 요청이 Azure Resource Manager로 전송됩니다.
1. PUT 요청은 Azure Arc 지원 Data Services RP에 전달됩니다. 
1. RP는 사용자 지정 위치가 있는 Azure Arc 지원 Kubernetes 클러스터와 연결된 `kubeconfig` 파일을 페치합니다. 
   * 사용자 지정 위치는 원래 PUT 요청에서 `extendedLocation`으로 참조됩니다. 
1. Azure Arc 지원 Data Services RP는 `kubeconfig`를 사용하여 클러스터와 통신하고 사용자 지정 위치에 매핑된 네임스페이스에 대한 Azure Arc 지원 Data Services 유형의 사용자 지정 리소스를 만듭니다. 
   * Azure Arc 지원 Data Services 연산자는 사용자 지정 위치가 존재하기 전에 클러스터 확장 만들기를 통해 배포되었습니다. 
1. Azure Arc 지원 Data Services 연산자는 클러스터에서 만든 새 사용자 지정 리소스를 읽고 데이터 컨트롤러를 만들어 클러스터에서 원하는 상태를 인식하도록 변환합니다. 

SQL 관리형 인스턴스 및 PostgreSQL 인스턴스를 만드는 단계 시퀀스는 위에 설명된 단계 시퀀스와 동일합니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 사용하여 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* Azure Arc 지원 Kubernetes 클러스터에 [사용자 지정 위치를 만듭니다](./custom-locations.md).