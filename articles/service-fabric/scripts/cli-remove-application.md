---
title: "Azure CLI 스크립트 샘플 - 클러스터에서 응용 프로그램 제거"
description: "Azure CLI 스크립트 샘플 - Service Fabric 클러스터에서 응용 프로그램 제거"
services: service-fabric
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 77fa78199854d10c4897d56da12c8e359ae491f3
ms.contentlocale: ko-kr
ms.lasthandoff: 07/25/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Service Fabric 클러스터에서 응용 프로그램 제거

이 샘플 스크립트는 실행 중인 Service Fabric 응용 프로그램 인스턴스를 삭제하고, 클러스터에서 응용 프로그램 유형 및 버전의 등록을 취소합니다.  응용 프로그램 인스턴스를 삭제하면 해당 응용 프로그램과 연결된 실행 중인 모든 서비스 인스턴스도 삭제됩니다. 다음으로 응용 프로그램 파일을 이미지 저장소에서 삭제합니다. 

필요한 경우 [Azure CLI](../service-fabric-azure-cli-2-0.md)를 설치합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "클러스터에서 응용 프로그램 제거")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 참고 사항 |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | 작업할 클러스터를 선택합니다. |
| [sf application delete](/cli/azure/sf/application#delete) | 클러스터에서 응용 프로그램 인스턴스를 삭제합니다. |
| [sf application unprovision](/cli/azure/sf/application#unprovision) | 클러스터에서 Service Fabric 응용 프로그램 유형 및 버전을 등록 취소합니다.|
| [sf application package-delete](/cli/azure/sf/application#package-delete) | 이미지 저장소에서 Service Fabric 응용 프로그램 패키지를 제거합니다. |

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure CLI 설명서](../service-fabric-azure-cli-2-0.md)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure CLI 샘플은 [Azure CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.

