---
title: Azure Portal을 사용하여 영역 중복 공용 IP 주소 프런트 엔드로 공용 부하 분산 장치 표준 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 영역 중복 공용 IP 주소 프런트 엔드로 공용 부하 분산 장치 표준을 만드는 방법 알아보기
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 10a264609469245d4743886b58730304da3df7bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Azure Portal을 사용하여 영역 중복 공용 IP 주소 프런트 엔드로 공용 부하 분산 장치 표준 만들기

이 문서에서는 공용 IP 표준 주소를 사용하는 영역 중복 프런트 엔드로 공용 [부하 분산 장치 표준](https://aka.ms/azureloadbalancerstandard)을 만드는 단계를 안내합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="register-for-availability-zones-preview"></a>가용성 영역 미리 보기에 등록
 
가용성 영역은 미리 보기 상태이며 개발 및 테스트 시나리오에 대한 준비를 마쳤습니다. Azure 리소스, 지역 및 VM 크기 제품군을 선택하는 데 지원을 사용할 수 있습니다. 시작하는 방법에 대한 자세한 내용 및 가용성 영역을 사용해 볼 수 있는 Azure 리소스, 지역 및 VM 크기 제품군은 [가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)를 참조하세요. 지원을 위해 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones)에 연결하거나 [Azure 지원 티켓을 열](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 수 있습니다.  

## <a name="log-in-to-azure"></a>Azure에 로그인 

https://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="create-a-zone-redundant-load-balancer"></a>영역 중복 부하 분산 장치 만들기

1. 브라우저에서 Azure Portal [http://portal.azure.com](http://portal.azure.com)로 이동하고 Azure 계정으로 로그인합니다.
2. 화면 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **부하 분산 장치**를 선택합니다.
3. **부하 분산 장치 만들기에서 **이름**에 **myPublicLB**를 입력합니다.
4. **형식**에서 **Public**을 선택합니다.
5. SKU에서 **표준(미리 보기)**을 선택합니다.
6. **공용 IP 주소**를 클릭하고, **새로 만들기**를 클릭하고, **공용 IP 주소 만들기** 페이지에서 이름에 **myPublicIPStandard**를 입력하고, **가용성 영역(미리 보기)**에서 **영역 중복**을 선택합니다.
7. **위치**에서 **미국 동부2**를 선택하고 **확인**을 클릭합니다. 그러면 부하 분산 장치에서 배포를 시작하며 배포를 완료하는 데 몇 분 정도가 걸립니다.

    ![Azure Portal을 사용하여 영역 중복 부하 분산 장치 표준 만들기](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>다음 단계
- [가용성 영역에 공용 IP 만드는](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) 방법 알아보기



