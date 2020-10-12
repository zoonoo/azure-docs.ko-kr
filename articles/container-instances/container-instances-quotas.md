---
title: 서비스 할당량 및 지역 가용성
description: Azure Container Instances 서비스의 할당량, 제한 및 지역 가용성입니다.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: eaaa8e0b2d72aaea546a1bc351da40932c1deb14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384834"
---
# <a name="quotas-and-limits-for-azure-container-instances"></a>Azure Container Instances 할당량 및 제한

모든 Azure 서비스에는 리소스와 기능에 대한 특정 기본 제한과 할당량이 있습니다. 이 문서에서는 Azure Container Instances의 기본 할당량 및 제한에 대해 자세히 설명합니다.

Azure Container Instances에 대 한 계산, 메모리 및 저장소 리소스의 가용성은 지역 및 운영 체제에 따라 다릅니다. 자세한 내용은 [Azure Container Instances에 대 한 리소스 가용성](container-instances-region-availability.md)을 참조 하세요.

[목록 사용](/rest/api/container-instances/location/listusage) API를 사용 하 여 구독에 대 한 지역에서 현재 할당량 사용량을 검토 합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 이러한 증대를 지원하는 하나 이상의 리소스에 대해 증대를 요청하려면 [Azure 지원 요청][azure-support](**발급 요청**에 대해 "할당량" 선택)을 제출합니다.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
