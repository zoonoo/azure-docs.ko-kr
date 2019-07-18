---
title: (사용되지 않음) Sysdig을 사용하여 Azure Container Service 클러스터 모니터링
description: Sysdig을 사용하여 Azure 컨테이너 서비스 클러스터를 모니터링합니다.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 39cd5a3fe7db0c442608e7943e7cbd6e3198edfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60509556"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>(사용되지 않음) Sysdig을 사용하여 Azure Container Service 클러스터 모니터링

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

이 문서에서는 Azure 컨테이너 서비스 클러스터의 모든 노드에 Sysdig 에이전트를 배포하는 방법을 설명합니다. 이러한 구성을 위해서는 Sysdig 계정이 필요합니다. 

## <a name="prerequisites"></a>필수 조건
Azure Container Service를 통해 구성된 클러스터를 [배포](container-service-deployment.md) 및 [연결](../container-service-connect.md)합니다. [Marathon UI](container-service-mesos-marathon-ui.md)를 탐색합니다. [https://app.sysdigcloud.com](https://app.sysdigcloud.com)으로 이동하여 Sysdig 클라우드 계정을 설정합니다. 

## <a name="sysdig"></a>Sysdig
Sysdig은 클러스터 내에서 컨테이너를 모니터링할 수 있는 모니터링 서비스입니다. Sysdig은 문제 해결에 도움을 준다고 알려져 있을 뿐만 아니라 CPU, 네트워킹, 메모리 및 I/O에 대한 기본 모니터링 메트릭도 가지고 있습니다. Sysdig를 사용하면 어떤 컨테이너가 가장 많이 사용되고 기본적으로 가장 많은 메모리와 CPU를 사용하는지를 확인할 수 있습니다. 이 보기는 "개요" 섹션에 있으며 현재 베타 단계입니다. 

![Sysdig UI](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Marathon으로 Sysdig 배포 구성
이러한 단계는 Marathon으로 클러스터에 Sysdig 애플리케이션을 배포 및 구성하는 방법을 보여줍니다. 

[http://localhost:80/](http://localhost:80/)을 통해 DC/OS UI에 액세스 DC/OS UI에 들어가면 왼쪽 아래에 있는 "Universe"로 이동한 다음, "Sysdig"를 검색합니다.

![DC/OS Universe에서 Sysdig](./media/container-service-monitoring-sysdig/sysdig1.png)

이제 구성을 완료하려면 Sysdig 클라우드 계정 또는 무료 평가판 계정이 필요합니다. Sysdig 클라우드 웹 사이트에 로그인하여 사용자 이름을 클릭하면 페이지에 "선택키"가 표시됩니다. 

![Sysdig API 키](./media/container-service-monitoring-sysdig/sysdig2.png) 

이제 DC/OS Universe 내에 있는 Sysdig 구성에 선택키를 입력합니다. 

![DC/OS Universe의 Sysdig 구성](./media/container-service-monitoring-sysdig/sysdig3.png)

인스턴스를 10000000로 설정하므로 새 노드가 클러스터에 추가될 때마다 Sysdig은 해당 노드에 에이전트를 자동으로 배포합니다. 이것이 Sysdig이 클러스터 내에서 모든 새 에이전트에 배포되었는지 확인하는 중간 솔루션입니다. 

![DC/OS Universe-인스턴스의 Sysdig 구성](./media/container-service-monitoring-sysdig/sysdig4.png)

일단 패키지를 설치하면 Sysdig UI로 이동하고 클러스터 내에서 컨테이너에 대한 여러 가지 사용 메트릭에 대해 알아볼 수 있습니다. 

[새 대시보드 마법사](https://app.sysdigcloud.com/#/dashboards/new)를 통해 Mesos 및 Marathon 지정 대시보드를 설치할 수도 있습니다.
