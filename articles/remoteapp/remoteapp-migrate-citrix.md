---

title: "Azure RemoteApp에서 Citrix XenApp Essentials로 마이그레이션 | Microsoft Docs"
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
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: fcd96a466d1c0dad17d7012308281ef868463b19
ms.contentlocale: ko-kr
ms.lasthandoff: 06/02/2017


---

# <a name="migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Azure RemoteApp에서 Citrix XenApp Essentials로 마이그레이션

Azure RemoteApp를 사용하고 Citrix XenApp Essentials로 마이그레이션하려는 몇 가지 필수 구성 요소에 유념해야 합니다. 먼저 Citrix의 [Citrix XenApp Essentials의 단계별 기술 배포 가이드](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) 및 해당 [ 온라인 기술 라이브러리](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html)를 참고하세요. 

## <a name="prerequisite-steps-for-migration"></a>마이그레이션을 위한 필수 구성 요소 단계

1. 새로운 가상 네트워크를 만들거나 Azure Resource Manager에서 Citrix XenApp Essentials에 배포할 Azure Virtual Network를 결정합니다. Azure RemoteApp에서는 Azure 클래식 포털을 사용합니다. Citrix XenApp Essentials는 Azure Resource Manager만 지원합니다.  
2. Citrix는 하이브리드 배포만 지원하기 때문에 선택한 가상 네트워크에 도메인 컨트롤러에 대한 네트워킹 액세스 권한이 있는지 확인합니다. Azure RemoteApp의 클라우드 배포를 사용하는 경우 가상 네트워크가 Active Directory 도메인 컨트롤러에 대한 네트워킹 액세스 권한이 있는지 확인합니다. Azure Active Directory Domain Services(Azure AD DS)를 사용할 수도 있습니다. 
3. 첫 번째 시도에서 도메인 가입에 성공할 수 있도록 DNS가 가상 네트워크에 대해 올바르게 구성되어 있는지 확인합니다. 선택한 가상 네트워크에서 VM(가상 컴퓨터)을 만들고 수동 도메인 가입을 수행하여 DNS 및 도메인 가입이 예상대로 작동하는지 확인할 수 있습니다. 이렇게 하면 Citrix XenApp Essentials를 처음 배포할 때 성공할 수 있습니다. 
4. 필요한 경우 Azure RemoteApp과 함께 사용하는 Azure 클래식 포털 가상 네트워크와 Azure Resource Manager 가상 네트워크간에 가상 네트워크 피어링을 만듭니다. 이 피어링 프로세스는 두 네트워크가 동일한 지역에 있는 경우 작동합니다. 그렇지 않은 경우 사이트 간 VPN을 사용하여 네트워킹용 가상 네트워크를 연결합니다. 
5. 필요한 경우 [Azure RemoteApp 내부/외부로 데이터를 마이그레이션하는 방법](remoteapp-migrate.md)을 참조하세요. 
6. 기존 Azure RemoteApp 이미지를 업데이트하여 Citrix VDA 구성 요소를 포함시킵니다(지침은 Citrix 설명서 참조). 
7. Azure Marketplace로 이동하고 Citrix XenApp Essentials 배포를 시작합니다.

## <a name="other-considerations"></a>기타 고려 사항

마이그레이션 할 때 다음과 같은 추가 고려 사항에 유념하세요.
- Citrix XenApp Essentials는 하이브리드 배포만 지원합니다. 즉, 도메인 조인을 수행하기 위해 도메인 컨트롤러에 대한 네트워크 액세스 권한이 필요합니다. Azure RemoteApp의 클라우드 배포를 사용하는 경우 Azure AD DS를 사용하거나 도메인 가입을 위해 가상 네트워크에 Active Directory에 대한 액세스 권한이 있는지 확인합니다. 
- Citrix XenApp Essentials로 사용자 데이터를 이동하는 방법을 알아보려면 [Azure RemoteApp 내부/외부로 데이터를 마이그레이션하는 방법](remoteapp-migrate.md)을 참조하세요. 
- Citrix XenApp Essentials는 Active Directory 계정만을 지원합니다. Microsoft 계정(예 : outlook.com, msn.com 또는 hotmail.com)은 지원하지 않습니다. 

## <a name="citrix-xenapp-essentials-billing"></a>Citrix XenApp Essentials 청구

가격 책정에 대한 자세한 내용은 [FAQ](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) 및 [Citrix 개요 문서](https://www.citrix.com/global-partners/microsoft/remote-app.html)를 참고하세요. Citrix XenApp Essentials에 대해 세 가지 청구 구성 요소가 있습니다.

- Citrix 서비스 요금은 월 사용자당 $12입니다. 모든 Azure Marketplace 구매와 같이 Azure 구독에 연결된 지불 방법으로 청구됩니다. EA(기업 계약) 고객의 경우 Azure 금액 크레딧을 사용할 수 없습니다. 
- RDS(Remote Data Services) CAL(client access licenses) 현재 Citrix XenApp Essentials 결제 정보와 함께 제공되는 원격 액세스 요금은 $ 6.25로 구입할 수 있습니다. EA 고객은 Azure 금액 크레딧을 사용하여 지불할 수 있습니다. 기존 RDS CAL을 사용하려면 청구서에 적용할 수 있도록 [arainfo@microsoft.com](mailto:arainfo@microsoft.com)으로 문의하세요. 
- Azure 계산 및 저장소 이 기능은 VM에서 사용한 Azure Storage 비용 및 계산 소비입니다. VM 크기와 사용자 밀도를 선택할 때 가격 책정에 주의하세요. EA 고객은 Azure 금액 크레딧을 사용하여 지불할 수 있습니다.

다른 질문이 있으면:
- [arainfo@microsoft.com](mailto:arainfo@microsoft.com)으로 메일을 보내세요.
- [Azure 지원에 문의하세요](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 먼저 [Azure 지원 사례를 열어서](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 필수 조건 1~5 단계에 도움이 되도록 합니다. 6~7 단계의 경우 Citrix 관리 포털 내에서 지원 티켓을 열어서 Citrix에 문의하세요. 

