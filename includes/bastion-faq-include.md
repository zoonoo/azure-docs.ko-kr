---
title: 포함 파일
description: 포함 파일
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 12/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c085ce4aba6760b09dcc3eaf2f6232c5cc904706
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469128"
---
### <a name="regions"></a>이용 가능한 지역은 어디인가요?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>내 가상 머신에서 공용 IP가 필요한가요?

Azure Bastion 서비스를 사용하여 연결하는 Azure Virtual Machine에는 공용 IP가 필요 없습니다. Bastion 서비스는 가상 네트워크 내부에서 가상 머신의 개인 IP를 통해 가상 머신에 대한 RDP/SSH 세션/연결을 설정합니다.

### <a name="is-ipv6-supported"></a>IPv6이 지원되나요?

현재 IPv6은 지원되지 않습니다. Azure Bastion은 IPv4만 지원합니다.

### <a name="rdpssh"></a>RDP 또는 SSH 클라이언트가 필요하나요?

Azure Portal에서 Azure 가상 머신에 RDP/SSH로 액세스할 때 RDP 또는 SSH 클라이언트가 필요하지 않습니다. [Azure Portal](https://portal.azure.com)을 사용하여 브라우저에서 직접 가상 머신에 대한RDP/SSH 액세스를 가져올 수 있습니다.

### <a name="agent"></a>Azure 가상 머신에서 실행되는 에이전트가 필요하나요?

브라우저 또는 Azure 가상 머신에 에이전트나 소프트웨어를 설치할 필요가 없습니다. Bastion 서비스는 에이전트 없이 사용할 수 있으며 RDP/SSH용 추가 소프트웨어가 필요하지 않습니다.

### <a name="browsers"></a>어떤 브라우저가 지원되나요?

Windows에서 Microsoft Edge 브라우저 또는 Chrome을 사용합니다. Apple Mac의 경우 Google Chrome 브라우저를 사용하세요. Microsoft Edge Chromium도 Windows와 Mac에서 모두 지원됩니다.

### <a name="roles"></a>가상 머신에 액세스할 때 꼭 필요한 역할이 있나요?

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="pricingpage"></a>가격 책정이란?

자세한 내용은 [가격 책정 페이지](https://aka.ms/BastionHostPricing)를 참조하세요.

### <a name="session"></a>왜 Bastion 세션이 시작하기 전에 "세션이 만료되었습니다." 오류 메시지를 받나요?

세션은 Azure Portal에서만 시작되어야 합니다. Azure Portal에 로그인하고 세션을 다시 시작합니다. 다른 브라우저 세션이나 탭에서 URL로 직접 이동하는 경우 이 오류가 예상됩니다. 세션이 더 안전한지 Azure Portal을 통해서만 세션에 액세스할 수 있는지 확인하도록 돕습니다.

### <a name="keyboard"></a>Bastion 원격 세션 중에 지원되는 키보드 레이아웃은?

Azure Bastion은 현재 VM 내에서 en-us-qwerty 키보드 레이아웃을 지원합니다.  키보드 레이아웃을 위한 다른 로캘에 대한 지원이 진행 중입니다.

### <a name="udr"></a>Azure Bastion 서브넷에서 UDR(사용자 정의 라우팅)이 지원됩니까?

아니요. Azure Bastion 서브넷에서 UDR은 지원되지 않습니다.
동일한 가상 네트워크에서 Azure Bastion과 Azure Firewall/NVA(네트워크 가상 어플라이언스)를 모두 포함하는 시나리오의 경우, Azure Bastion과 VM 간의 통신이 비공개이기 때문에 Azure Bastion 서브넷에서 Azure Firewall으로 트래픽을 강제로 전송할 필요가 없습니다. 자세한 내용은 [Bassion으로 Azure Firewall 뒤에 있는 VM에 액세스](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)를 참조하세요.

### <a name="filetransfer"></a>Azure Bastion RDP 세션에서 파일 전송이 지원되나요?

새 기능을 추가하기 위해 노력하고 있습니다. 현재 파일 전송은 지원되지 않지만 로드맵의 일부입니다. [Azure Bastion Feedback 페이지](https://feedback.azure.com/forums/217313-networking?category_id=367303)에서 새로운 기능에 대한 피드백을 공유해 주시기 바랍니다.
