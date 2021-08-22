---
title: Azure Integration Runtime IP 주소
description: 데이터 저장소에 대한 네트워크 액세스를 보호하는 방화벽을 제대로 구성하기 위해 인바운드 트래픽을 허용해야 하는 IP 주소를 알아봅니다.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: e1e8a1429e853a652d3fe6a08ec7571547c56399
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642246"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime IP 주소

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Integration Runtime에서 사용하는 IP 주소는 Azure 통합 런타임이 있는 지역에 따라 다릅니다. 동일한 지역에 있는 *모든* Azure 통합 런타임은 동일한 IP 주소 범위를 사용합니다.

> [!IMPORTANT]  
> Managed Virtual Network를 사용하도록 설정하는 데이터 흐름 및 Azure Integration Runtime은 고정 IP 범위 사용을 지원하지 않습니다.
>
> 데이터 이동, 파이프라인 및 외부 활동 실행에 이러한 IP 범위를 사용할 수 있습니다. 이러한 IP 범위는 Azure 통합 런타임에서 인바운드 액세스를 위해 데이터 저장소/NSG(네트워크 보안 그룹)/방화벽에서 필터링하는 데 사용할 수 있습니다. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP 주소: 특정 지역

리소스가 있는 특정 Azure 지역에서 Azure 통합 런타임에 대해 나열된 IP 주소의 트래픽을 허용합니다. [서비스 태그 IP 범위 다운로드 링크](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)에서 서비스 태그의 IP 범위 목록을 가져올 수 있습니다. 예를 들어 Azure 지역이 **AustraliaEast** 인 경우 **DataFactory.AustraliaEast** 에서 IP 범위 목록을 가져올 수 있습니다.


## <a name="known-issue-with-azure-storage"></a>Azure Storage의 알려진 문제

* Azure Storage 계정에 연결할 때 IP 네트워크 규칙은 스토리지 계정과 동일한 지역의 Azure 통합 런타임에서 시작된 요청에 영향을 주지 않습니다. 자세한 내용은 [이 문서를 참조](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)하세요. 

  대신 [Azure Storage에 연결하는 동안 신뢰할 수 있는 서비스](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)를 사용하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory에서 데이터 이동을 위한 보안 고려 사항](data-movement-security-considerations.md)