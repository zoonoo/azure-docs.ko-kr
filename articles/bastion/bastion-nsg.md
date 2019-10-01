---
title: Azure 방호에서 Vm 및 NSGs 작업 | Microsoft Docs
description: 이 문서에서는 Azure 방호에 NSG 액세스를 통합 하는 방법을 설명 합니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694938"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>NSG 액세스 및 Azure 방호 (미리 보기) 작업

Azure 방호에서 작업 하는 경우 NSGs (네트워크 보안 그룹)를 사용할 수 있습니다. 자세한 내용은 [보안 그룹](../virtual-network/security-overview.md)을 참조 하세요. 

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

![아키텍처](./media/bastion-nsg/nsg_architecture.png)

이 다이어그램에서

* Bastion 호스트는 가상 네트워크에 배포되어 있습니다.
* 사용자는 HTML5 브라우저를 사용하여 Azure Portal에 연결합니다.
* 사용자가 연결할 가상 머신을 선택합니다.
* 한 번의 클릭으로 RDP/SSH 세션이 브라우저에서 열립니다.
* Azure VM에 공용 IP가 필요하지 않습니다.

## <a name="nsg"></a>네트워크 보안 그룹

* **AzureBastionSubnet:** Azure 방호은 특정 AzureBastionSubnet 배포 됩니다.  
    * **공용 인터넷에서 수신 되는 트래픽:** Azure 방호는 수신 트래픽에 대 한 공용 IP에서 포트 443를 사용 하도록 설정 해야 하는 공용 IP를 만듭니다. AzureBastionSubnet에서 포트 3389/22을 열 필요는 없습니다.
    * **대상 Vm에 대 한 송신 트래픽:** Azure 방호는 개인 IP를 통해 대상 Vm에 도달 합니다. NSGs는 다른 대상 VM 서브넷에 대 한 송신 트래픽을 허용 해야 합니다.
* **대상 VM 서브넷:** 이 서브넷은 RDP/SSH 하려는 대상 가상 머신을 포함 하는 서브넷입니다.
    * **Azure 방호의 수신 트래픽:** Azure 방호는 개인 IP를 통해 대상 VM에 연결 됩니다. RDP/SSH 포트 (각각 포트 3389 및 22)는 개인 IP를 통해 대상 VM 쪽에서 열어야 합니다.

## <a name="apply"></a>AzureBastionSubnet에 NSGs 적용

**AzureBastionSubnet**에 nsgs를 적용 하는 경우 Azure 제어 평면과 인프라에 대해 다음과 같은 두 가지 서비스 태그를 허용 합니다.

* **게이트웨이 관리자 (리소스 관리자에만 해당)** : 이 태그는 Azure Gateway Manager 서비스의 주소 접두사를 나타냅니다. 값으로 Gmanager를 지정 하는 경우에는 게이트웨이 관리자에 대 한 트래픽이 허용 되거나 거부 됩니다.  AzureBastionSubnet에서 NSGs를 만드는 경우 인바운드 트래픽에 대해 Gmanager 태그를 사용 하도록 설정 합니다.

* **Azurecloud (리소스 관리자에만 해당)** : 이 태그는 모든 데이터 센터 공용 IP 주소를 포함 하는 Azure의 IP 주소 공간을 나타냅니다. 값에 대해 AzureCloud를 지정 하는 경우 Azure 공용 IP 주소에 대 한 트래픽이 허용 되거나 거부 됩니다. 특정 지역의 AzureCloud에만 액세스를 허용 하려는 경우 지역을 지정할 수 있습니다. 예를 들어 미국 동부 지역에서 Azure AzureCloud에만 액세스를 허용 하려는 경우 서비스 태그로 AzureCloud를 지정할 수 있습니다. AzureBastionSubnet에서 NSGs를 만드는 경우 아웃 바운드 트래픽에 대해 AzureCloud 태그를 사용 하도록 설정 합니다. 인터넷에 대 한 인바운드 포트 443을 여는 경우 인바운드 트래픽에 대해 AzureCloud 태그를 사용 하도록 설정할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

Azure 방호에 대 한 자세한 내용은 [FAQ](bastion-faq.md) 를 참조 하세요.
