---

title: "Azure RemoteApp에서 Citrix XenApp Essentials로 마이그레이션하는 방법 | Microsoft Docs"
description: "Azure RemoteApp에서 Citrix XenApp Essentials로 마이그레이션하는 방법"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 695a8165-3454-4855-8e21-f2eb2c61201b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: de47edc5ea4dc1b67bd69b74929acf249b0d43e6
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017


---

# <a name="how-to-migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Azure RemoteApp에서 Citrix XenApp Essentials로 마이그레이션하는 방법

Citrix XenApp Essentials로 마이그레이션하려는 Azure RemoteApp 고객을 위한 몇 가지 필수 구성 요소가 있습니다.  Citrix의 [Citrix XenApp Essentials의 단계별 기술 배포 가이드](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) 및 해당 [온라인 기술 라이브러리](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html)를 먼저 참고하는 것이 좋습니다. 

다행히 대부분 Azure RemoteApp에서 투자한 대부분을 다시 사용할 수 있지만 XenApp Essentials를 배포하기 위한 몇 가지 높은 수준의 필수 구성 요소가 있습니다.

## <a name="prerequisites"></a>필수 조건

1. 새 VNET을 만들거나 Citrix XenApp Essentials를 배포할 수 있는 Azure Resource Manager의 Azure VNET을 결정합니다. Azure RemoteApp에서는 Azure 클래식을 사용합니다. Citrix XenApp Essentials는 Azure Resource Manager만 지원합니다.  
2. Citrix가 하이브리드 배포만을 지원하기 때문에 선택한 VNET에 도메인 컨트롤러에 대한 네트워킹 액세스 권한이 있는지 확인합니다. Azure RemoteApp의 클라우드 배포를 사용하는 경우 VNET에 Active Directory 도메인 컨트롤러에 대한 네트워킹 액세스 권한이 있는지 확인하거나 AAD-DS(Azure Active Directly Domain Services)를 사용하는 것이 좋습니다. 
3. 도메인 조인이 첫 번째 시도에 성공할 수 있도록 DNS가 VNET에 대해 올바르게 구성되어 있는지 확인합니다. 선택한 VNET에서 가상 컴퓨터를 만들고 수동 도메인 조인을 수행하여 DNS 및 도메인 조인이 예상대로 작동하는지 확인할 수 있습니다. 이렇게 하면 첫 번째에 Citrix XenApp Essentials를 성공적으로 배포할 수 있습니다. 
4. 필요한 경우 Azure RemoteApp에서 사용 중인 Azure 클래식과 Azure Resource Manager VNET이 동일한 지역에 있으면 두 기능 간에 VNET 피어링을 만듭니다. 그리고 다른 지역에 있으면 네트워킹에 VNET을 연결하기 위해 S2S VPN을 사용합니다. 
5. 필요한 경우 [Azure RemoteApp 내부/외부로 데이터를 마이그레이션하는 방법](remoteapp-migrate.md)을 참조하세요. 
6. 기존 Azure RemoteApp 이미지를 업데이트하여 Citrix VDA 구성 요소를 포함하고 지침에 따라 Citrix 설명서를 참조합니다. 
7. Azure Marketplace로 이동하고 Citrix XenApp Essentials를 배포하기 시작합니다.

행운을 빌며 Azure RemoteApp을 사용해 주셔서 감사합니다. 

## <a name="other-considerations"></a>기타 고려 사항:

- Citrix XenApp Essentials는 하이브리드 배포만 지원합니다. 즉, 도메인 조인을 수행하기 위해 도메인 컨트롤러에 대한 네트워크 액세스 권한이 필요합니다. Azure RemoteApp의 클라우드 배포를 사용하는 경우 AAD-DS를 사용하거나 VNET에 Active Directory 도메인 조인에 대한 액세스 권한이 있는지 확인해야 합니다. 
- CXE에 사용자 데이터를 이동하는 방법을 자세히 알아보려면 [Azure RemoteApp 간에 데이터를 마이그레이션하는 방법](remoteapp-migrate.md)을 참고하세요. 
- Citrix XenApp Essentials는 Active Directory 계정만을 지원합니다. Microsoft 계정을 지원하지 않습니다(@outlook.com, @msn.com, @hotmail.com). 

## <a name="understanding-billing-for-citrix-xenapp-essentials"></a>Citrix XenApp Essentials에 대한 요금 청구 이해 

가격 책정에 대한 자세한 내용은 [FAQ](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) 및 [Citrix 개요 문서](https://www.citrix.com/global-partners/microsoft/remote-app.html)를 참고하세요. Citrix XenApp Essentials에 대한 세 가지 청구 구성 요소는 다음과 같습니다.

1. Citrix의 사용자당 월별 서비스 요금은 $12입니다. Azure Marketplace 구매와 같이 Azure 구독에 연결된 지불 방법으로 청구됩니다. EA 고객의 경우 Azure 금액 크레딧을 사용할 수 없습니다. 
2. RDS CAL 고유한 RDS CAL(출시 예정)을 가져오거나 Citrix XenApp Essentials 결제 정보와 함께 제공되는 RAF(원격 액세스 요금)를 $6.25(USD)로 구매해야 합니다. EA 고객의 경우 여기에 Azure 금액 크레딧을 사용할 수 있습니다. 기존 RDS CAL을 사용하려는 경우 청구서에 적용할 수 있도록 [arainfo@microsoft.com](mailto:arainfo@microsoft.com)으로 문의하세요. 
3. Azure 계산 및 저장소 이 기능은 VM에서 사용한 Azure Storage 비용 및 계산 소비입니다. VM 크기와 사용자 밀도를 선택할 때 가격 책정에 주의하세요. EA 고객의 경우 여기에 Azure 금액 크레딧을 사용할 수 있습니다.

질문이 있으면 문의하세요.
1. [arainfo@microsoft.com](mailto:arainfo@microsoft.com)으로 메일을 보내세요.
2. [Azure 지원에 문의하세요](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 먼저 [Azure 지원 사례를 열어서](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 위의 #1~5 단계에 도움이 되도록 합니다. #6~7 단계의 경우 Citrix 관리 포털 내에서 지원 티켓을 열어서 Citrix에 문의하세요. 

