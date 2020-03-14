---
title: 지역별 리소스 가용성
description: 여러 다른 Azure 지역의 Azure Container Instances 서비스에 대한 컴퓨팅 및 메모리 리소스 가용성입니다.
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: f429a165fe26cc9fc7aa973231f5a77163feef4a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247138"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 지역의 Azure Container Instances에 대한 리소스 가용성

이 문서에서는 Azure 지역 및 대상 운영 체제에서 Azure Container Instances 계산, 메모리 및 저장소 리소스의 가용성을 자세히 설명 합니다. 

제공되는 값은 [컨테이너 그룹](container-instances-container-groups.md) 배포당 사용 가능한 최대 리소스입니다. 값은 게시 시점에 제공됩니다. 

> [!NOTE]
> 이러한 리소스 제한 내에서 만든 컨테이너 그룹은 배포 지역 내의 사용 가능 여부의 적용을 받습니다. 영역이 과부하 상태에 있는 경우 인스턴스를 배포할 때 오류가 발생할 수 있습니다. 이러한 배포 오류를 완화하려면 낮은 리소스 설정을 사용하여 인스턴스를 배포하거나 나중에 배포를 시도합니다.

배포의 할당량 및 기타 제한에 대한 내용은 [Azure Container Instances의 할당량 및 제한](container-instances-quotas.md)을 참조하세요.

## <a name="availability---general"></a>가용성 - 일반

Linux와 [지원 되](container-instances-faq.md#what-windows-base-os-images-are-supported) 는 Windows Server 2016 기반 컨테이너를 포함 하는 컨테이너 그룹에는 다음 지역과 최대 리소스를 사용할 수 있습니다.

| 영역 | OS | 최대 CPU | 최대 메모리 (GB) | 스토리지(GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 브라질 남부, 캐나다 중부, 인도 중부, 미국 중부, 동아시아, 미국 동부, 미국 동부 2, 서유럽, 미국 중부, 동남 아시아, 인도 남부, 영국 남부, 유럽 서부, 미국 서 부, 미국 서 부 2 | Linux | 4 | 16 | 50 |
| 오스트레일리아 동부, 일본 동부 | Linux | 2 | 8 | 50 |
| 미국 중북부 | Linux | 2 | 3.5 | 50 |
| 브라질 남부, 일본 동부, 유럽 서부 | Windows | 4 | 16 | 20 |
| 미국 동부, 미국 서부 | Windows | 4 | 14 | 20 |
| 오스트레일리아 동부, 캐나다 중부, 인도 중부, 미국 중부, 동아시아, 미국 동부 2, 미국 중 북부, 유럽 북부, 남부 중부, 동남 아시아, 인도 남부, 영국 남부, 미국 서 부 2 | Windows | 2 | 3.5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>가용성 - Windows Server 2019 LTSC, 1809 배포(미리 보기)

Windows Server 2019 기반 컨테이너 (미리 보기)를 사용 하 여 컨테이너 그룹에 사용할 수 있는 지역 및 최대 리소스는 다음과 같습니다.

| 영역 | OS | 최대 CPU | 최대 메모리 (GB) | 스토리지(GB) |
| -------- | -- | :---: | :-----------: | :---: |
| 오스트레일리아 동부, 브라질 남부, 캐나다 중부, 인도 중부, 미국 중부, 동아시아, 미국 동부, 일본 동부, 미국 중 북부, 서유럽, 미국 중 북부, 동남 아시아, 인도 남부, 영국 남부, 유럽 서부 | Windows | 4 | 16 | 20 |
| 미국 동부 2, 미국 서 부 2 | Windows | 2 | 3.5 | 20 |


## <a name="availability---virtual-network-deployment"></a>가용성-가상 네트워크 배포

[Azure 가상 네트워크](container-instances-vnet.md)에 배포 된 컨테이너 그룹에서 사용할 수 있는 지역 및 최대 리소스는 다음과 같습니다.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>가용성 - GPU 리소스(미리 보기)

[GPU 리소스](container-instances-gpu.md) (미리 보기)를 사용 하 여 배포 된 컨테이너 그룹에서 사용할 수 있는 지역 및 최대 리소스는 다음과 같습니다.

> [!IMPORTANT]
> GPU 리소스는 요청 시에만 사용할 수 있습니다. GPU 리소스에 대 한 액세스를 요청 하려면 [Azure 지원 요청][azure-support]을 제출 하세요.

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>다음 단계

추가 지역 또는 증가된 리소스 가용성을 확인하고 싶은 경우 [aka.ms/aci/feedback](https://aka.ms/aci/feedback)을 통해 팀에 알려주세요.

컨테이너 인스턴스 배포 문제 해결 방법에 대한 내용은 [Azure Container Instances로의 배포 문제 해결](container-instances-troubleshooting.md)을 참조하세요.


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest