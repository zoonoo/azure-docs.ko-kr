---
title: (사용되지 않음) Azure DC/OS 클러스터 모니터링 - Datadog
description: Datadog를 사용하여 Azure 컨테이너 서비스 클러스터를 모니터링합니다. DC/OS 웹 UI를 사용하여 Datadog 에이전트를 클러스터에 배포합니다.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a094369d467b3b1f3d5fe93f870dccc9eae7519c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60508118"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(사용되지 않음) Datadog을 사용하여 Azure Container Service DC/OS 클러스터 모니터링

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

이 문서에서는 Azure 컨테이너 서비스 클러스터의 모든 노드에 Datadog 에이전트를 배포하는 방법을 설명합니다. 이러한 구성을 위해서는 Datadog 계정이 필요합니다. 

## <a name="prerequisites"></a>필수 조건
Azure Container Service를 통해 구성된 클러스터를 [배포](container-service-deployment.md) 및 [연결](../container-service-connect.md)합니다. [Marathon UI](container-service-mesos-marathon-ui.md)를 탐색합니다. [https://datadoghq.com](https://datadoghq.com)으로 이동하여 Datadog 계정을 설정합니다. 

## <a name="datadog"></a>Datadog
Datadog은 Azure 컨테이너 서비스 클러스터 내의 컨테이너에서 모니터링 데이터를 수집하는 모니터링 서비스입니다. Datadog에는 컨테이너 내에서 특정 메트릭을 볼 수 있는 Docker 통합 대시보드가 있습니다. 컨테이너에서 수집된 메트릭은 CPU, 메모리, 네트워크 및 I/O로 구성됩니다. Datadog은 메트릭을 컨테이너와 이미지로 분할합니다. CPU 사용량에 대한 UI 모양의 예는 아래에 나와 있습니다.

![Datadog UI](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Marathon으로 Datadog 배포 구성
이러한 단계는 Marathon으로 클러스터에 Datadog 애플리케이션을 배포 및 구성하는 방법을 보여줍니다. 

[http://localhost:80/](http://localhost:80/)을 통해 DC/OS UI에 액세스합니다. DC/OS UI에 들어가면 왼쪽 아래에 있는 "Universe"로 이동한 다음 "Datadog"를 검색하고 "설치"를 클릭합니다.

![DC/OS Universe 내 Datadog 패키지](./media/container-service-monitoring/datadog1.png)

이제 Datadog 계정 또는 무료 평가판 계정 구성을 완료합니다. Datadog 웹 사이트에 로그인하면 왼쪽으로 이동하여 통합으로 이동한 다음, [API](https://app.datadoghq.com/account/settings#api)로 이동합니다. 

![Datadog API 키](./media/container-service-monitoring/datadog2.png)

이제 DC/OS Universe 내에 있는 Datadog 구성에 API 키를 입력합니다. 

![DC/OS Universe의 Datadog 구성](./media/container-service-monitoring/datadog3.png) 

위의 구성에서 인스턴스는 10000000로 설정되므로 새 노드가 클러스터에 추가될 때마다 Datadog은 해당 노드에 에이전트를 자동으로 배포합니다. 이는 일시적인 해결책입니다. 패키지를 설치하면 Datadog 웹 사이트로 다시 이동하여 "[대시보드](https://app.datadoghq.com/dash/list)"를 찾아야 합니다. 여기에 사용자 지정 및 통합 대시보드가 표시됩니다. [Docker 대시보드](https://app.datadoghq.com/screen/integration/docker)에는 클러스터를 모니터링하는 데 필요한 모든 컨테이너 메트릭이 있게 됩니다. 

