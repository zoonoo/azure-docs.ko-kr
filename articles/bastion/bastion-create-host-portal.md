---
title: 'Azure 요새 호스트 만들기: 포털'
description: 이 문서에서는 포털을 사용하여 Azure 요새 호스트를 만드는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366146"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>포털을 사용하여 Azure 요새 호스트 만들기

이 문서에서는 Azure 포털을 사용하여 Azure 요새 호스트를 만드는 방법을 보여 주며, 이 문서에서는 Azure 요새 호스트를 만드는 방법을 보여 주며, 이 문서에서는 가상 네트워크에서 Azure Bastion 서비스를 프로비전하면 동일한 가상 네트워크의 모든 VM에서 원활한 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크별로 수행됩니다.

모든 설정을 수동으로 지정하거나 기존 VM에 해당하는 설정을 사용하여 포털에서 새 요새 호스트 리소스를 만들 수 있습니다. VM 설정을 사용하여 방호 호스트를 만들려면 [빠른 시작](quickstart-host-portal.md) 문서를 참조하세요. 선택적으로 [Azure PowerShell을](bastion-create-host-powershell.md) 사용하여 Azure 요새 호스트를 만들 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

요새는 다음 Azure 공용 지역에서 사용할 수 있습니다.

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>요새 호스트 만들기

이 섹션에서는 Azure 포털에서 새 Azure 요새 리소스를 만드는 데 도움이 됩니다.

1. Azure [포털](https://portal.azure.com) 메뉴 또는 **홈** 페이지에서 **리소스 만들기를**선택합니다.

1. **새** 페이지에서 *마켓플레이스 검색* 필드에서 **바스티온을**입력한 다음 **Enter를** 클릭하여 검색 결과로 이동합니다.

1. 결과에서 **배스션을**클릭합니다. 게시자가 *Microsoft*이고 범주가 *네트워킹*인지 확인합니다.

1. **배스션 페이지에서 배스션** 만들기 페이지를 열려면 **만들기를** **클릭합니다.**

1. **대공 만들기** 페이지에서 새 요새 리소스를 구성합니다. 배스천 리소스의 구성 설정을 지정합니다.

    ![요새 만들기](./media/bastion-create-host-portal/settings.png)

    * **구독**: 새 바스티온 리소스를 만드는 데 사용할 Azure 구독입니다.
    * **리소스 그룹**: 새 바스티온 리소스가 생성되는 Azure 리소스 그룹입니다. 기존 리소스 그룹이 없는 경우 새 리소스 그룹을 만들 수 있습니다.
    * **이름**: 새 요새 리소스의 이름
    * **지역**: 리소스가 생성될 Azure 공용 지역입니다.
    * **가상 네트워크**: Bastion 리소스가 생성되는 가상 네트워크입니다. 이 프로세스 중에 포털에 새 가상 네트워크를 만들거나 기존 가상 네트워크를 사용할 수 있습니다. 기존 가상 네트워크를 사용하는 경우 기존 가상 네트워크에 Bastion 서브넷 요구 사항을 수용할 수 있는 충분한 여유 주소 공간이 있는지 확인합니다.
    * **서브넷**: 새 Bastion 호스트 리소스가 배포될 가상 네트워크의 서브넷입니다. 이름 값 **AzureBastionSubnet을**사용 하 여 서브넷을 만들어야 합니다. 이 값을 통해 Azure는 Bastion 리소스를 배포할 서브넷을 알 수 있습니다. 이는 게이트웨이 서브넷과 다릅니다. 최소 /27 이상의 서브넷(/27, /26 등)을 사용해야 합니다.
    
       경로 테이블이나 위임 없이 **AzureBastionSubnet을** 만듭니다. **AzureBastionSubnet에서**네트워크 보안 그룹을 사용하는 경우 [NSG사용 문서](bastion-nsg.md) 참조.
    * **공용 IP 주소**: RDP/SSH에 액세스할 배스션 리소스의 공용 IP(포트 443이상). 새 공용 IP를 만들거나 기존 IP를 사용합니다. 공용 IP 주소는 만드는 Bastion 리소스와 동일한 지역에 있어야 합니다.
    * **공용 IP 주소 이름**: 공용 IP 주소 리소스의 이름입니다.
    * **공용 IP 주소 SKU**: 이 설정은 기본적으로 **표준으로**미리 채워집니다. Azure 요새는 표준 공용 IP SKU만 사용/지원합니다.
    * **할당**: 이 설정은 기본적으로 **정적**에 미리 채워집니다.

1. 설정 지정을 완료하면 검토 **+ 만들기를**클릭합니다. 이렇게 하면 값의 유효성이 검사됩니다. 유효성 검사가 통과되면 생성 프로세스를 시작할 수 있습니다.
1. **대위 만들기** 페이지에서 을 **클릭합니다.**
1. 배포가 진행 중임을 알리는 메시지가 표시됩니다. 리소스가 생성되면 이 페이지에 상태가 표시됩니다. 요새 리소스를 만들고 배포하는 데 약 5분이 걸립니다.

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [배스션 FAQ를](bastion-faq.md) 참조하십시오.

* Azure 요새 서브넷이 있는 네트워크 보안 그룹을 사용하려면 [NSG사용 작업을](bastion-nsg.md)참조하십시오.