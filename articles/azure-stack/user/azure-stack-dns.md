---
title: "Azure 스택의 DNS | Microsoft Docs"
description: "Azure Stack의 DNS"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 394abe5295af4ed99e48d50b5886ac93af87e875
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="dns-in-azure-stack"></a>Azure Stack의 DNS

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택에는 다음과 같은 DNS 기능이 포함 됩니다.
* DNS 호스트 이름 확인에 대 한 지원
* DNS 영역 및 API를 사용 하 여 레코드 만들기 및 관리

## <a name="support-for-dns-hostname-resolution"></a>DNS 호스트 이름 확인에 대 한 지원
에 대 한 매핑을 만듭니다 공용 IP 리소스에 대 한 DNS 도메인 이름 레이블을 지정할 수 있습니다 *domainnamelabel.location*. cloudapp.azurestack.external Azure 스택에서 공용 IP 주소를 관리 하는 DNS 서버입니다.  

예를 들어 있는 공용 IP 리소스를 만들 경우 **contoso** 로컬 Azure 스택 위치, 정규화 된 도메인 이름 (FQDN)에 있는 도메인 이름 레이블과 **contoso.local.cloudapp.azurestack.external**리소스의 공용 IP 주소로 확인 합니다. Azure 스택에서 공용 IP 주소를 가리키는 CNAME 레코드는 사용자 지정 도메인을 만드는이 FQDN을 사용할 수 있습니다.

> [!IMPORTANT]
> 만든 각 도메인 이름 레이블이 해당 Azure 스택 위치 내에서 고유 해야 합니다.

포털을 사용 하 여 공용 IP 주소를 만드는 경우 다음과 같이 보입니다.

![공용 IP 주소 만들기](media/azure-stack-whats-new-dns/image01.png)

이 구성은 공용 IP 주소를 부하 분산 된 리소스와 연결 하려는 경우에 유용 합니다. 예를 들어 웹 응용 프로그램에서 요청을 처리 하는 부하 분산 장치를 할 수 있습니다. 뒤에 부하 분산 장치는 하나 이상의 가상 컴퓨터에 있는 웹 사이트입니다. 이제 IP 주소 대신 DNS 이름을 부하 분산 된 웹 사이트를 액세스할 수 있습니다.

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>DNS 영역 및 API를 사용 하 여 레코드 만들기 및 관리
수 만들고 DNS 영역 및 Azure 스택의 레코드를 관리 합니다.  

Azure 스택 Azure의 DNS Api와 일치 하는 Api를 사용 하 여 Azure의 같은 DNS 서비스를 제공 합니다.  Azure 스택 dns에서 도메인을 호스트 하 여 다른 Azure 서비스와 동일한 자격 증명, Api, 도구, 대금 청구 및 지원 DNS 레코드를 관리할 수 있습니다. 

가장 큰 이유에 대 한 Azure 스택 DNS 인프라는 Azure의 보다 더욱 간결 합니다. 따라서, 범위, 배율 및 성능 Azure 스택 배포 및 배포 된 환경의 규모에 따라 다릅니다.  따라서 성능, 가용성, 글로벌 메일 및 고가용성 (HA) 등 배포 마다 달라질 수 있습니다.

## <a name="comparison-with-azure-dns"></a>Azure DNS와 비교
Azure 스택에 있는 DNS는 두 가지 주요 차이점이 azure에서 DNS와 비슷합니다.
* **AAAA 레코드를 지원 하지 않습니다.**

    Azure 스택 IPv6 주소를 지원 하지 않으므로 azure 스택 AAAA 레코드를 지원 하지 않습니다.  이것이 Azure에서 DNS 및 Azure 스택 간의 주요 차이점입니다.
* **다중 테 넌 트가 아닙니다.**

    Azure에서 달리 Azure 스택의 DNS 서비스는 다중 테 넌 트 아닙니다. 따라서 각 테 넌 트 동일한 DNS 영역을 만들 수 없습니다. 첫 번째 구독 영역을 만들려고 시도 성공 하 고 이후 요청 실패.  알려진된 문제 및 Azure와 Azure 스택 DNS 간의 주요 차이점 않습니다. 이 문제는 향후 릴리스에서 해결 됩니다.

또한 Azure 스택 DNS 태그, 메타 데이터, Etag, 및 제한을 구현 하는 방법에서 약간의 차이가 있습니다.

다음 정보가 Azure 스택 DNS에만 적용 하 고 Azure DNS에서 약간 다릅니다. Azure DNS에 대 한 자세한 참조 [DNS 영역 및 기록](../../dns/dns-zones-records.md) Microsoft Azure 설명서 사이트입니다.

### <a name="tags-metadata-and-etags"></a>태그, 메타 데이터 및 Etags

**태그**

Azure 스택 DNS Azure 리소스 관리자 태그를 사용 하 여 DNS 영역 리소스를 지원 합니다. DNS 레코드 다음에 설명 된 대로 설정 하는 대신 'metadata'가 지원 되지만 태그 DNS 레코드 집합에서 지원 하지 않습니다.

**Metadata**

레코드 집합 태그에는 대신 Azure 스택 DNS 'metadata'를 사용 하 여 레코드 집합에 주석 지정을 지원 합니다. 태그와 유사한 메타데이터를 사용하면 각 레코드 집합에 이름-값 쌍을 연결할 수 있습니다. 예를 들어이 각 레코드 집합의 목적으로 기록해 두면 유용할 수 있습니다. 태그와 달리, 메타데이터는 Azure 청구서를 필터링하여 표시하는 데 사용할 수 없으며 Azure Resource Manager 정책에서 지정할 수 없습니다.

**Etag는**

두 사용자 또는 두 프로세스가 동시에 DNS 레코드 수정을 시도한다고 가정합니다. 어느 쪽이 성공할까요? 그리고 성공한 사용자 자신이 다른 사용자가 만든 변경 내용을 덮어썼다는 것을 알고 있을까요?

Azure 스택 DNS Etag를 사용 하 여 안전 하 게 같은 리소스에 대 한 동시 변경 처리. Etag는 Azure 리소스 관리자 '태그'에서 구분 됩니다. 각 DNS 리소스(영역 또는 레코드 집합)에는 Etag가 연결되어 있습니다. 리소스를 검색할 때마다 해당 Etag도 검색됩니다. 리소스를 업데이트할 때 다시 전달 하기 위한 Etag Azure 스택 DNS를 확인할 수 있도록 Etag 서버 일치 선택할 수 있습니다. 리소스를 업데이트할 때마다 Etag가 다시 생성되므로 Etag 불일치는 동시 변경이 발생했음을 나타냅니다. 새 리소스를 만들 때도 Etag를 사용하여 리소스가 존재하지 않는지 확인합니다.

기본적으로 Azure 스택 DNS PowerShell Etag를 사용 하 여 영역에 대 한 동시 변경 차단 하 고 레코드 집합입니다. 선택적 *-Overwrite* 스위치를 사용하여 Etag 검사를 무시할 수 있으며, 이 경우 발생한 동시 변경 내용을 덮어쓰게 됩니다.

Azure 스택 DNS REST API 수준에서 Etag는 HTTP 헤더를 사용 하 여 지정 됩니다. 해당 동작은 다음 표에 나와 있습니다.

| 헤더 | 동작|
|--------|---------|
| 없음   | PUT 항상 성공(Etag 검사 안 함)|
| If 일치| 리소스가 있고 Etag가 일치하는 경우에만 PUT 성공|
| If-match *| 리소스가 있는 경우에만 PUT 성공|
| If-none-match *| 리소스가 없는 경우에만 PUT 성공|

### <a name="limits"></a>제한

Azure 스택 DNS를 사용 하는 경우는 다음과 같은 기본 제한 값에 적용 됩니다.

| 리소스| 기본 제한|
|---------|--------------|
| 구독당 영역| 100|
| 영역당 레코드 집합| 5,000|
| 레코드 집합당 레코드| 20|

## <a name="next-steps"></a>다음 단계
[Azure 스택에 대 한 Idn 소개](azure-stack-understanding-dns.md)
