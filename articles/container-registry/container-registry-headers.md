---
title: "Azure 컨테이너 레지스트리 리포지토리 | Microsoft Docs"
description: "Docker 이미지에 Azure 컨테이너 레지스트리 리포지토리를 사용하는 방법"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.openlocfilehash: 2090d4c951e2261529bf1b7b361510d5822060a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Azure 컨테이너 레지스트리 리포지토리

Azure Container Registry는 다양한 서비스 및 오케스트레이터와 호환됩니다. ACR을 사용하는 소스 서비스 및 에이전트를 쉽게 추적하기 위해 Docker.config 파일에서 Docker 헤더 필드를 사용하기 시작했습니다.



## <a name="viewing-repositories-in-the-portal"></a>포털에서 리포지토리 보기

ACR 헤더는 다음과 같은 형식을 따릅니다.
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* Cloud: Azure, Azure Stack 또는 기타 정부 또는 국가별 Azure 클라우드입니다. Azure Stack 및 정부 클라우드가 현재 지원되지 않지만 이 매개 변수는 앞으로 지원될 수 있습니다.
* Service: 서비스의 이름입니다.
* Optionalservicename: 하위 서비스를 포함하거나 SKU를 지정하는 서비스의 선택적 매개 변수입니다(예: Azure/app-service/web-apps에 해당하는 웹앱).

파트너 서비스 및 오케스트레이터는 원격 분석에 도움이 되도록 특정 헤더 값을 사용하는 것이 좋습니다. 사용자가 원하는 경우 헤더에 전달된 값을 수정할 수도 있습니다.

ACR 파트너에서 "X-Meta-Source-Client" 필드를 채우는 데 사용하려는 값은 아래와 같습니다.

| 서비스 이름              | 헤더                                |
| ------------------------- | ------------------------------------- |
| Azure Container Service   | azure/compute/azure-container-service |
| App Service - Web Apps    | azure/app-service/web-apps            |
| App Service - Logic Apps  | azure/app-service/logic-apps          |
| 배치                     | azure/compute/batch                   |
| 클라우드 콘솔             | azure/cloud-console                   |
| 함수                 | azure/compute/functions               |
| IoT(사물 인터넷) - 허브  | azure/iot/hub                         |
| HDInsight                 | azure/data/hdinsight                  |
| Jenkins                   | azure/jenkins                         |
| Machine Learning          | azure/data/machile-learning           |
| 서비스 패브릭            | azure/compute/service-fabric          |
| VSTS                      | azure/vsts                            |


## <a name="next-steps"></a>다음 단계
[레지스트리 및 지원되는 서비스와 오케스트레이터에 대한 자세한 정보](container-registry-intro.md)
