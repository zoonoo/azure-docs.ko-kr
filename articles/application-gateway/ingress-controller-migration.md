---
title: Azure 애플리케이션 게이트웨이 수신 컨트롤러 투구에서 AGIC 추가 기능으로 마이그레이션하는 방법
description: 이 문서에서는 투구를 통해 배포 된 AGIC에서 AKS 추가 기능으로 배포 된 AGIC로 마이그레이션하는 방법에 대 한 지침을 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050837"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>AGIC 투구에서 AGIC 추가 기능으로 마이그레이션 

AGIC를 이미 배포 했지만 AKS 추가 기능으로 배포 된 AGIC로 마이그레이션하려는 경우 다음 단계를 통해 마이그레이션 프로세스를 안내할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항 
마이그레이션 프로세스를 시작 하기 전에 확인 해야 할 몇 가지 사항이 있습니다. 
  - [현재 AGIC 추가 기능에서 지원 되지](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)않는 AGIC 투구의 기능을 사용 하 고 있나요?
  - AKS 클러스터당 둘 이상의 AGIC 투구 배포를 사용 하 고 있나요? 
  - 하나의 Application Gateway를 대상으로 하는 여러 AGIC 투구 배포를 사용 하 고 있나요? 

위의 질문에 대 한 대답이 예 인 경우, AGIC 추가 기능은 사용 사례를 아직 지원 하지 않으므로 그 동안 AGIC 투구를 계속 사용 하는 것이 좋습니다. 그렇지 않으면 업무 외 시간에 아래 마이그레이션 프로세스를 계속 합니다. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>AGIC 투구가 현재 대상으로 하는 리소스 ID를 Application Gateway 찾습니다. 
AGIC 투구 배포가 대상으로 하는 Application Gateway로 이동 합니다. 해당 Application Gateway의 리소스 ID를 복사 하 고 저장 합니다. 이후 단계에서 리소스 ID가 필요 합니다. 리소스 ID는 포털에서 Application Gateway의 속성 탭 또는 Azure CLI를 통해 찾을 수 있습니다. 다음 예제에서는 리소스 그룹 *Myresourcegroup* 에서 *myapplicationgateway* 라는 게이트웨이에 대 한 Application Gateway 리소스 ID를 *appgwid* 에 저장 합니다.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>AKS 클러스터에서 AGIC 투구 삭제
Azure CLI를 통해 클러스터에서 AGIC 투구 배포를 삭제 합니다. AGIC AKS 추가 기능을 사용 하도록 설정 하기 전에 AGIC 투구 배포를 먼저 삭제 해야 합니다. AGIC 투구 배포를 삭제 하는 시간과 AGIC 추가 기능을 사용 하도록 설정 하는 시간 사이에 AKS 클러스터 내에서 발생 하는 모든 변경 내용은 Application Gateway에 반영 되지 않으므로 업무 시간 외에는 영향을 최소화 하기 위해이 마이그레이션 프로세스를 수행 해야 합니다. Application Gateway는 AGIC에서 마지막 구성을 계속 적용 하므로 기존 라우팅 규칙은 영향을 받지 않습니다. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>기존 Application Gateway를 사용 하 여 AGIC 추가 기능 사용 
이제 AKS 클러스터에서 AGIC 추가 기능을 사용 하도록 설정 하 여 Azure CLI 또는 포털을 통해 기존 Application Gateway를 대상으로 지정할 수 있습니다. 다음 Azure CLI 명령을 실행 하 여 AKS 클러스터에서 AGIC 추가 기능을 사용 하도록 설정 합니다. 이 예제에서는 이전 단계에서 위에서 저장 한 Application Gateway 리소스 ID *Appgwid* 를 사용 하 여 *myresourcegroup* 이라는 리소스 그룹에서 *myCluster* 이라는 클러스터의 추가 기능을 사용 하도록 설정 합니다. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

또는이 [링크](https://portal.azure.com/?feature.aksagic=true) 를 사용 하 여 포털에서 AKS 클러스터로 이동 하 고 클러스터의 네트워킹 탭에서 AGIC 추가 기능을 사용 하도록 설정할 수 있습니다. 추가 기능을 대상으로 하는 Application Gateway 선택 하는 경우 드롭다운 메뉴에서 기존 Application Gateway를 선택 합니다. 

![Application Gateway 수신 컨트롤러 포털](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>다음 단계
- [**Application Gateway 수신 컨트롤러 문제 해결**](ingress-controller-troubleshoot.md): AGIC에 대 한 문제 해결 가이드 
- [**Application Gateway 수신 컨트롤러 주석**](ingress-controller-annotations.md): AGIC의 주석 목록 