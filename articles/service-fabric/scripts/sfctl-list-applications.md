---
title: Service Fabric CLI 스크립트 샘플 - 클러스터의 응용 프로그램 나열
description: Service Fabric CLI 스크립트 샘플 - Service Fabric 클러스터에서 프로비전된 응용 프로그램 나열.
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: adegeo
ms.custom: ''
ms.openlocfilehash: 2a58a9d534116dbdd486f1319f3d87e1d2ba24ef
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642770"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Service Fabric 클러스터에서 실행 중인 응용 프로그램 목록

이 샘플 스크립트는 Service Fabric 클러스터에 연결된 다음, 모든 프로비전된 응용 프로그램을 나열합니다.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Service Fabric CLI 설명서](../service-fabric-cli.md)를 참조하세요.

Azure Service Fabric에 대한 추가 Service Fabric CLI 샘플은 [Service Fabric CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.
