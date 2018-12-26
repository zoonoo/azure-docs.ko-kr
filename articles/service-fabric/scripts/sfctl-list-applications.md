---
title: Service Fabric CLI 스크립트 샘플 - 클러스터의 애플리케이션 나열
description: Service Fabric CLI 스크립트 샘플 - Service Fabric 클러스터에서 프로비전된 응용 프로그램 나열.
services: service-fabric
documentationcenter: ''
author: TylerMSFT
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: twhitney
ms.custom: ''
ms.openlocfilehash: bea88460ecaa093a0719d627e3608fd1d530727b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070201"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Service Fabric 클러스터에서 실행 중인 애플리케이션 목록

이 샘플 스크립트는 Service Fabric 클러스터에 연결된 다음, 모든 프로비전된 애플리케이션을 나열합니다.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Service Fabric CLI 설명서](../service-fabric-cli.md)를 참조하세요.

Azure Service Fabric에 대한 추가 Service Fabric CLI 샘플은 [Service Fabric CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.
