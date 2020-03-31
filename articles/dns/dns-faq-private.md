---
title: Azure 프라이빗 DNS FAQ
description: 이 문서에서는 Azure 개인 DNS에 대한 자주 묻는 질문을 배웁니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939415"
---
# <a name="azure-private-dns-faq"></a>Azure 프라이빗 DNS FAQ

다음은 Azure 개인 DNS에 대한 자주 묻는 질문입니다.

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS에서는 프라이빗 도메인을 지원하나요?

개인 도메인은 Azure 개인 DNS 영역 기능을 사용하여 지원됩니다. 개인 DNS 영역은 지정된 가상 네트워크 내에서만 확인할 수 있습니다. 자세한 내용은 [개요](private-dns-overview.md)를 참조하세요.

Azure의 다른 내부 DNS 옵션에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인을](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)참조하십시오.

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Azure 개인 DNS 영역이 Azure 리전에서 작동합니까?

예. 프라이빗 영역에서는 Azure 지역의 가상 네트워크 간 DNS 확인이 지원됩니다. 프라이빗 영역은 가상 네트워크를 명시적으로 피어링하지 않아도 작동합니다. 모든 가상 네트워크는 개인 DNS 영역에 연결되어야 합니다.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>프라이빗 영역을 위해 가상 네트워크에서 인터넷에 연결되어야 하나요?

아니요. 프라이빗 영역은 가상 네트워크에서 작동합니다. 이를 사용하여 가상 네트워크 내부 및 전체에서 가상 컴퓨터 또는 기타 리소스에 대한 도메인을 관리할 수 있습니다. 이름 확인을 위해 인터넷 연결이 필요하지는 않습니다.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>확인을 위해 여러 가상 네트워크에서 동일한 프라이빗 영역을 사용할 수 있나요?

예. 개인 DNS 영역을 수천 개의 가상 네트워크와 연결할 수 있습니다. 자세한 내용은 [Azure DNS 제한을](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) 참조하십시오.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>다른 구독에 속한 가상 네트워크를 개인 영역에 연결할 수 있습니까?

예. 가상 네트워크와 프라이빗 DNS 영역에 대해 쓰기 작업 권한이 있어야 합니다. 쓰기 권한은 여러 RBAC 역할에 부여할 수 있습니다. 예를 들어 클래식 네트워크 기여자 RBAC 역할에는 가상 네트워크에 대한 쓰기 권한이 있고 개인 DNS 영역 기여자 역할에는 개인 DNS 영역에 대한 쓰기 권한이 있습니다. RBAC 역할에 대한 자세한 내용은 [역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조하세요.

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>가상 컴퓨터를 삭제하면 개인 영역에 자동으로 등록된 가상 시스템 DNS 레코드가 자동으로 삭제되나요?

예. 자동 등록을 사용하도록 설정한 연결된 가상 네트워크 내에서 가상 컴퓨터를 삭제하면 등록된 레코드가 자동으로 삭제됩니다.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>연결된 가상 네트워크에서 개인 영역에 자동으로 등록된 가상 시스템 레코드를 수동으로 삭제할 수 있습니까?

예. 이러한 자동으로 등록된 DNS 레코드를 영역에서 수동으로 만든 DNS 레코드로 덮어쓸 수 있습니다. 다음 질문과 대답이 이 항목에 대한 것입니다.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>연결된 가상 네트워크에서 자동으로 등록된 기존 가상 컴퓨터와 동일한 호스트 이름을 가진 개인 영역에 새 DNS 레코드를 수동으로 만들려고 하면 어떻게 됩니까?

연결된 가상 네트워크에서 자동으로 등록된 기존 가상 컴퓨터와 동일한 호스트 이름을 가진 개인 영역에 새 DNS 레코드를 수동으로 만들려고 합니다. 이렇게 하면 새 DNS 레코드가 자동으로 등록된 가상 머신 레코드를 덮어씁니다. 이 영역에서 다시 수동으로 만든 이 DNS 레코드를 삭제하려고 하면 삭제가 성공합니다. 가상 머신이 여전히 존재하고 프라이빗 IP가 연결되어 있기만 하면 자동 등록이 다시 수행됩니다. DNS 레코드는 영역에서 자동으로 다시 생성됩니다.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>개인 영역에서 연결된 가상 네트워크의 연결을 해제하면 어떻게 되나요? 가상 네트워크의 자동으로 등록된 가상 머신 레코드가 해당 영역에서도 제거되나요?

예. 개인 영역에서 연결된 가상 네트워크의 연결을 해제하려면 DNS 영역을 업데이트하여 연결된 가상 네트워크 링크를 제거합니다. 이 프로세스에서 자동으로 등록된 가상 머신 레코드는 영역에서 제거됩니다.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>개인 영역에 연결된 연결된 가상 네트워크를 삭제하면 어떻게 되나요? 영역에서 연결된 가상 네트워크로 가상 네트워크의 연결을 해제하려면 개인 영역을 수동으로 업데이트해야 합니까?

아니요. 개인 영역에서 먼저 연결을 해제하지 않고 연결된 가상 네트워크를 삭제하면 삭제 작업이 성공하고 DNS 영역에 대한 링크가 자동으로 지워집니다.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>기본 FQDN(internal.cloudapp.net)을 사용하여 DNS 해상도가 가상 네트워크에 연결된 경우에도 개인 영역(예: private.contoso.com)이 계속 작동합니까?

예. 개인 영역은 Azure에서 제공하는 기본 internal.cloudapp.net 영역을 대체하지 않습니다. Azure에서 제공한 internal.cloudapp.net을 신뢰하는지 또는 사용자 고유의 프라이빗 영역을 신뢰하는지에 관계없이 확인하려는 영역의 FQDN을 사용하도록 합니다.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>연결된 가상 네트워크 내의 가상 머신에 사용되는 DNS 접미사가 프라이빗 영역의 DNS 접두사로 변경되나요?

아니요. , 연결된 가상 네트워크의 가상 머신에 사용된 DNS 접미사는 기본 Azure 제공 접미사("*. internal.cloudapp.net")로 유지됩니다. 가상 머신의 이 DNS 접미사를 프라이빗 영역의 DNS 접미사로 수동으로 변경할 수 있습니다.
이 접미사를 변경하는 방법에 대한 지침은 동적 DNS 사용을 사용하여 [자신의 DNS 서버에 호스트 이름을 등록하는](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients) 것을 참조하십시오.

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Azure DNS 개인 영역의 사용 제한은 무엇입니까?

Azure [DNS](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) 개인 영역에 대한 사용 제한에 대한 자세한 내용은 Azure DNS 제한을 참조하십시오.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>기존 개인 DNS 영역이 새 포털 환경에서 표시되지 않는 이유는 무엇입니까?

미리 보기 API를 사용하여 기존 개인 DNS 영역을 만든 경우 이러한 영역을 새 리소스 모델로 마이그레이션해야 합니다. 미리 보기 API를 사용하여 만든 개인 DNS 영역은 새 포털 환경에서 표시되지 않습니다. 새 리소스 모델로 마이그레이션하는 방법에 대한 지침은 아래를 참조하십시오.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>기존 개인 DNS 영역을 새 모델로 마이그레이션하려면 어떻게 해야 합니까?

가능한 한 빨리 새 리소스 모델로 마이그레이션하는 것이 좋습니다. 레거시 리소스 모델이 지원되지만 이 모델 위에 추가 기능이 개발되지는 않습니다. 앞으로는 새로운 리소스 모델에 찬성하여 더 이상 사용하지 않으려 고 합니다. 기존 개인 DNS 영역을 새 리소스 모델로 마이그레이션하는 방법에 대한 지침은[Azure DNS 개인 영역에 대한 마이그레이션 가이드를](private-dns-migration-guide.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure 개인 DNS에 대해 자세히 알아보기](private-dns-overview.md)
