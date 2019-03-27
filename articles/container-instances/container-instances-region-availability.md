---
title: Azure Container Instances 리소스 가용성
description: 여러 다른 Azure 지역의 Azure Container Instances 서비스에 대한 컴퓨팅 및 메모리 리소스 가용성입니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554869"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 지역의 Azure Container Instances에 대한 리소스 가용성

이 문서에서는 Azure 지역의 Azure Container Instances 컴퓨팅 및 메모리 리소스에 대한 가용성을 자세히 설명합니다. 

제공되는 값은 [컨테이너 그룹](container-instances-container-groups.md) 배포당 사용 가능한 최대 리소스입니다. 값은 게시 시점에 제공됩니다. 최신 정보의 경우 [목록 기능](/rest/api/container-instances/listcapabilities/listcapabilities) API를 사용합니다. 

> [!NOTE]
> 이러한 리소스 제한 내에서 만든 컨테이너 그룹은 배포 지역 내의 사용 가능 여부의 적용을 받습니다. 영역이 과부하 상태에 있는 경우 인스턴스를 배포할 때 오류가 발생할 수 있습니다. 이러한 배포 오류를 완화하려면 낮은 리소스 설정을 사용하여 인스턴스를 배포하거나 나중에 배포를 시도합니다.

배포의 할당량 및 기타 제한에 대한 내용은 [Azure Container Instances의 할당량 및 제한](container-instances-quotas.md)을 참조하세요.

## <a name="availability---general"></a>가용성 - 일반

| 위치 | OS | CPU | 메모리(GB) |
| -------- | -- | :---: | :-----------: |
| 캐나다 중부, 미국 중부, 미국 동부 2, 미국 중남부 | Linux | 4 | 16 |
| 미국 동부, 북유럽, 유럽 서부, 미국 서부, 미국 서부 2 | Linux | 4 | 14 |
| 일본 동부 | Linux | 2 | 8 |
| 오스트레일리아 동부, 동남 아시아 | Linux | 2 | 7 |
| 인도 중부, 동아시아, 미국 중북부, 인도 남부 | Linux | 2 | 3.5 |
| 미국 동부, 유럽 서부, 미국 서부 |  Windows | 4 | 14 |
| 오스트레일리아 동부, 캐나다 중부, 인도 중부, 미국 중부, 동아시아, 미국 동부 2, 일본 동부, 미국 중북부, 유럽 북부, 미국 중남부, 인도 남부, 동남 아시아, 미국 서부 2 |  Windows | 2 | 3.5 |

## <a name="availability---virtual-network-deployment-preview"></a>가용성 - 가상 네트워크 배포(미리 보기)

다음 지역 및 리소스는 [Azure VIrtual Network](container-instances-vnet.md)(미리 보기)에 배포된 컨테이너 그룹에서 사용할 수 있습니다.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>가용성 - GPU 리소스(미리 보기)

다음 지역 및 리소스는 [GPU 리소스](container-instances-gpu.md)(미리 보기)와 함께 배포된 컨테이너 그룹에서 사용할 수 있습니다.

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>다음 단계

추가 지역 또는 증가된 리소스 가용성을 확인하고 싶은 경우 [aka.ms/aci/feedback](https://aka.ms/aci/feedback)을 통해 팀에 알려주세요.

컨테이너 인스턴스 배포 문제 해결 방법에 대한 내용은 [Azure Container Instances로의 배포 문제 해결](container-instances-troubleshooting.md)을 참조하세요.
