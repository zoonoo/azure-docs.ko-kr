---
title: Azure 프라이빗 DNS FAQ
description: In this article, learn frequently asked questions about Azure Private DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: de63799400a10afc1930cd373df0c8dd86320f78
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212003"
---
# <a name="azure-private-dns-faq"></a>Azure 프라이빗 DNS FAQ

The following are frequently asked questions about Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS에서는 프라이빗 도메인을 지원하나요?

Private domains are supported using the Azure Private DNS zones feature. Private DNS zones are resolvable only from within specified virtual networks. 자세한 내용은 [개요](private-dns-overview.md)를 참조하세요.

Azure의 다른 내부 DNS 옵션에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Will Azure Private DNS zones work across Azure regions?

예. 프라이빗 영역에서는 Azure 지역의 가상 네트워크 간 DNS 확인이 지원됩니다. 프라이빗 영역은 가상 네트워크를 명시적으로 피어링하지 않아도 작동합니다. All the virtual networks must be linked to the private DNS zone.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>프라이빗 영역을 위해 가상 네트워크에서 인터넷에 연결되어야 하나요?

아닙니다. 프라이빗 영역은 가상 네트워크에서 작동합니다. You use them to manage domains for virtual machines or other resources within and across virtual networks. 이름 확인을 위해 인터넷 연결이 필요하지는 않습니다.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>확인을 위해 여러 가상 네트워크에서 동일한 프라이빗 영역을 사용할 수 있나요?

예. You can link a private DNS zone with thousands of virtual networks. For more information, see [Azure DNS Limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Can a virtual network that belongs to a different subscription be linked to a private zone?

예. 가상 네트워크와 프라이빗 DNS 영역에 대해 쓰기 작업 권한이 있어야 합니다. 쓰기 권한은 여러 RBAC 역할에 부여할 수 있습니다. For example, the Classic Network Contributor RBAC role has write permissions to virtual networks and Private DNS zones Contributor role has write permissions on the private DNS zones. RBAC 역할에 대한 자세한 내용은 [역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조하세요.

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Will the automatically registered virtual machine DNS records in a private zone be automatically deleted when you delete the virtual machine?

예. If you delete a virtual machine within a linked virtual network with autoregistration enabled, the registered records are automatically deleted.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Can an automatically registered virtual machine record in a private zone from a linked virtual network be deleted manually?

예. 이러한 자동으로 등록된 DNS 레코드를 영역에서 수동으로 만든 DNS 레코드로 덮어쓸 수 있습니다. 다음 질문과 대답이 이 항목에 대한 것입니다.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>What happens when I try to manually create a new DNS record into a private zone that has the same hostname as an automatically registered existing virtual machine in a linked virtual network?

You try to manually create a new DNS record into a private zone that has the same hostname as an existing, automatically registered virtual machine in a linked virtual network. 이렇게 하면 새 DNS 레코드가 자동으로 등록된 가상 머신 레코드를 덮어씁니다. 이 영역에서 다시 수동으로 만든 이 DNS 레코드를 삭제하려고 하면 삭제가 성공합니다. 가상 머신이 여전히 존재하고 프라이빗 IP가 연결되어 있기만 하면 자동 등록이 다시 수행됩니다. DNS 레코드는 영역에서 자동으로 다시 생성됩니다.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>What happens when we unlink a linked virtual network from a private zone? 가상 네트워크의 자동으로 등록된 가상 머신 레코드가 해당 영역에서도 제거되나요?

예. To unlink a linked virtual network from a private zone, you update the DNS zone to remove the associated virtual network link. 이 프로세스에서 자동으로 등록된 가상 머신 레코드는 영역에서 제거됩니다.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>What happens when we delete a linked virtual network that's linked to a private zone? Do we have to manually update the private zone to unlink the virtual network as a linked virtual network from the zone?

아닙니다. When you delete a linked virtual network without unlinking it from a private zone first, your deletion operation succeeds and the links to the DNS zone are automatically cleared.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Will DNS resolution by using the default FQDN (internal.cloudapp.net) still work even when a private zone (for example, private.contoso.com) is linked to a virtual network?

예. Private Zones don't replace the default Azure-provided internal.cloudapp.net zone. Azure에서 제공한 internal.cloudapp.net을 신뢰하는지 또는 사용자 고유의 프라이빗 영역을 신뢰하는지에 관계없이 확인하려는 영역의 FQDN을 사용하도록 합니다.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>연결된 가상 네트워크 내의 가상 머신에 사용되는 DNS 접미사가 프라이빗 영역의 DNS 접두사로 변경되나요?

아닙니다. , 연결된 가상 네트워크의 가상 머신에 사용된 DNS 접미사는 기본 Azure 제공 접미사("*. internal.cloudapp.net")로 유지됩니다. 가상 머신의 이 DNS 접미사를 프라이빗 영역의 DNS 접미사로 수동으로 변경할 수 있습니다.
For guidance on how to change this suffix refer to [Use dynamic DNS to register hostnames in your own DNS server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>What are the usage limits for Azure DNS Private zones?

Refer to [Azure DNS limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits) for details on the usage limits for Azure DNS private zones.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Why don’t my existing private DNS zones show up in new portal experience?

If your existing private DNS zone were created using preview API, you must migrate these zones to new resource model. Private DNS zones created using preview API will not show up in new portal experience. See below for instructions on how to migrate to new resource model.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>How do I migrate my existing private DNS zones to the new model?

We strongly recommend that you migrate to the new resource model as soon as possible. Legacy resource model will be supported, however, further features will not be developed on top of this model. In future, we intend to deprecate it in favor of new resource model. For guidance on how to migrate your existing private DNS zones to new resource model see[migration guide for Azure DNS private zones](private-dns-migration-guide.md).

## <a name="next-steps"></a>다음 단계

- [Learn more about Azure Private DNS](private-dns-overview.md)
