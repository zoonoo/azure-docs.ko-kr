---
title: Azure Service Fabric CLI(sfctl) 스크립트 제거 샘플
description: Azure Service Fabric CLI를 사용하여 Azure Service Fabric 클러스터에 애플리케이션 제거
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: fd5d20ed3f2cc41f4d7d51f06d4bc90a6afd22eb
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75526535"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-the-service-fabric-cli"></a>Service Fabric CLI를 사용하여 Service Fabric 클러스터에서 애플리케이션 제거

이 샘플 스크립트는 실행 중인 Service Fabric 애플리케이션 인스턴스를 삭제한 다음, 클러스터에서 애플리케이션 유형 및 버전의 등록을 취소합니다.  애플리케이션 인스턴스를 삭제하면 해당 애플리케이션과 연결된 실행 중인 모든 서비스 인스턴스도 삭제됩니다. 다음으로 애플리케이션 파일을 이미지 저장소에서 삭제합니다. 

필요한 경우 [Service Fabric CLI](../service-fabric-cli.md)를 설치합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Service Fabric CLI 설명서](../service-fabric-cli.md)를 참조하세요.

Azure Service Fabric에 대한 추가 Service Fabric CLI 샘플은 [Service Fabric CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.
