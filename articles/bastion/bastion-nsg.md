---
title: Azure 요새에서 VM 및 NSG 로 작업
description: 이 문서에서는 Azure 요새에 NSG 액세스를 통합하는 방법에 대해 설명합니다.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087260"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>NSG 액세스 및 Azure 요새 작업

Azure Bastion으로 작업할 때 NSG(네트워크 보안 그룹)를 사용할 수 있습니다. 자세한 내용은 [보안 그룹을](../virtual-network/security-overview.md)참조하십시오. 

![Architecture](./media/bastion-nsg/nsg-architecture.png)

이 다이어그램에서

* 배스션 호스트가 가상 네트워크에 배포됩니다.
* 사용자는 HTML5 브라우저를 사용하여 Azure Portal에 연결합니다.
* 사용자는 Azure 가상 머신을 RDP/SSH로 이동합니다.
* 통합 연결 - 브라우저 내부의 한 번 클릭 RDP/SSH 세션
* Azure VM에 공용 IP가 필요하지 않습니다.

## <a name="network-security-groups"></a><a name="nsg"></a>네트워크 보안 그룹

이 섹션에서는 사용자와 Azure 요새 간의 네트워크 트래픽과 가상 네트워크의 대상 VM을 통해 네트워크 트래픽을 보여 줍니다.

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure 요새는 Azure BastionSubnet에 특별히 배포됩니다.

* **트래픽 을 통해:**

   * **공용 인터넷의 트래픽 에 대한 정보:** Azure Bastion은 트래픽 을 입어에 대한 공용 IP에서 포트 443을 사용하도록 설정해야 하는 공용 IP를 만듭니다. 포트 3389/22AzureBastionSubnet에서 열 필요가 없습니다.
   * **Azure 요새 제어 평면에서 트래픽을 받아들입니다.** 제어 평면 연결을 위해 **GatewayManager** 서비스 태그에서 포트 443 인바운드를 사용하도록 설정합니다. 이렇게 하면 제어 평면, 즉 게이트웨이 관리자가 Azure Bastion과 대화할 수 있습니다.

* **트래픽 송신:**

   * **트래픽을 트래픽으로 타겟팅합니다.** Azure 요새는 개인 IP를 통해 대상 VM에 도달합니다. NSG는 포트 3389 및 22의 다른 대상 VM 서브넷으로 트래픽을 송신할 수 있어야 합니다.
   * **Azure의 다른 공용 끝점으로 트래픽을 송신합니다.** Azure Bastion은 Azure 내의 다양한 공용 끝점에 연결할 수 있어야 합니다(예: 진단 로그 및 계량 로그 저장). 이러한 이유로 Azure Bastion은 **AzureCloud** 서비스 태그에 443으로 아웃바운드해야 합니다.

* **대상 VM 서브넷:** RDP/SSH에 원하는 대상 가상 컴퓨터를 포함하는 서브넷입니다.

   * **Azure 요새에서 트래픽을 받아들입니다.** Azure 요새는 개인 IP를 통해 대상 VM에 도달합니다. RDP/SSH 포트(포트 3389/22)는 개인 IP를 통해 대상 VM 측에서 열어야 합니다. 가장 좋은 방법은 이 규칙에서 Azure Bastion Subnet IP 주소 범위를 추가하여 대상 VM 서브넷의 대상 VM에서 Bastion만 이러한 포트를 열 수 있도록 허용할 수 있습니다.

## <a name="apply-nsgs-to-azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet에 NSG 적용

***AzureBastionSubnet에***NSG를 만들고 적용하는 경우 NSG에 다음 규칙을 추가했는지 확인합니다. 이러한 규칙을 추가하지 않으면 NSG 생성/업데이트가 실패합니다.

* **평면 연결 제어:** 게이트웨이관리자에서 443인바운드
* **진단 로깅 및 기타:** 443에서 AzureCloud로 의 아웃바운드. 이 서비스 태그 내의 지역 태그는 아직 지원되지 않습니다.
* **대상 VM:** 3389 및 22에서 가상 네트워크로 의 아웃바운드

NSG 규칙 예제는 이 [빠른 시작 템플릿에서](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 요새에 대한 자세한 내용은 [FAQ](bastion-faq.md)를 참조하십시오.
