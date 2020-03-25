---
title: sfctl에서 클러스터의 애플리케이션 업데이트
description: Service Fabric CLI 스크립트 샘플 - 새 버전으로 애플리케이션 업데이트. 이 예제에서는 배포된 애플리케이션도 새로운 비트로 업그레이드합니다.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 34f2ae6f3a2ff3adc35794d6e7dfd682640c646b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614726"
---
# <a name="update-an-application-using-the-service-fabric-cli"></a>Service Fabric CLI를 사용하여 애플리케이션 업데이트

이 샘플 스크립트에서는 기존 애플리케이션의 새 버전을 업로드한 다음, 배포된 애플리케이션을 새로운 비트로 업그레이드합니다.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Service Fabric CLI 설명서](../service-fabric-cli.md)를 참조하세요.

Azure Service Fabric에 대한 추가 Service Fabric CLI 샘플은 [Service Fabric CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.
