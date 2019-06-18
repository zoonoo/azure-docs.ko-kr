---
title: Azure 사설 DNS FAQ
description: Azure 사설 DNS에 대 한 질문과 대답
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: c963cb1b6930b41a703b479e0213311d971e6606
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082847"
---
# <a name="azure-private-dns-faq"></a>Azure 사설 DNS FAQ

> [!IMPORTANT]
> Azure 사설 DNS는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS에서는 프라이빗 도메인을 지원하나요?

Azure 사설 DNS 영역 기능을 사용 하 여 supported 개인 도메인에 대 한 지원입니다. 사설 DNS 영역으로 인터넷 연결 Azure DNS 영역과 동일한 도구를 사용 하 여 관리 됩니다. 이러한 영역은 지정된 가상 네트워크 내에서만 확인할 수 있습니다. 자세한 내용은 [개요](private-dns-overview.md)를 참조하세요.

Azure의 다른 내부 DNS 옵션에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS Private Zones가 여러 Azure 지역에서 작동하나요?

예. 프라이빗 영역에서는 Azure 지역의 가상 네트워크 간 DNS 확인이 지원됩니다. 프라이빗 영역은 가상 네트워크를 명시적으로 피어링하지 않아도 작동합니다. 모든 가상 네트워크는 프라이빗 영역에 대한 확인 가상 네트워크로 지정해야 합니다. 고객은 TCP/HTTP 트래픽이 지역 간을 흐를 수 있도록 가상 네트워크를 피어링해야 할 수 있습니다.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>프라이빗 영역을 위해 가상 네트워크에서 인터넷에 연결되어야 하나요?

아니요. 프라이빗 영역은 가상 네트워크에서 작동합니다. 고객은 이러한 영역을 사용하여 가상 머신 또는 가상 네트워크 내부 및 여러 가상 네트워크 간의 다른 리소스에 대한 도메인을 관리합니다. 이름 확인을 위해 인터넷 연결이 필요하지는 않습니다.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>확인을 위해 여러 가상 네트워크에서 동일한 프라이빗 영역을 사용할 수 있나요?

예. 단일 개인 영역을 사용 하 여 최대 1000 가상 네트워크를 연결할 수 있습니다.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>다른 구독에 속하는 가상 네트워크에 추가할 수 있습니다 연결 된 가상 네트워크로 개인 영역?

예. 가상 네트워크와 프라이빗 DNS 영역에 대해 쓰기 작업 권한이 있어야 합니다. 쓰기 권한은 여러 RBAC 역할에 부여할 수 있습니다. 예를 들어, 클래식 네트워크 참가자 RBAC 역할에는 가상 네트워크에 대한 쓰기 권한이 있습니다. RBAC 역할에 대한 자세한 내용은 [역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조하세요.

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>개인 영역에 자동으로 등록 된 가상 컴퓨터 DNS 레코드도 자동으로 삭제할 가상 컴퓨터를 삭제 하면?

예. 사용 하도록 설정 하는 자동 등록을 사용 하 여 연결된 된 가상 네트워크 내의 가상 컴퓨터를 삭제 하는 경우 등록 된 레코드를 자동으로 삭제 됩니다.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>연결된 된 가상 네트워크에서 개인 영역에는 자동으로 등록 된 가상 머신 레코드를 수동으로 삭제 해야 합니까?

예. 이러한 자동으로 등록된 DNS 레코드를 영역에서 수동으로 만든 DNS 레코드로 덮어쓸 수 있습니다. 다음 질문과 대답이 이 항목에 대한 것입니다.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>연결된 된 가상 네트워크에 자동으로 등록 된 기존 가상 머신으로 동일한 호스트에 있는 개인 영역에 새 DNS 레코드를 수동으로 만들 하려고 하면 어떻게 되나요?

동일한 호스트 이름으로 자동으로 등록 된 기존 가상 머신으로 연결된 된 가상 네트워크에 있는 개인 영역에 새 DNS 레코드를 수동으로 만들 하려고 합니다. 이렇게 하면 새 DNS 레코드가 자동으로 등록된 가상 머신 레코드를 덮어씁니다. 이 영역에서 다시 수동으로 만든 이 DNS 레코드를 삭제하려고 하면 삭제가 성공합니다. 가상 머신이 여전히 존재하고 프라이빗 IP가 연결되어 있기만 하면 자동 등록이 다시 수행됩니다. DNS 레코드는 영역에서 자동으로 다시 생성됩니다.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>개인 영역에서 연결된 된 가상 네트워크 연결을 해제 하면 어떻게 되나요? 가상 네트워크의 자동으로 등록된 가상 머신 레코드가 해당 영역에서도 제거되나요?

예. 개인 영역에서 연결된 된 가상 네트워크 연결을 끊을 연결 된 가상 네트워크 연결을 제거 하려면 DNS 영역을 업데이트 합니다. 이 프로세스에서 자동으로 등록된 가상 머신 레코드는 영역에서 제거됩니다.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>개인 영역에 연결 된 연결 된 가상 네트워크를 삭제 하면 어떻게 되나요? 영역에서 연결 된 가상 네트워크로 가상 네트워크 연결을 해제 하는 개인 영역을 수동으로 업데이트 해야 합니까?

예. 연결 된 개인 영역에서 먼저 해제 하지 않고 연결된 된 가상 네트워크를 삭제 하면 삭제 작업이 성공 합니다. 그렇지만 가상 네트워크가 프라이빗 영역에서 자동으로 연결 해제되지는 않습니다. 수동으로 프라이빗 영역에서 가상 네트워크 연결을 해제해야 합니다. 이러한 이유로, 연결을 삭제하기 전에 먼저 프라이빗 영역에서 가상 네트워크의 연결을 해제합니다.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>DNS 확인의 기본 FQDN (internal.cloudapp.net)를 사용 하 여 계속 작동 개인 영역 (예를 들어 private.contoso.com) 가상 네트워크에 연결 된 경우에?

예. 프라이빗 영역은 Azure에서 제공한 internal.cloudapp.net 영역을 사용하여 기본 DNS 확인을 대체하지 않습니다. 이 영역은 추가 기능 또는 향상된 기능으로 제공됩니다. Azure에서 제공한 internal.cloudapp.net을 신뢰하는지 또는 사용자 고유의 프라이빗 영역을 신뢰하는지에 관계없이 확인하려는 영역의 FQDN을 사용하도록 합니다.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>연결된 가상 네트워크 내의 가상 머신에 사용되는 DNS 접미사가 프라이빗 영역의 DNS 접두사로 변경되나요?

아니요. , 연결된 가상 네트워크의 가상 머신에 사용된 DNS 접미사는 기본 Azure 제공 접미사("*. internal.cloudapp.net")로 유지됩니다. 가상 머신의 이 DNS 접미사를 프라이빗 영역의 DNS 접미사로 수동으로 변경할 수 있습니다.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Azure 사설 DNS에 대 한 사용량 제한은 무엇 인가요?

다음과 같은 기본 제한이 Azure 사설 DNS를 사용할 때 고려해 야 합니다.

| Resource | 기본 제한 |
| --- | --- |
|구독 당 사설 DNS 영역|1000|
|사설 DNS 영역당 레코드 집합|25,000|
|레코드 집합당 레코드|20|
|사설 DNS 영역 당 가상 네트워크 연결|1000|
|자동 등록을 사용 하 여 사설 DNS 영역은 당 가상 네트워크 연결 사용|100|
|자동 등록 활성화를 사용 하 여 가상 네트워크 연결 가져오기 개인 DNS 영역의 수|1|
|사설 DNS 영역은 가상 네트워크를 연결할 가져오기 있습니다 수|1000|

## <a name="is-there-portal-support-for-private-zones"></a>프라이빗 영역에 대한 포털 지원이 있나요?

Api, PowerShell, CLI 및 Sdk를 통해 이미 만든 개인 영역과 예, Azure portal에 표시 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 사설 DNS에 자세히 알아보기](private-dns-overview.md)