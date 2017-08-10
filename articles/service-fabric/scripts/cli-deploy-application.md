---
title: "Azure CLI 스크립트 샘플 - 클러스터에 응용 프로그램 배포"
description: "Azure CLI 스크립트 샘플 - Service Fabric 클러스터에 응용 프로그램 배포"
services: service-fabric
documentationcenter: 
author: Thraka
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
ms.openlocfilehash: de6ec4378a05656ecefefa7d5994a4f5dad404ba
ms.contentlocale: ko-kr
ms.lasthandoff: 07/25/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Service Fabric 클러스터에 응용 프로그램 배포

이 샘플 스크립트는 클러스터 이미지 저장소에 응용 프로그램 패키지를 복사하고, 응용 프로그램 유형을 클러스터에 등록하고, 해당 응용 프로그램 유형에서 응용 프로그램 인스턴스를 만듭니다.  기본 서비스가 대상 응용 프로그램 형식의 응용 프로그램 매니페스트에 정의된 경우 이때 이러한 서비스도 생성됩니다.

필요한 경우 [Azure CLI](../service-fabric-azure-cli-2-0.md)를 설치합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "클러스터에 응용 프로그램 배포")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플이 실행된 후에 [응용 프로그램 제거](cli-remove-application.md)의 스크립트를 사용하여 응용 프로그램 인스턴스를 제거하고, 응용 프로그램 유형을 등록 취소하고, 이미지 저장소에서 응용 프로그램 패키지를 삭제할 수 있습니다.

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.


| 명령 | 참고 사항 |
|---|---|
| [sf cluster select](/cli/azure/sf/cluster#select) | 작업할 클러스터를 선택합니다. |
| [sf application upload](/cli/azure/sf/application#upload) | 앱 파일 및 매니페스트를 업로드합니다. |
| [sf application provision](/cli/azure/sf/application#provision) | 클러스터에 응용 프로그램을 등록합니다.|
| [sf application create](/cli/azure/sf/application#create) | 응용 프로그램의 인스턴스를 만들고 노드에 정의된 모든 서비스를 배포합니다. |

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure CLI 설명서](../service-fabric-azure-cli-2-0.md)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure CLI 샘플은 [Azure CLI 샘플](../samples-cli.md)에서 확인할 수 있습니다.

