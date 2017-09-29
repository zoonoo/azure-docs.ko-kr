---
title: "Service Fabric CLI 스크립트 샘플 - 클러스터에서 응용 프로그램 업데이트"
description: "Service Fabric CLI 스크립트 샘플 - 새 버전으로 응용 프로그램 업데이트. 이 예제에서는 배포된 응용 프로그램도 새로운 비트로 업그레이드합니다."
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: adegeo
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f91ea8c2f8c6e9a6ce38cf72be2c61d9e5218b23
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Service Fabric 클러스터에 응용 프로그램 인증서 추가

이 샘플 스크립트에서는 기존 응용 프로그램의 새 버전을 업로드하고 배포된 응용 프로그램을 새로운 비트로 업그레이드합니다.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Service Fabric CLI 설명서](../service-fabric-cli.md)를 참조하세요.

Azure Service Fabric에 대한 추가 Service Fabric CLI 샘플은 [Service Fabric CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.

