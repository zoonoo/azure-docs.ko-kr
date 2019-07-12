---
title: Vm 및 Azure 배스 천에서 Nsg를 사용 하 여 작업 | Microsoft Docs
description: 이 문서에서는 Azure 배스 천을 사용 하 여 NSG 액세스를 통합 하는 방법 설명
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 5312ad2593e732f4c84eb67ed263bc9e4666a67a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594186"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>NSG 액세스 및 Azure 배스 천 (미리 보기) 사용

Azure 배스 천으로 작업할 경우에 네트워크 보안 그룹 (Nsg)을 사용할 수 있습니다. 자세한 내용은 [보안 그룹](../virtual-network/security-overview.md)합니다. 

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

![아키텍처](./media/bastion-nsg/nsg_architecture.png)

이 다이어그램:

* 요새 호스트 가상 네트워크에 배포 됩니다.
* 사용자는 모든 HTML5 브라우저를 사용 하 여 Azure portal에 연결 합니다.
* 사용자에 연결할 가상 컴퓨터를 선택 합니다.
* 한 번의 클릭을 사용 하 여 RDP/SSH 세션을 브라우저에서 열립니다.
* Azure VM의 공용 IP 없음 필요 합니다.

## <a name="nsg"></a>네트워크 보안 그룹

* **AzureBastionSubnet:** Azure 배스 천 특정 AzureBastionSubnet에 배포 됩니다.  
    * **공용 인터넷에서 트래픽을 수신 합니다.** Azure 배스 천 포트 443에서 수신 트래픽에 대 한 공용 IP 사용 하도록 설정 해야 하는 공용 IP를 만듭니다. 포트 3389/22는 AzureBastionSubnet에서 열어야 필요가 없습니다.
    * **대상 Vm으로 나가는 트래픽이:** Azure 배스 천 개인 IP를 통해 대상 Vm에 도달 합니다. Nsg는 다른 대상 VM 서브넷에 들어오고 나가는 트래픽을 허용 해야 합니다.
* **대상 VM 서브넷:** 이 RDP/SSH를 추가 하려는 대상 가상 컴퓨터를 포함 하는 서브넷입니다.
    * **Azure 배스 천에서 수신 트래픽을 합니다.** 개인 IP를 통해 azure 배스 천 대상 VM에 도달 합니다. RDP/SSH 포트 (3389 포트 22 및 각각) 개인 IP를 통해 대상 쪽 VM에서 열려야 합니다.

## <a name="apply"></a>AzureBastionSubnet에 Nsg 적용

Nsg를 적용 하는 경우는 **AzureBastionSubnet**, Azure 제어 평면 및 인프라에 대 한 다음 두 서비스 태그를 허용 합니다.

* **(리소스 관리자에만 해당) GatewayManager**: 이 태그는 Azure Gateway Manager 서비스의 주소 접두사를 나타냅니다. 값에 대 한 GatewayManager를 지정 하는 경우에 트래픽이 허용 되거나 GatewayManager 거부 합니다.  AzureBastionSubnet에서 Nsg를 만드는 경우 인바운드 트래픽에 대해 GatewayManager 태그를 사용 하도록 설정 합니다.

* **AzureCloud (Resource Manager만 해당)** : 이 태그는 모든 데이터 센터 공용 IP 주소를 포함 하 여 Azure에 대 한 IP 주소 공간을 나타냅니다. 값에 대 한 azure 클라우드를 지정 하는 경우에 트래픽이 허용 되거나 Azure 공용 IP 주소를 거부 합니다. AzureCloud 특정 지역에만 액세스할 수 있도록 하려는 경우 지역을 지정할 수 있습니다. 예를 들어, 미국 동부 지역에서 Azure AzureCloud만 액세스할 수 있도록 하려는 경우 AzureCloud.EastUS를 서비스 태그로 지정할 수 있습니다. AzureBastionSubnet에서 Nsg를 만드는 경우 아웃 바운드 트래픽에 대해 AzureCloud 태그를 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

배스 천 Azure에 대 한 자세한 내용은 참조는 [FAQ](bastion-faq.md)
