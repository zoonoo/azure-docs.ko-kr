---
title: 포함 파일
description: 포함 파일
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48a7912b1a6375111b6f9af1496cbbd41966202c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563376"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>이용 가능한 지역은 어디인가요?

[!INCLUDE [Azure Bastion region availability](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Azure Bastion을 통해 연결하려면 내 가상 머신에 공용 IP가 필요한가요?

아니요. Azure Bastion을 사용하여 VM에 연결하는 경우 연결하려는 Azure 가상 머신에는 공용 IP가 필요하지 않습니다. Bastion 서비스는 가상 네트워크 내부에서 가상 머신의 개인 IP를 통해 가상 머신에 대한 RDP/SSH 세션/연결을 설정합니다.

### <a name="is-ipv6-supported"></a>IPv6이 지원되나요?

현재 IPv6은 지원되지 않습니다. Azure Bastion은 IPv4만 지원합니다.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>RDP 또는 SSH 클라이언트가 필요하나요?

아니요. Azure Portal에서 Azure 가상 머신에 RDP/SSH로 액세스할 때 RDP 또는 SSH 클라이언트가 필요하지 않습니다. [Azure Portal](https://portal.azure.com)을 사용하여 브라우저에서 직접 가상 머신에 대한RDP/SSH 액세스를 가져올 수 있습니다.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Azure 가상 머신에서 실행되는 에이전트가 필요하나요?

아니요. 브라우저 또는 Azure 가상 머신에 에이전트나 소프트웨어를 설치할 필요가 없습니다. Bastion 서비스는 에이전트 없이 사용할 수 있으며 RDP/SSH용 추가 소프트웨어가 필요하지 않습니다.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>각 Azure Bastion에서 지원하는 동시 RDP 및 SSH 세션 수는?

RDP와 SSH는 모두 사용 기반 프로토콜입니다. 세션을 많이 사용하면 베스천 호스트에서 낮은 총 세션 수를 지원합니다. 아래 숫자는 정상적인 일상 워크플로를 가정합니다.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>RDP 세션에서는 어떤 기능이 지원되나요?

지금은 텍스트 복사/붙여넣기만 지원됩니다. 파일 복사와 같은 기능은 지원되지 않습니다. [Azure Bastion 피드백 페이지](https://feedback.azure.com/forums/217313-networking?category_id=367303)에서 새 기능에 대한 피드백을 공유해 주시기 바랍니다.

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Bastion 보안 강화는 AADJ VM 확장 조인 VM에서 작동하나요?

이 기능은 Azure AD 사용자를 사용하는 AADJ VM 확장 조인 머신에서 작동하지 않습니다. 자세한 내용은 [Windows Azure VM 및 Azure AD](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements)를 참조하세요.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>어떤 브라우저가 지원되나요?

Windows에서 Microsoft Edge 브라우저 또는 Chrome을 사용합니다. Apple Mac의 경우 Google Chrome 브라우저를 사용하세요. Microsoft Edge Chromium도 Windows와 Mac에서 모두 지원됩니다.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Azure Bastion은 고객 데이터를 어디에 저장하나요?

Azure Bastion은 배포된 지역 외부로 고객 데이터를 이동하거나 저장하지 않습니다.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>가상 머신에 액세스할 때 꼭 필요한 역할이 있나요?

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>가격 책정이란?

자세한 내용은 [가격 책정 페이지](https://aka.ms/BastionHostPricing)를 참조하세요.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Azure Bastion이 Azure 호스팅 VM에 관리 용도로 액세스하는 경우 RDS CAL이 필요한가요?

아니요. Azure Bastion이 관리 용도로만 Windows Server VM에 액세스하는 경우에는 [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab)이 필요하지 않습니다.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Bastion 원격 세션 중에 지원되는 키보드 레이아웃은?

Azure Bastion은 현재 VM 내에서 en-us-qwerty 키보드 레이아웃을 지원합니다.  키보드 레이아웃을 위한 다른 로캘에 대한 지원이 진행 중입니다.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Azure Bastion 서브넷에서 UDR(사용자 정의 라우팅)이 지원됩니까?

아니요. Azure Bastion 서브넷에서 UDR은 지원되지 않습니다.

동일한 가상 네트워크에서 Azure Bastion과 Azure Firewall/NVA(네트워크 가상 어플라이언스)를 모두 포함하는 시나리오의 경우, Azure Bastion과 VM 간의 통신이 비공개이기 때문에 Azure Bastion 서브넷에서 Azure Firewall으로 트래픽을 강제로 전송할 필요가 없습니다. 자세한 내용은 [Bassion으로 Azure Firewall 뒤에 있는 VM에 액세스](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)를 참조하세요.

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>왜 Bastion 세션이 시작하기 전에 "세션이 만료되었습니다." 오류 메시지를 받나요?

세션은 Azure Portal에서만 시작되어야 합니다. Azure Portal에 로그인하고 세션을 다시 시작합니다. 다른 브라우저 세션이나 탭에서 URL로 직접 이동하는 경우 이 오류가 예상됩니다. 세션이 더 안전한지 Azure Portal을 통해서만 세션에 액세스할 수 있는지 확인하도록 돕습니다.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>배포 실패를 처리하는 방법은?

오류 메시지를 검토하고 필요에 따라 [Azure Portal에서 지원 요청을 제기합니다](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). [Azure 구독 제한, 할당량 및 제약 조건](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)에서 배포 오류가 발생할 수 있습니다. 특히, 고객은 구독당 허용되는 공용 IP 주소 수 제한으로 인해 Azure Bastion 배포가 실패할 수 있습니다.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>재해 복구 계획에 Azure Bastion을 통합하려면 어떻게 할까요?

Azure Bastion은 VNet 또는 피어링된 VNet 내부에 배포되고 Azure 지역에 연결됩니다. Azure Bastion을 DR(재해 복구) 사이트 VNet에 배포하는 것은 고객의 책임입니다. Azure 지역에 장애가 발생하는 경우 VM을 DR 지역으로 장애 조치(failover)하세요. 그런 다음, DR 지역에 배포된 Azure Bastion 호스트를 사용하여 현재 여기에 배포된 VM에 연결합니다.