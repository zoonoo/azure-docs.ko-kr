---
title: Azure Service Fabric에서 컨테이너 로그 보기 | Microsoft Docs
description: Service Fabric Explorer를 사용하여 실행 중인 Service Fabric 컨테이너 서비스에 대한 컨테이너 로그를 보는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Service Fabric 컨테이너 서비스에 대한 로그 보기
Azure Service Fabric은 컨테이너 오케스트레이터이며 [Windows 및 Linux 컨테이너](service-fabric-containers-overview.md)를 모두 지원합니다.  이 문서에서는 문제를 진단하고 해결할 수 있도록 실행 중인 컨테이너 서비스의 컨테이너 로그를 보는 방법을 설명합니다.

## <a name="access-container-logs"></a>컨테이너 로그 액세스
컨테이너 로그는 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)를 사용하여 액세스할 수 있습니다.  웹 브라우저에서 [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer)로 이동하여 클러스터의 관리 끝점에서 Service Fabric Explorer를 엽니다.  

컨테이너 로그는 컨테이너 서비스 인스턴스가 실행 중인 클러스터 노드에 있습니다. 예를 들어, [Linux Voting 샘플 응용 프로그램](service-fabric-quickstart-containers-linux.md)의 웹 프런트 엔드 컨테이너 로그를 가져옵니다. 트리 보기에서 **Cluster**>**Applications**>**VotingType**>**fabric:/Voting/azurevotefront**를 확장합니다.  그런 다음 파티션(이 예제의 d1aa737e-f22a-e347-be16-eec90be24bc1)을 확장하고 해당 컨테이너가 클러스터 노드 *_lnxvm_0*에서 실행되고 있는지 확인합니다.

트리 보기에서 **Nodes**>**_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Code Packages**>**code**를 확장하여 *_lnxvm_0* 노드에서 코드 패키지를 찾습니다.  그런 후 **컨테이너 로그** 옵션을 선택하여 컨테이너 로그를 표시합니다.

![서비스 패브릭 플랫폼][Image1]


## <a name="next-steps"></a>다음 단계
- [Linux 컨테이너 응용 프로그램 만들기 자습서](service-fabric-tutorial-create-container-images.md)를 진행합니다.
- [Service Fabric 및 컨테이너](service-fabric-containers-overview.md)에 대해 자세알아히 봅니다.

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
