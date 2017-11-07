---
title: "개인 도메인에 Azure DNS 사용 | Microsoft Docs"
description: "Microsoft Azure의 개인 DNS 호스팅 서비스에 대한 개요입니다."
services: dns
documentationcenter: na
author: garbrad
manager: 
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2017
ms.author: garbrad
ms.openlocfilehash: ed47a9d850995aaf9e78bfde8b6a5fd80dc36918
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-dns-for-private-domains"></a>개인 도메인에 Azure DNS 사용
DNS(Domain Name System)는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다. Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다.  인터넷 연결 DNS 도메인 외에도 Azure DNS는 이제 사설 DNS 도메인을 미리 보기 기능으로 지원합니다.  
 
Azure DNS는 사용자 지정 DNS 솔루션을 추가하지 않고도 가상 네트워크의 도메인 이름을 관리하고 확인할 수 있는 안정적이고 신뢰할 수 있는 DNS 서비스를 제공합니다. 사설 DNS 영역을 사용하면 현재 Azure에서 제공하는 이름 대신 사용자 고유의 사용자 지정 도메인 이름을 사용할 수 있습니다.  사용자 지정 도메인 이름을 사용하면 조직의 요구 사항에 가장 적합하도록 가상 네트워크 아키텍처를 조정할 수 있습니다. 가상 네트워크 내에서 그리고 가상 네트워크 간에 이름을 확인할 수 있게 해줍니다. 또한 분할 수평 보기로 영역을 구성하여 사설 및 공용 DNS 영역에서 동일한 이름을 공유할 수 있습니다.

![DNS 개요](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>이점

* **사용자 지정 DNS 솔루션이 필요 없습니다.** 이전에는 많은 고객이 가상 네트워크에서 DNS 영역을 관리하기 위해 사용자 지정 DNS 솔루션을 만들었습니다.  이제는 Azure의 기본 인프라를 사용하여 DNS 영역 관리를 수행할 수 있으므로 사용자 지정 DNS 솔루션을 만들고 관리해야 하는 부담이 사라졌습니다.

* **모든 공용 DNS 레코드 형식을 사용합니다.**  Azure DNS는 A, AAAA, CNAME, MX, NS, PTR, SOA, SRV 및 TXT 레코드를 지원합니다.

* **자동 호스트 레코드 관리.** Azure는 사용자 지정 DNS 레코드를 호스팅할 뿐 아니라 VM의 호스트 이름 레코드를 지정된 가상 네트워크에 자동으로 보존합니다.  따라서 사용자 지정 DNS 솔루션을 만들거나 응용 프로그램을 수정할 필요 없이 사용하는 도메인 이름을 최적화할 수 있습니다.

* **가상 네트워크 간의 호스트 이름 확인.** Azure에서 제공하는 호스트 이름과는 달리, 사설 DNS 영역은 가상 네트워크 간에 공유할 수 있습니다.  이 기능은 가상 네트워크 피어링 같은 교차 네트워크 및 서비스 검색 시나리오를 단순화합니다.

* **친숙한 도구 및 사용자 환경.** 이 새로운 기능은 학습 곡선을 단축할 수 있도록 기존의 친숙한 Azure DNS 도구(PowerShell, Resource Manager 템플릿, REST API)를 사용하며 조만간 CLI 및 포털에 대한 지원이 추가될 예정입니다.

* **수평 분할 DNS 지원.** Azure DNS를 사용하면 이름이 같고 가상 네트워크 내 그리고 공용 인터넷의 다른 답변을 확인하는 영역을 만들 수 있습니다.  분할 수평 DNS에 대한 일반적인 시나리오는 가상 네트워크 내에서 사용할 수 있는 전용 서비스 버전을 제공하는 것입니다.


## <a name="pricing"></a>가격

관리되는 미리 보기 기간에는 사설 DNS 영역을 무료로 사용할 수 있습니다. 일반 공급이 발표되면 이 기능은 기존 Azure DNS 제품과 비슷한 사용량 기반 가격 책정 모델을 사용합니다. 


## <a name="next-steps"></a>다음 단계

Azure DNS에서 [사설 DNS 영역을 만드는](./private-dns-getstarted-powershell.md) 방법을 알아봅니다.

[DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하여 DNS 영역 및 레코드에 대해 알아봅니다.

Azure의 다른 주요 [네트워킹 기능](../networking/networking-overview.md)을 알아봅니다.

