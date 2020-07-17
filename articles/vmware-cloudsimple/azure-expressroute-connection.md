---
title: CloudSimple의 azure VMware 솔루션-Express 경로를 사용 하 여 사설 클라우드를 Azure 네트워크에 연결
description: Express 경로를 사용 하 여 CloudSimple 사설 클라우드 환경을 Azure virtual network에 연결 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77015238"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Express 경로를 사용 하 여 CloudSimple 사설 클라우드 환경을 Azure virtual network에 연결

Azure Express 경로를 사용 하 여 CloudSimple 사설 클라우드를 Azure virtual network에 연결할 수 있습니다.  높은 대역폭의 낮은 대기 시간 연결을 사용 하면 사설 클라우드 환경에서 Azure 구독에서 실행 중인 서비스에 액세스할 수 있습니다.

가상 네트워크 연결을 사용 하 여 다음을 수행할 수 있습니다.

* 사설 클라우드의 가상 컴퓨터에 대 한 백업 대상으로 Azure를 사용 합니다.
* Azure 구독에 KMS 서버를 배포 하 여 사설 클라우드 vSAN 데이터 저장소를 암호화 합니다.
* 응용 프로그램 및 데이터베이스 계층이 사설 클라우드에서 실행 되는 동안 응용 프로그램의 웹 계층이 공용 클라우드에서 실행 되는 하이브리드 응용 프로그램을 사용 합니다.

![가상 네트워크에 대 한 Azure Express 경로 연결](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>가상 네트워크 연결 설정

사설 클라우드에 대 한 가상 네트워크 연결을 설정 하려면 권한 부여 키, 피어 회로 URI 및 Azure 구독에 대 한 액세스가 필요 합니다. 이 정보는 CloudSimple 포털의 Virtual Network 연결 페이지에서 사용할 수 있습니다. 지침은 [Azure virtual network에 대 한 피어 링 정보를 CloudSimple 연결로 가져오기](virtual-network-connection.md)를 참조 하세요. 정보를 얻는 데 문제가 있는 경우 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원 요청</a>을 제출 합니다.

> [!TIP]
> Azure 가상 네트워크, 게이트웨이 서브넷 및 가상 네트워크 게이트웨이가 이미 있는 경우 4 단계로 건너뛸 수 있습니다.

1. Azure 구독에서 가상 네트워크를 만들고 선택한 주소 공간이 사설 클라우드의 주소 공간과 다른 지 확인 합니다.  Azure 가상 네트워크가 이미 있는 경우 기존 항목을 사용할 수 있습니다.  자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md)를 참조 하세요.
2. Azure 가상 네트워크에 게이트웨이 서브넷을 만듭니다.  Azure 가상 네트워크에 게이트웨이 서브넷이 이미 있는 경우 기존 게이트웨이 서브넷을 사용할 수 있습니다. 자세한 내용은 [게이트웨이 서브넷 만들기](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)를 참조 하세요.
3. 가상 네트워크에서 가상 네트워크 게이트웨이를 만듭니다.  기존 가상 네트워크 게이트웨이가 있는 경우 기존 가상 네트워크 게이트웨이를 사용할 수 있습니다. 자세한 내용은 [가상 네트워크 게이트웨이 만들기](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)를 참조 하세요.
4. [가상 네트워크를 회로에 연결 하는 구독](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)에 설명 된 대로 권한 부여 키를 교환 하 여 가상 네트워크와 사설 클라우드 간에 연결을 만듭니다.

> [!WARNING]
> 기존 가상 네트워크 게이트웨이를 사용 하 고 CloudSimple Express 경로 회로와 동일한 위치에 대 한 Express 경로 연결을 사용 하는 경우 연결이 설정 되지 않습니다.  새 가상 네트워크를 만들고 이전 단계를 따릅니다.

## <a name="test-the-virtual-network-connection"></a>가상 네트워크 연결 테스트

연결이 만들어지면 **설정**에서 **속성** 을 선택 하 여 연결 상태를 확인할 수 있습니다.  상태 및 프로 비전 상태가 **성공**으로 표시 되어야 합니다.

![연결 상태](media/azure-expressroute-connection.png)

가상 네트워크 연결을 테스트 하려면:

1. Azure 구독에서 가상 머신을 만듭니다.
2. 사설 클라우드 vCenter의 IP 주소를 찾습니다 (환영 전자 메일 참조).
3. Azure 가상 네트워크에서 만든 가상 머신에서 Cloud vCenter를 Ping 합니다.
4. 사설 클라우드 vCenter에서 실행 되는 가상 머신에서 Azure 가상 머신을 Ping 합니다.

연결을 설정 하는 동안 문제가 발생 한 경우 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원 요청</a>을 제출 합니다.
