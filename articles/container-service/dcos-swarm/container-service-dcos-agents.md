---
title: (사용되지 않음) Azure Container Service의 DC/OS 에이전트 풀
description: 공용 및 프라이빗 에이전트 풀이 Azure Container Service 클러스터와 함께 작동하는 방식
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 03cacda1aa405cb2d0ded579c8ddb5f6011ce3bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478449"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(사용되지 않음) Azure Container Service의 DC/OS 에이전트 풀

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Azure Container Service의 DC/OS 클러스터는 2개의 풀, 즉 공용 풀과 프라이빗 풀에 에이전트 노드를 포함합니다. 애플리케이션을 이러한 두 풀 중 하나에 배포할 수 있으며 이는 컨테이너 서비스의 컴퓨터 간의 액세스 가능성에 영향을 줍니다. 머신은 인터넷(공용)에 노출되거나 내부(개인)로 유지될 수 있습니다. 이 문서에서는 공용 및 프라이빗 풀이 있는 이유에 대한 간략한 개요를 제공합니다.


* **사용자 에이전트**: 프라이빗 에이전트 노드는 라우팅할 수 없는 네트워크를 통해 실행됩니다. 이 네트워크는 관리 영역에서나 공용 영역 에지 라우터를 통해서만 액세스할 수 있습니다. 기본적으로 DC/OS는 프라이빗 에이전트 노드에서 앱을 시작합니다. 

* **공용 에이전트**: 공용 에이전트 노드는 공개적으로 액세스 가능한 네트워크를 통해 DC/OS 앱과 서비스를 실행합니다. 

DC/OS 네트워크 보안에 대한 자세한 내용은 [DC/OS 설명서](https://docs.mesosphere.com/)를 참조하세요.

## <a name="deploy-agent-pools"></a>에이전트 풀 배포

Azure Container Service의 DC/OS 에이전트 풀은 다음과 같이 만들어집니다.

* **개인 풀**은 [DC/OS 클러스터 배포](container-service-deployment.md) 시 지정하는 에이전트 노드 수를 포함합니다. 

* **공용 풀**은 처음에 미리 정의된 수의 에이전트 노드를 포함합니다. 이 풀은 DC/OS 클러스터가 프로비저닝될 때 자동으로 추가됩니다.

프라이빗 풀과 공용 풀은 Azure 가상 머신 규모 집합입니다. 배포 후 이러한 풀의 크기를 조정할 수 있습니다.

## <a name="use-agent-pools"></a>에이전트 풀 사용
기본적으로 **Marathon**은 새 애플리케이션을 *프라이빗* 에이전트 노드에 배포합니다. 애플리케이션 생성 중에 애플리케이션을 *공용* 노드에 명시적으로 배포해야 합니다. **선택 사항** 탭을 선택하고 **수락된 리소스 역할** 값에 **slave_public**을 입력합니다. 이 과정은 [여기](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)와 [DC\OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) 설명서에 문서화되어 있습니다.

## <a name="next-steps"></a>다음 단계
* [DC/OS 컨테이너 관리](container-service-mesos-marathon-ui.md)에 대해 자세히 알아보세요.

* DC/OS 컨테이너에 대한 공용 액세스를 허용하기 위해 Azure에서 제공하는 [방화벽을 여는 방법](container-service-enable-public-access.md)에 대해 알아보세요.

