---
title: Azure Container Instances 할당량 및 지역 가용성
description: Azure Container Instances 서비스의 할당량, 제한 및 지역 가용성입니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 02/15/2019
ms.author: danlep
ms.openlocfilehash: c676989b4b882f2b1887a1b6a5091b60027f61d0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328411"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Azure Container Instances에 대한 할당량 및 지역 가용성

모든 Azure 서비스에는 리소스와 기능에 대한 특정 기본 제한과 할당량이 있습니다. 다음 섹션에서는 몇 가지 Azure Container Instances 리소스의 기본 리소스 제한과, Azure 지역의 서비스 가용성에 대해 자세히 설명합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="feature-availability"></a>기능 가용성

Azure Container Instances는 동일한 API로 Windows 및 Linux 컨테이너를 모두 예약할 수 있습니다. 그러나 다음 기능은 현재 Linux 컨테이너 그룹에만 사용할 수 있습니다. Windows 지원이 예정되어 있습니다.

* 컨테이너 그룹당 다중 컨테이너 수
* 볼륨 탑재(Azure Files, emptyDir, GitRepo, 비밀)
* Virtual Network(미리 보기)
* GPU 리소스(미리 보기)

## <a name="region-availability"></a>지역 가용성

Azure Container Instances를 각 컨테이너 그룹에 대해 지정된 CPU 및 메모리 제한을 사용하여 다음 지역에서 사용할 수 있습니다. 값은 게시 시점에 제공됩니다. 최신 정보의 경우 [목록 기능](/rest/api/container-instances/listcapabilities/listcapabilities) API를 사용합니다. 

[가상 네트워크](container-instances-vnet.md)(미리 보기) 또는 [GPU 리소스](container-instances-gpu.md)(미리 보기)에 Azure Container Instances를 사용할 때 가용성 및 리소스 제한이 달라질 수 있습니다.

| 위치 | OS | CPU | 메모리(GB) |
| -------- | -- | :---: | :-----------: |
| 캐나다 중부, 미국 중부, 미국 동부 2, 미국 중남부 | Linux | 4 | 16 |
| 미국 동부, 북유럽, 유럽 서부, 미국 서부, 미국 서부 2 | Linux | 4 | 14 |
| 일본 동부 | Linux | 2 | 8 |
| 오스트레일리아 동부, 동남 아시아 | Linux | 2 | 7 |
| 인도 중부, 동아시아, 미국 중북부, 인도 남부 | Linux | 2 | 3.5 |
| 미국 동부, 유럽 서부, 미국 서부 |  Windows | 4 | 14 |
| 오스트레일리아 동부, 캐나다 중부, 인도 중부, 미국 중부, 동아시아, 미국 동부 2, 일본 동부, 미국 중북부, 유럽 북부, 미국 중남부, 인도 남부, 동남 아시아, 미국 서부 2 |  Windows | 2 | 3.5 |

이러한 리소스 제한 내에서 만든 컨테이너 인스턴스는 배포 지역 내의 사용 가능 여부의 적용을 받습니다. 영역이 과부하 상태에 있는 경우 인스턴스를 배포할 때 오류가 발생할 수 있습니다. 이러한 배포 오류를 완화하려면 낮은 CPU 및 메모리 설정을 사용하여 인스턴스를 배포하거나 나중에 배포를 시도합니다.

필요한 추가 지역 또는 늘려야 하는 CPU/메모리 제한이 있으면 [aka.ms/aci/feedback](https://aka.ms/aci/feedback)에서 지원 팀에 알려주세요.

컨테이너 인스턴스 배포 문제 해결에 대한 자세한 내용은 [Azure Container Instances로의 배포 문제 해결](container-instances-troubleshooting.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 이러한 증대를 지원하는 하나 이상의 리소스에 대해 증대를 요청하려면 [Azure 지원 요청][azure-support] (**발급 요청**에 대해 "할당량" 선택)을 제출합니다.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
