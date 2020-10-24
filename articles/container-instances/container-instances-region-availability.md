---
title: 지역별 리소스 가용성
description: 여러 다른 Azure 지역의 Azure Container Instances 서비스에 대한 컴퓨팅 및 메모리 리소스 가용성입니다.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 1ed3f50198c0410d9c893fe87523fa214ca03d88
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521461"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 지역의 Azure Container Instances에 대한 리소스 가용성

이 문서에서는 Azure 지역 및 대상 운영 체제에서 Azure Container Instances 계산, 메모리 및 저장소 리소스의 가용성을 자세히 설명 합니다. Azure Container Instances 사용 가능한 지역에 대 한 일반적인 목록은 [사용 가능한 지역](https://azure.microsoft.com/regions/services/)을 참조 하세요.

제공되는 값은 [컨테이너 그룹](container-instances-container-groups.md) 배포당 사용 가능한 최대 리소스입니다. 값은 게시 시점에 제공됩니다.

> [!NOTE]
> 이러한 리소스 제한 내에서 만든 컨테이너 그룹은 배포 지역 내의 사용 가능 여부의 적용을 받습니다. 영역이 과부하 상태에 있는 경우 인스턴스를 배포할 때 오류가 발생할 수 있습니다. 이러한 배포 실패를 완화 하려면 리소스 설정이 낮은 인스턴스를 배포 하거나 나중에 배포를 시도 하거나 사용 가능한 리소스가 있는 다른 지역에 배포를 시도 합니다.

배포의 할당량 및 기타 제한에 대한 내용은 [Azure Container Instances의 할당량 및 제한](container-instances-quotas.md)을 참조하세요.

## <a name="linux-container-groups"></a>Linux 컨테이너 그룹

일반 배포, [Azure 가상 네트워크](container-instances-vnet.md) 배포 및 [GPU 리소스](container-instances-gpu.md) (미리 보기)를 사용한 배포에서 Linux 컨테이너를 사용 하는 컨테이너 그룹에는 다음 지역과 최대 리소스를 사용할 수 있습니다.

> [!IMPORTANT]
> 영역의 최대 리소스는 배포에 따라 다릅니다. 예를 들어 Azure 가상 네트워크 배포에서는 지역에 일반적인 배포 보다 최대 CPU 및 메모리 크기가 다를 수 있습니다. 동일한 지역에 GPU 리소스를 사용 하는 배포에 대 한 다른 최대값 집합이 있을 수도 있습니다. 아래 테이블에서 해당 지역의 최대 값을 확인 하기 전에 배포 유형을 확인 합니다.

| 지역 | 최대 CPU | 최대 메모리 (GB) | VNET 최대 CPU | VNET 최대 메모리 (GB) | 스토리지(GB) | GPU Sku (미리 보기) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| 오스트레일리아 동부 | 4 | 16 | 4 | 16 | 50 | N/A |
| 브라질 남부 | 4 | 16 | 2 | 8 | 50 | N/A |
| 캐나다 중부 | 4 | 16 | 4 | 16 | 50 | N/A |
| 인도 중부 | 4 | 16 | N/A | N/A | 50 | V100 |
| 미국 중부 | 4 | 16 | 4 | 16 | 50 | N/A |
| 동아시아 | 4 | 16 | 4 | 16 | 50 | N/A |
| 미국 동부 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| 미국 동부 2 | 4 | 16 | 4 | 16 | 50 | N/A |
| 일본 동부 | 2 | 8 | 4 | 16 | 50 | N/A |
| 한국 중부 | 4 | 16 | N/A | N/A | 50 | N/A |
| 미국 중북부 | 2 | 3.5 | 4 | 16 | 50 | N/A |
| 북유럽 | 4 | 16 | 4 | 16 | 50 | K80 |
| 미국 중남부 | 4 | 16 | 4 | 16 | 50 | N/A |
| 동남아시아 | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| 인도 남부 | 4 | 16 | N/A | N/A | 50 | N/A |
| 영국 남부 | 4 | 16 | 4 | 16 | 50 | N/A |
| 미국 중서부| 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| 서유럽 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| 미국 서부 | 4 | 16 | 2 | 4 | 16| N/A |
| 미국 서부 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

[GPU 리소스](container-instances-gpu.md) (미리 보기)를 사용 하 여 배포 된 컨테이너 그룹에 사용할 수 있는 최대 리소스는 다음과 같습니다.

| GPU Sku | GPU 수 | 최대 CPU | 최대 메모리 (GB) | 스토리지(GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Windows 컨테이너 그룹

다음 지역과 최대 리소스는 컨테이너 그룹에서 [지원 되](container-instances-faq.md#what-windows-base-os-images-are-supported) 는 Windows Server 컨테이너와 함께 사용할 수 있습니다.

| 지역 | Windows Server 2016 최대 CPU | Windows Server 2016 최대 메모리 (GB) | Windows Server 2019 LTSC Max CPU | Windows Server 2019 LTSC 최대 메모리 (GB) | 스토리지(GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| 오스트레일리아 동부 | 2 | 3.5 | 4 | 16 | 20 |
| 브라질 남부 | 4 | 16 | 4 | 16 | 20 |
| 캐나다 중부 | 2 | 3.5 | 4 | 16 | 20 |
| 인도 중부 | 2 | 3.5 | 4 | 16 | 20 |
| 미국 중부 | 2 | 3.5 | 4 | 16 | 20 |
| 동아시아 | 2 | 3.5 | 4 | 16 | 20 |
| 미국 동부 | 2 | 8 | 4 | 16 | 20 |
| 미국 동부 2 | 2 | 3.5 | 2 | 3.5 | 20 |
| 프랑스 중부 | 4 | 16 | 4 | 16 | 20 |
| 일본 동부 | 4 | 16 | 4 | 16 | 20 |
| 한국 중부 | 4 | 16 | 4 | 16 | 20 |
| 미국 중북부 | 2 | 3.5 | 4 | 16 | 20 |
| 북유럽 | 2 | 3.5 | 4 | 16 | 20 |
| 미국 중남부 | 2 | 3.5 | 4 | 16 | 20 |
| 인도 남부 | 2 | 3.5 | 4 | 16 | 20 |
| 동남아시아 | 2 | 3.5 | 4 | 16 | 20 |
| 영국 남부 | 2 | 3.5 | 4 | 16 | 20 |
| 미국 중서부 | 4 | 16 | 4 | 16 | 20 |
| 서유럽 | 4 | 16 | 4 | 16 | 20 |
| 미국 서부 | 4 | 14 | N/A | N/A | 20 |
| 미국 서부 2 | 2 | 3.5 | 2 | 3.5 | 20 |

## <a name="next-steps"></a>다음 단계

추가 지역 또는 증가된 리소스 가용성을 확인하고 싶은 경우 [aka.ms/aci/feedback](https://aka.ms/aci/feedback)을 통해 팀에 알려주세요.

컨테이너 인스턴스 배포 문제 해결 방법에 대한 내용은 [Azure Container Instances로의 배포 문제 해결](container-instances-troubleshooting.md)을 참조하세요.


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
