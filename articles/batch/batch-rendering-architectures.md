---
title: Azure 렌더링 - 참조 아키텍처
description: Azure Batch 및 기타 Azure 서비스를 통해 클라우드로 버스트하여 온-프레미스 렌더링 팜을 확장하는 아키텍처입니다.
services: batch
author: davefellows
manager: jeconnoc
ms.author: danlep
ms.date: 08/13/2018
ms.topic: conceptual
ms.openlocfilehash: 0fe101ee6eb88094034b90c4d39f06ba509c9512
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099748"
---
# <a name="reference-architectures-for-azure-rendering"></a>Azure 렌더링 참조 아키텍처

이 문서에서는 온-프레미스 렌더링 팜을 Azure로 확장하거나 "버스트"하는 시나리오에 대한 고급 아키텍처 다이어그램을 보여 줍니다. 예제에서는 Azure 계산, 네트워킹 및 저장소 서비스에 대한 다양한 옵션을 보여 줍니다.

## <a name="hybrid-with-nfs-or-cfs"></a>NFS 또는 CFS가 있는 하이브리드

아래 다이어그램에서는 다음 Azure 서비스가 포함된 하이브리드 시나리오를 보여 줍니다.

* **계산** - Azure Batch 풀 또는 Virtual Machine Scale Set입니다.

* **네트워크** - 온-프레미스: Azure ExpressRoute 또는 VPN입니다. Azure: Azure VNet입니다.

* **저장소** - 입력 및 출력 파일: Azure VM을 사용하는 NFS 또는 CFS이며, Azure 파일 동기화 또는 RSync를 통해 온-프레미스 저장소와 동기화됩니다.

  ![클라우드 버스팅 - NFS와 CFS가 있는 하이브리드](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Blobfuse가 있는 하이브리드

아래 다이어그램에서는 다음 Azure 서비스가 포함된 하이브리드 시나리오를 보여 줍니다.

* **계산** - Azure Batch 풀 또는 Virtual Machine Scale Set입니다.

* **네트워크** - 온-프레미스: Azure ExpressRoute 또는 VPN입니다. Azure: Azure VNet입니다.

* **저장소** - 입력 및 출력 파일: Blob 저장소이며, Azure Blobfuse를 통해 계산 리소스에 탑재됩니다.

  ![클라우드 버스팅 - Blobfuse가 있는 하이브리드](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>하이브리드 계산 및 저장소

아래 다이어그램에서는 계산 및 저장소에 대한 완전히 연결된 하이브리드 시나리오를 보여 주며, 포함된 Azure 서비스는 다음과 같습니다.

* **계산** - Azure Batch 풀 또는 Virtual Machine Scale Set입니다.

* **네트워크** - 온-프레미스: Azure ExpressRoute 또는 VPN입니다. Azure: Azure VNet입니다.

* **저장소** - 프레미스 간: Avere vFXT입니다. Azure Data Box를 통해 온-프레미스 파일을 선택적으로 Blob 저장소에 보관합니다.

  ![클라우드 버스트 - 하이브리드 계산 및 저장소](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>다음 단계

* Azure Batch를 통해 [렌더링 관리자](batch-rendering-render-managers.md)를 사용하는 방법에 대해 자세히 알아봅니다.

* [Azure의 렌더링](batch-rendering-service.md) 옵션에 대해 자세히 알아봅니다.