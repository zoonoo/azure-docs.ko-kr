---
title: Azure Service Health란?
description: Azure 앱이 현재 및 향후 Azure 서비스 문제 및 유지 관리에 의해 어떤 영향을 받는지에 대한 개인 설정된 정보입니다.
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: 44b819a88baec383d2faf80ab37edb903b0a5f4d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77654089"
---
# <a name="what-is-azure-service-health"></a>Azure Service Health란?

Azure는 클라우드 리소스의 상태에 대해 지속적으로 알려주도록 일련의 경험을 제공합니다. 이 정보에는 서비스에 영향을 주는 이벤트, 계획된 유지 관리 및 가용성에 영향을 줄 수 있는 기타 변경 내용과 같은 현재 및 향후 문제가 포함됩니다.

Azure Service Health는 세 개의 서로 다른 작은 서비스의 조합입니다.

[Azure 상태](azure-status-overview.md)는 **[Azure 상태 페이지](https://status.azure.com)** 의 Azure에서 서비스 중단에 대해 알려줍니다. 페이지는 모든 Azure 지역에 걸친 모든 Azure 서비스 상태의 전역적 보기입니다. 상태 페이지에는 광범위한 영향을 가진 인시던트의 경우 유용한 참조이지만 현재 Azure 사용자는 Azure 서비스 상태를 활용하여 최신 Azure 인시던트 및 유지 관리에 대해 아는 것이 좋습니다.

[Azure 서비스 상태](service-health-overview.md)에서는 사용 중인 Azure 서비스 및 지역의 상태를 볼 수 있도록 맞춤형 보기를 제공합니다. 인증된 Azure Service Health 환경이 현재 사용하는 서비스 및 리소스를 알고 있기 때문에 여기서는 중단, 계획된 유지 관리 작업 및 기타 상태 권고에 대해 서비스에 영향을 주는 통신을 찾는 것이 좋습니다. 서비스 문제, 계획된 유지 관리 또는 다른 변경 내용이 사용하는 Azure 서비스 및 지역에 영향을 주는 경우 기본 설정된 통신 채널을 통해 알려주도록 Service Health 경고를 설정하는 것이 Service Health를 사용하는 가장 좋은 방법입니다.

[Azure 리소스 상태](resource-health-overview.md)에서는 특정 가상 머신 인스턴스와 같은 개별 클라우드 리소스 상태에 대한 정보를 제공합니다. Azure Monitor를 사용하여 클라우드 리소스의 가용성 변경을 알리는 경고를 구성할 수도 있습니다. Azure Monitor 알림과 함께 Azure Resource Health를 통해 분 단위로 리소스의 가용성에 대해 계속 알려주고, 문제가 사용자 쪽에 있는지 아니면 Azure 플랫폼 이벤트로 인해 발생했는지를 신속하게 평가하는 데 도움을 줄 수 있습니다.

이러한 환경은 함께 가장 관련 있는 세분성에서 Azure의 상태에 대한 종합적인 보기를 제공합니다.

**Azure 상태 페이지, Azure Service Health 및 Azure Resource Health의 개요 보기**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]