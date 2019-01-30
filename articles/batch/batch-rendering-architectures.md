---
title: Azure 렌더링 참조 아키텍처 - Azure Batch
description: Azure Batch 및 기타 Azure 서비스를 통해 클라우드로 버스트하여 온-프레미스 렌더링 팜을 확장하는 아키텍처입니다.
services: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 08/13/2018
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: d5102ba94e2b7808a457df00a87b35ef7022c454
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543498"
---
# <a name="reference-architectures-for-azure-rendering"></a>Azure 렌더링 참조 아키텍처

이 문서에서는 온-프레미스 렌더링 팜을 Azure로 확장하거나 "버스트"하는 시나리오에 대한 고급 아키텍처 다이어그램을 보여 줍니다. 예제에서는 Azure 계산, 네트워킹 및 저장소 서비스에 대한 다양한 옵션을 보여 줍니다.

## <a name="hybrid-with-nfs-or-cfs"></a>NFS 또는 CFS가 있는 하이브리드

아래 다이어그램에서는 다음 Azure 서비스가 포함된 하이브리드 시나리오를 보여 줍니다.

* **계산** - Azure Batch 풀 또는 Virtual Machine Scale Set입니다.

* **네트워크** - 온-프레미스: Azure ExpressRoute 또는 VPN입니다. Azure: Azure VNet.

* **스토리지** - 입력 및 출력 파일: Azure VM을 사용하는 NFS 또는 CFS이며, Azure 파일 동기화 또는 RSync를 통해 온-프레미스 스토리지와 동기화됩니다.

  ![클라우드 버스팅 - NFS와 CFS가 있는 하이브리드](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Blobfuse가 있는 하이브리드

아래 다이어그램에서는 다음 Azure 서비스가 포함된 하이브리드 시나리오를 보여 줍니다.

* **계산** - Azure Batch 풀 또는 Virtual Machine Scale Set입니다.

* **네트워크** - 온-프레미스: Azure ExpressRoute 또는 VPN입니다. Azure: Azure VNet.

* **스토리지** - 입력 및 출력 파일: Azure Blobfuse를 통해 컴퓨팅 리소스에 탑재된 Blob Storage입니다.

  ![클라우드 버스팅 - Blobfuse가 있는 하이브리드](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>하이브리드 계산 및 저장소

아래 다이어그램에서는 계산 및 저장소에 대한 완전히 연결된 하이브리드 시나리오를 보여 주며, 포함된 Azure 서비스는 다음과 같습니다.

* **계산** - Azure Batch 풀 또는 Virtual Machine Scale Set입니다.

* **네트워크** - 온-프레미스: Azure ExpressRoute 또는 VPN입니다. Azure: Azure VNet.

* **스토리지** - 크로스-프레미스: Avere vFXT. Azure Data Box를 통해 온-프레미스 파일을 선택적으로 Blob 저장소에 보관합니다.

  ![클라우드 버스트 - 하이브리드 계산 및 저장소](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>다음 단계

* Azure Batch를 통해 [렌더링 관리자](batch-rendering-render-managers.md)를 사용하는 방법에 대해 자세히 알아봅니다.

* [Azure의 렌더링](batch-rendering-service.md) 옵션에 대해 자세히 알아봅니다.