---
title: Azure Stack의 DNS | Microsoft Docs
description: Azure Stack의 DNS를 사용 하 여
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 8459a5f88bf660ac460f778f67618e9805afa29d
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970709"
---
# <a name="using-dns-in-azure-stack"></a>Azure Stack의 DNS를 사용 하 여

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 다음 도메인 이름 시스템 (DNS) 기능을 지원 합니다.

* DNS 호스트 이름 확인
* DNS 영역 및 API를 사용 하 여 레코드 생성 및 관리

## <a name="support-for-dns-hostname-resolution"></a>DNS 호스트 이름 확인에 대 한 지원

공용 IP 리소스에 대 한 DNS 도메인 이름 레이블을 지정할 수 있습니다. 다음을 사용 하 여 azure Stack *domainnamelabel.location*. cloudapp.azurestack.external 레이블 이름 및 Azure Stack에서 공용 IP를 해결 하는 맵에 대 한 DNS 서버를 관리 합니다.

예를 들어, 사용 하 여 공용 IP 리소스를 만들면 **contoso** 로컬 Azure Stack 위치에서 도메인 이름 레이블로 합니다 [정규화 된 도메인 이름](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **contoso.local.cloudapp.azurestack.external** 리소스의 공용 IP 주소로 확인 합니다. 사용자 지정 도메인을 Azure Stack에서 공용 IP 주소를 가리키는 CNAME 레코드를 만들려면이 FQDN을 사용할 수 있습니다.

이름 확인에 대 한 자세한 내용은 참조는 [DNS 확인](https://docs.microsoft.com/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 문서.

> [!IMPORTANT]
> 만든 각 도메인 이름은 Azure Stack 위치 내에서 고유 해야 합니다.

다음 화면 캡처에서는 합니다 **공용 IP 주소 만들기** 포털을 사용 하 여 공용 IP 주소를 만드는 대화 상자.

![공용 IP 주소 만들기](media/azure-stack-whats-new-dns/image01.png)

**예제 시나리오**

부하 분산 장치는 웹 응용 프로그램에서 요청을 처리 해야 합니다. 뒤에 부하 분산 장치 하나 이상의 가상 머신에서 실행 중인 웹 사이트입니다. 웹 사이트 부하 분산 된 IP 주소 대신 DNS 이름을 사용 하 여 액세스할 수 있습니다.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>DNS 영역 및 API를 사용 하 여 레코드 생성 및 관리

만들기 및 Azure Stack의 DNS 영역 및 레코드를 관리할 수 있습니다.

Azure Stack Azure DNS Api를 사용 하 여 일치 하는 Api를 사용 하 여 Azure와 같은 DNS 서비스를 제공 합니다.  Azure Stack DNS에서 도메인을 호스트에 동일한 자격 증명, Api 및 도구를 사용 하 여 DNS 레코드를 관리할 수 있습니다. 동일한 청구를 사용 하 고 다른 Azure 서비스와 지원 수도 있습니다.

Azure Stack DNS 인프라는 Azure의 보다 간결 합니다. 크기와 위치는 Azure Stack 배포의 DNS 범위, 확장성 및 성능 영향 을지 것입니다. 즉, 성능, 가용성, 글로벌 분포 및 고가용성 배포 마다 달라질 수 있습니다.

## <a name="comparison-with-azure-dns"></a>Azure DNS 사용 하 여 비교

Azure Stack의 DNS는 Azure에서 DNS 비슷합니다도 이해 해야 하는 주요 예외가 있습니다.

* **AAAA 레코드를 지원 하지 않습니다.**

    Azure Stack에서 IPv6 주소를 지원 하지 않으므로 azure Stack AAAA 레코드를 지원 하지 않습니다.  이것이 Azure에서 DNS 및 Azure Stack 간의 주요 차이점입니다.
* **다중 테 넌 트 아닙니다.**

    Azure Stack의 DNS 서비스를 다중 테 넌 트 아닙니다. 각 테 넌 트 동일한 DNS 영역을 만들 수 없습니다. 영역을 만들려고 시도 하는 첫 번째 구독만 성공 하 고 후속 요청이 실패 합니다.  알려진된 문제 및 Azure 및 Azure Stack DNS 간의 주요 차이점은. 이후 릴리스에서이 문제를 해결 될 예정입니다.
* **태그, 메타 데이터 및 Etag**

    Azure Stack의 태그, 메타 데이터, Etag 및 제한을 처리 하는 방법에 약간의 차이가 있습니다.

Azure DNS에 대 한 자세한 내용은 참조 하세요 [DNS 영역 및 레코드](../../dns/dns-zones-records.md)합니다.

### <a name="tags-metadata-and-etags"></a>태그, 메타 데이터 및 Etag

**태그**

Azure Stack DNS는 DNS 영역 리소스에서 Azure Resource Manager 태그를 사용 하 여 지원 합니다. 다음에 설명 된 대로 DNS 레코드 집합에 대체 '메타 데이터'가 지원 되지만 DNS 레코드 집합의 태그는 지원 되지 않습니다.

**Metadata**

Azure Stack의 DNS 레코드 집합 태그 대신 'metadata'를 사용 하 여 레코드 집합에 주석 지정을 지원 합니다. 태그와 유사한 메타데이터를 사용하면 각 레코드 집합에 이름-값 쌍을 연결할 수 있습니다. 예를 들어이 각 레코드 집합의 목적은 기록할 유용할 수 있습니다. 태그와 달리, 메타데이터는 Azure 청구서를 필터링하여 표시하는 데 사용할 수 없으며 Azure Resource Manager 정책에서 지정할 수 없습니다.

**Etag**

두 사용자 또는 두 프로세스가 동시에 DNS 레코드 수정을 시도한다고 가정합니다. 어느 쪽이 성공할까요? 그리고 성공한 사용자 자신이 다른 사용자가 만든 변경 내용을 덮어썼다는 것을 알고 있을까요?

Azure Stack DNS는 Etag를 사용 하 여 동일한 리소스에 대 한 동시 변경을 안전 하 게 처리. Etag는 Azure Resource Manager '태그'에서 구분 됩니다. 각 DNS 리소스(영역 또는 레코드 집합)에는 Etag가 연결되어 있습니다. 리소스를 검색할 때마다 해당 Etag도 검색됩니다. 리소스를 업데이트할 때 전달할 다시 Etag Azure Stack DNS 하는지 확인할 수 있도록 Etag server 일치 항목을 선택할 수 있습니다. 리소스를 업데이트할 때마다 Etag가 다시 생성되므로 Etag 불일치는 동시 변경이 발생했음을 나타냅니다. 새 리소스를 만들 때도 Etag를 사용하여 리소스가 존재하지 않는지 확인합니다.

기본적으로 Azure Stack DNS PowerShell 영역에 대 한 동시 변경을 차단 및 레코드 집합에 Etag를 사용 합니다. 선택적 *-Overwrite* 스위치를 사용하여 Etag 검사를 무시할 수 있으며, 이 경우 발생한 동시 변경 내용을 덮어쓰게 됩니다.

Azure Stack DNS REST API의 수준에서 Etag는 HTTP 헤더를 사용 하 여 지정 됩니다. 해당 동작은 다음 표에 나와 있습니다.

| 헤더 | 동작|
|--------|---------|
| 없음   | PUT 항상 성공(Etag 검사 안 함)|
| If-match-| 리소스가 있고 Etag가 일치하는 경우에만 PUT 성공|
| If-match *| 리소스가 있는 경우에만 PUT 성공|
| If-none-match *| 리소스가 없는 경우에만 PUT 성공|

### <a name="limits"></a>제한

Azure Stack DNS를 사용 하는 경우 다음과 같은 기본 제한이 적용 됩니다.

| 리소스| 기본 제한|
|---------|--------------|
| 구독당 영역| 100|
| 영역당 레코드 집합| 5,000|
| 레코드 집합당 레코드| 20|

## <a name="next-steps"></a>다음 단계

[Azure Stack 용 iDNS 소개](azure-stack-understanding-dns.md)
