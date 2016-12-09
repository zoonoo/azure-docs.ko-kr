---
title: "공용 및 개인 DC/OS 에이전트 풀 ACS | Microsoft Docs"
description: "공용 및 사용자 에이전트 풀이 Azure Container Service 클러스터와 함께 작동하는 방식입니다."
services: container-service
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Mesos, Azure"
ms.assetid: 36d657c9-8845-4bf7-bed2-088323b67406
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a6d9ab6d95de936e4f1d28d47d4e1d74c080bdae


---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Azure Container Service의 DC/OS 에이전트 풀
DC/OS Azure Container Service는 에이전트를 공용 또는 개인 풀로 나눕니다. 배포가 이러한 두 풀 중 하나로 이루어질 수 있으며 이는 컨테이너 서비스의 컴퓨터 간의 액세스 가능성에 영향을 줍니다. 컴퓨터는 인터넷(공용)에 노출되거나 내부(개인)로 유지될 수 있습니다. 이 문서에서는 공용 및 개인 풀이 있는 이유에 대한 간략한 개요를 제공합니다.

### <a name="private-agents"></a>사용자 에이전트
사용자 에이전트 노드는 라우팅할 수 없는 네트워크를 통해 실행됩니다. 이 네트워크는 관리 영역에서나 공용 영역 에지 라우터를 통해서만 액세스할 수 있습니다. 기본적으로 DC/OS는 사용자 에이전트 노드에서 앱을 시작합니다. 네트워크 보안에 대한 자세한 내용은 [DC/OS 설명서](https://dcos.io/docs/1.7/administration/securing-your-cluster/) 를 참조하세요.

### <a name="public-agents"></a>공용 에이전트
공용 에이전트 노드는 공개적으로 액세스 가능한 네트워크를 통해 DC/OS 앱과 서비스를 실행합니다. 네트워크 보안에 대한 자세한 내용은 [DC/OS 설명서](https://dcos.io/docs/1.7/administration/securing-your-cluster/) 를 참조하세요.

## <a name="using-agent-pools"></a>에이전트 풀 사용
기본적으로 **Marathon** 은 새 응용 프로그램을 *사용자* 에이전트 노드에 배포합니다. 응용 프로그램 생성 중에 응용 프로그램을 *공용* 노드에 명시적으로 배포해야 합니다. **선택 사항** 탭을 선택하고 **수락된 리소스 역할** 값에 **slave_public**을 입력합니다. 이 과정은 [여기](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)와 [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) 설명서에 문서화되어 있습니다.

## <a name="next-steps"></a>다음 단계
[DC/OS 컨테이너 관리](container-service-mesos-marathon-ui.md)에 대해 자세히 알아보세요.

DC/OS 컨테이너에 대한 공용 액세스를 허용하기 위해 Azure에 제공된 [방화벽을 여는 방법](container-service-enable-public-access.md) 에 대해 알아보세요.




<!--HONumber=Nov16_HO3-->


