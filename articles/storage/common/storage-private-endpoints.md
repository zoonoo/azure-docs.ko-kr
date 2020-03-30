---
title: 전용 끝점 사용
titleSuffix: Azure Storage
description: 가상 네트워크에서 저장소 계정에 대한 보안 액세스를 위한 개인 끝점 개요입니다.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299059"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Azure 저장소에 개인 끝점 사용

Azure Storage 계정에 [개인 끝점을](../../private-link/private-endpoint-overview.md) 사용하여 가상 네트워크(VNet)의 클라이언트가 [개인 링크를](../../private-link/private-link-overview.md)통해 데이터에 안전하게 액세스할 수 있도록 허용할 수 있습니다. 개인 끝점은 저장소 계정 서비스에 대한 VNet 주소 공간의 IP 주소를 사용합니다. VNet의 클라이언트와 저장소 계정 간의 네트워크 트래픽은 VNet과 Microsoft 백본 네트워크의 개인 링크를 통과하여 공용 인터넷에서 노출되지 않습니다.

저장소 계정에 개인 끝점을 사용하면 다음을 수행할 수 있습니다.

- 저장소 방화벽을 구성하여 저장소 서비스의 공용 끝점의 모든 연결을 차단하여 저장소 계정을 보호합니다.
- VNet에서 데이터의 유출을 차단할 수 있으므로 VNet(가상 네트워크)에 대한 보안을 강화합니다.
- 프라이빗 피어링을 사용하여 [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoutes를](../../expressroute/expressroute-locations.md) 사용하여 VNet에 연결하는 온-프레미스 네트워크의 저장소 계정에 안전하게 연결합니다.

## <a name="conceptual-overview"></a>개념적 개요

![Azure 저장소에 대한 개인 끝점 개요](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

개인 끝점은 [가상](../../virtual-network/virtual-networks-overview.md) 네트워크(VNet)의 Azure 서비스에 대한 특수 네트워크 인터페이스입니다. 저장소 계정에 대한 개인 엔드포인트를 만들면 VNet의 클라이언트와 저장소 간에 안전한 연결을 제공합니다. 개인 끝점은 VNet의 IP 주소 범위에서 IP 주소가 할당됩니다. 개인 끝점과 저장소 서비스 간의 연결은 보안 개인 링크를 사용합니다.

VNet의 응용 프로그램은 개인 끝점을 통해 저장소 서비스에 원활하게 연결할 수 **있으며, 그렇지 않으면 사용할 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용할 수 있습니다.** 개인 엔드포인트는 REST 및 SMB를 포함하여 저장소 계정에서 지원하는 모든 프로토콜과 함께 사용할 수 있습니다.

개인 끝점은 [서비스](../../virtual-network/virtual-network-service-endpoints-overview.md)끝점을 사용하는 서브넷에서 만들 수 있습니다. 따라서 서브넷의 클라이언트는 개인 끝점을 사용하여 하나의 저장소 계정에 연결할 수 있으며 서비스 끝점을 사용하여 다른 저장소에 액세스할 수 있습니다.

VNet에서 스토리지 서비스에 대한 프라이빗 엔드포인트를 만들면 스토리지 계정 소유자에게 승인을 위해 동의 요청이 전송됩니다. 개인 끝점 만들기를 요청하는 사용자가 저장소 계정의 소유자이기도 한 경우 이 동의 요청은 자동으로 승인됩니다.

저장소 계정 소유자는 [Azure 포털의](https://portal.azure.com)저장소 계정에 대한 *'개인 끝점'* 탭을 통해 동의 요청 및 개인 끝점을 관리할 수 있습니다.

> [!TIP]
> 개인 끝점을 통해서만 저장소 계정에 대한 액세스를 제한하려면 저장소 방화벽을 구성하여 공용 끝점을 통해 액세스를 거부하거나 제어하도록 합니다.

저장소 [방화벽을 구성하여](storage-network-security.md#change-the-default-network-access-rule) 기본적으로 공용 끝점을 통해 액세스를 거부하도록 구성하여 저장소 계정만 VNet에서 연결을 허용하도록 보호할 수 있습니다. 저장소 방화벽은 공용 끝점을 통해서만 액세스를 제어하므로 개인 끝점이 있는 VNet의 트래픽을 허용하는 방화벽 규칙이 필요하지 않습니다. 프라이빗 엔드포인트는 서브넷에 스토리지 서비스에 대한 액세스 권한을 부여하기 위한 동의 흐름에 의존합니다.

### <a name="private-endpoints-for-azure-storage"></a>Azure 저장소에 대한 개인 끝점

개인 끝점을 만들 때는 저장소 계정과 연결되는 저장소 서비스를 지정해야 합니다. 액세스해야 하는 저장소 계정의 각 저장소 서비스에 대해 별도의 개인 끝점, 즉 [Blobs,](../blobs/storage-blobs-overview.md) [데이터 레이크 저장소 Gen2,](../blobs/data-lake-storage-introduction.md) [파일,](../files/storage-files-introduction.md) [큐,](../queues/storage-queues-introduction.md) [테이블](../tables/table-storage-overview.md)또는 정적 [웹 사이트에](../blobs/storage-blob-static-website.md)액세스해야 합니다.

> [!TIP]
> RA-GRS 계정에서 더 나은 읽기 성능을 위해 저장소 서비스의 보조 인스턴스에 대해 별도의 개인 끝점을 만듭니다.

지역 중복 저장소에 대해 구성된 저장소 계정이 있는 보조 영역에 대한 읽기 액세스의 경우 서비스의 기본 인스턴스와 보조 인스턴스 모두에 대해 별도의 개인 끝점이 필요합니다. **장애 조치**에 대한 보조 인스턴스에 대한 개인 끝점을 만들 필요가 없습니다. 개인 끝점은 장애 조치 후 새 기본 인스턴스에 자동으로 연결됩니다. 저장소 중복 옵션에 대한 자세한 내용은 [Azure 저장소 중복을](storage-redundancy.md)참조하십시오.

저장소 계정에 대한 개인 끝점을 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [Azure 포털의 저장소 계정 환경에서 저장소 계정에 개인적으로 연결](../../private-link/create-private-endpoint-storage-portal.md)
- [Azure 포털의 개인 링크 센터를 사용하여 개인 끝점 만들기](../../private-link/create-private-endpoint-portal.md)
- [Azure CLI를 사용하여 Azure 프라이빗 엔드포인트 만들기](../../private-link/create-private-endpoint-cli.md)
- [Azure PowerShell을 사용하여 개인 끝점 만들기](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>프라이빗 엔드포인트에 연결

프라이빗 엔드포인트를 사용하는 VNet의 클라이언트는 공용 끝점에 연결하는 클라이언트와 동일한 연결 문자열을 저장소 계정에 사용해야 합니다. DNS 해상도를 사용하여 VNet에서 저장소 계정으로 개인 링크를 통해 자동으로 라우팅합니다.

> [!IMPORTANT]
> 동일한 연결 문자열을 사용하여 개인 끝점을 사용하여 저장소 계정에 연결합니다. '비공개*링크'* 하위 도메인 URL을 사용하여 저장소 계정에 연결하지 마십시오.

기본적으로 개인 끝점에 필요한 업데이트와 함께 VNet에 연결된 개인 [DNS 영역을](../../dns/private-dns-overview.md) 만듭니다. 그러나 자체 DNS 서버를 사용하는 경우 DNS 구성을 추가로 변경해야 할 수 있습니다. 아래 [DNS 변경 사항](#dns-changes-for-private-endpoints) 섹션에서는 개인 끝점에 필요한 업데이트를 설명합니다.

## <a name="dns-changes-for-private-endpoints"></a>개인 끝점에 대한 DNS 변경

개인 끝점을 만들 면 저장소 계정에 대 한 DNS CNAME 리소스 레코드 접두사 '*개인 링크*'와 하위 도메인의 별칭으로 업데이트 됩니다. 기본적으로 개인 끝점에 대한 DNS A 리소스 레코드와 함께 *'개인 링크'* 하위 도메인에 해당하는 개인 [DNS 영역도](../../dns/private-dns-overview.md)만듭니다.

VNet 외부에서 개인 끝점을 사용하여 저장소 끝점 URL을 확인하면 저장소 서비스의 공용 끝점으로 확인됩니다. 개인 엔드포인트를 호스팅하는 VNet에서 해결되면 저장소 끝점 URL이 개인 끝점의 IP 주소로 확인됩니다.

위의 예에서 설명된 경우 개인 끝점을 호스팅하는 VNet 외부에서 해결된 저장소 계정 'StorageAccountA'에 대한 DNS 리소스 레코드는 다음과 같이 표시됩니다.

| 이름                                                  | Type  | 값                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<스토리지 서비스 공용 엔드포인트\>                   |
| \<스토리지 서비스 공용 엔드포인트\>                   | A     | \<스토리지 서비스 공용 IP 주소\>                 |

앞에서 설명한 것처럼 저장소 방화벽을 사용하여 공용 끝점을 통해 VNet 외부의 클라이언트에 대한 액세스를 거부하거나 제어할 수 있습니다.

전용 엔드포인트를 호스팅하는 VNet의 클라이언트에서 해결한 StorageAccountA에 대한 DNS 리소스 레코드는 다음과 같은 것입니다.

| 이름                                                  | Type  | 값                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

이 방법을 사용하면 개인 끝점을 호스팅하는 VNet의 클라이언트와 VNet 외부의 클라이언트에 대해 **동일한 연결 문자열을 사용하여** 저장소 계정에 액세스할 수 있습니다.

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 저장소 계정 끝점에 대한 FQDN을 개인 엔드포인트 IP 주소로 확인할 수 있어야 합니다. 개인 링크 하위 도메인을 VNet의 개인 DNS 영역에 위임하도록 DNS 서버를 구성하거나 개인 엔드포인트 IP 주소로 *'StorageAccountA.privatelink.blob.core.windows.net'에*대한 A 레코드를 구성해야 합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용하는 경우 '개인 링크' 하위 도메인의 저장소 계정 이름을 개인 엔드포인트 IP 주소로 확인하도록 DNS 서버를 구성해야 합니다. '개인 링크' 하위 도메인을 VNet의 개인 DNS 영역에 위임하거나 DNS 서버에서 DNS 영역을 구성하고 DNS A 레코드를 추가하여 이 작업을 수행할 수 있습니다.

저장소 서비스에 대 한 개인 끝점에 대 한 권장된 DNS 영역 이름은 다음과 같습니다.

| 스토리지 서비스        | 영역 이름                            |
| :--------------------- | :----------------------------------- |
| Blob service           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| 파일 서비스           | `privatelink.file.core.windows.net`  |
| 큐 서비스          | `privatelink.queue.core.windows.net` |
| 테이블 서비스          | `privatelink.table.core.windows.net` |
| 정적 웹 사이트        | `privatelink.web.core.windows.net`   |

개인 끝점을 지원하도록 자체 DNS 서버를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [Azure 가상 네트워크에서 리소스에 대한 이름 확인](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [전용 끝점을 위한 DNS 구성](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>가격 책정

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

Azure 저장소에 대한 개인 끝점에 대한 다음과 같은 알려진 문제를 염두에 두어야 합니다.

### <a name="copy-blob-support"></a>복사 Blob 지원

저장소 계정이 방화벽에 의해 보호되고 개인 끝점을 통해 계정에 액세스하는 경우 해당 계정은 [복사 Blob](/rest/api/storageservices/copy-blob) 작업의 소스역할을 할 수 없습니다.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 VNet의 클라이언트에 대한 스토리지 액세스 제약 조건

기존 프라이빗 엔드포인트가 있는 VNet의 클라이언트는 개인 끝점이 있는 다른 저장소 계정에 액세스할 때 제약 조건에 직면합니다. 예를 들어 VNet N1에 Blob 저장소용 저장소 계정 A1에 대한 개인 끝점이 있다고 가정합니다. 저장소 계정 A2에 Blob 저장소에 대 한 VNet N2에 개인 끝점이 있는 경우 VNet N1의 클라이언트는 개인 끝점을 사용 하 여 계정 A2의 Blob 저장소에도 액세스 해야 합니다. 저장소 계정 A2에 Blob 저장소에 대한 개인 끝점이 없는 경우 VNet N1의 클라이언트는 개인 끝점 없이 해당 계정의 Blob 저장소에 액세스할 수 있습니다.

이 제약 조건은 계정 A2가 개인 끝점을 만들 때 만들어진 DNS 변경의 결과입니다.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 서브넷의 네트워크 보안 그룹 규칙

현재 개인 끝점에 [대해 NSG(네트워크 보안 그룹)](../../virtual-network/security-overview.md) 규칙 및 사용자 정의 경로를 구성할 수 없습니다. 전용 끝점을 호스팅하는 서브넷에 적용되는 NSG 규칙은 전용 끝점에 적용됩니다. 이 문제에 대한 제한된 해결 방법은 소스 서브넷의 개인 끝점에 대한 액세스 규칙을 구현하는 것입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md)
- [Blob 저장소에 대한 보안 권장 사항](../blobs/security-recommendations.md)
