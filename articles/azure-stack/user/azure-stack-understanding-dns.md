---
title: Azure Stack에서 이해 Idn | Microsoft Docs
description: Azure Stack의 기능과 Idn 기능 이해
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b313772fb2737c015a02cfc36cec87797c95f619
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251965"
---
# <a name="introducing-idns-for-azure-stack"></a>Azure Stack 용 iDNS 소개

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Idn은 외부 DNS 이름을 확인할 수 있도록 Azure Stack 네트워킹 기능 (예를 들어 http://www.bing.com.) 내부 가상 네트워크 이름을 등록할 수도 있습니다. 이 작업을 수행 하면 IP 주소 대신 이름으로 동일한 가상 네트워크에 Vm을 해결할 수 있습니다. 이 방법은 제거 사용자 지정 DNS 서버 항목을 제공 해야 합니다. DNS에 대 한 자세한 내용은 참조는 [Azure DNS 개요](https://docs.microsoft.com/azure/dns/dns-overview)합니다.

## <a name="what-does-idns-do"></a>Idn 무엇 인가요?

Azure Stack에서 Idn은 다음 기능으로 사용자 지정 DNS 서버 항목을 지정할 필요 없이 이용할 수 있습니다.

- 테 넌 트 워크 로드에 대 한 DNS 이름 확인 서비스를 공유 합니다.
- 이름 확인 및 테 넌 트 가상 네트워크 내에서 DNS 등록에 대 한 권한 있는 DNS 서비스입니다.
- 테 넌 트 Vm에서에서 인터넷 이름 확인에 대 한 재귀 DNS 서비스입니다. 테 넌 트는 더 이상 인터넷 이름 (예: www.bing.com.)를 해결 하려면 사용자 지정 DNS 항목을 지정 해야

여전히 사용자 고유의 DNS를 제공 하 고 사용자 지정 DNS 서버를 사용할 수 있습니다. 그러나 Idn을 사용 하 여 인터넷 DNS 이름을 확인 하 고 동일한 가상 네트워크의 다른 Vm에 연결할 수, 사용자 지정 DNS 항목을 만들 필요가 없습니다.

## <a name="what-doesnt-idns-do"></a>Idn 수행 하지 않습니다.

어떤 Idn 수행 하기를 허용 하지 않는 가상 네트워크 외부에서 확인 될 수 있는 이름에 대 한 DNS 레코드를 만드는 것입니다.

Azure에서 공용 IP 주소와 연결 된 DNS 이름 레이블을 지정 하는 옵션도 있습니다. 레이블 (접두사)를 선택할 수 있지만 Azure 공용 IP 주소를 만든 지역을 기반으로 하는 접미사를 선택 합니다.

![DNS 이름 레이블 예제](media/azure-stack-understanding-dns-in-tp2/image3.png)

이전 이미지에서 볼 수 있듯이 Azure 만들어집니다 "A" 레코드를 DNS에 영역 아래에 지정 된 DNS 이름 레이블에 대 한 **westus.cloudapp.azure.com**합니다. 접두사와 접미사는 결합 하 여 작성 한 [정규화 된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)는 확인할 수에서 아무 곳 이나 공용 인터넷에서.

Azure Stack만 지원 Idn 내부 이름 등록에 대 한 하므로 다음을 수행할 수 없습니다.

- 기존 호스트 된 DNS 영역 (예: local.azurestack.external.)에서 DNS 레코드 만들기
- DNS 영역을 (예: Contoso.com)을 만듭니다.
- 사용자 고유의 사용자 지정 DNS 영역에서 레코드를 만듭니다.
- 도메인 이름 구입을 지원 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack의 DNS를 사용 하 여](azure-stack-dns.md)
