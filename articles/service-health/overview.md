---
title: Azure Service Health 이란?
description: Azure 앱이 현재 및 향후 Azure 서비스 문제 및 유지 관리에 의해 어떤 영향을 받는지에 대한 개인 설정된 정보입니다.
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 039583d9243f8ce76b33afcee098e71a670b5285
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939210"
---
# <a name="what-is-azure-service-health"></a>Azure Service Health 이란?

Azure는 클라우드 리소스의 상태에 대 한 정보를 유지 하는 데 사용할 수 있는 일련의 경험을 제공 합니다. 이 정보에는 서비스에 영향을 주는 이벤트, 계획 된 유지 관리 및 가용성에 영향을 줄 수 있는 기타 변경 사항과 같은 현재 및 예정 된 문제가 포함 됩니다.

Azure Service Health는 세 개의 서로 다른 작은 서비스의 조합입니다.

Azure [상태](azure-status-overview.md) 는 azure의 azure **[상태 페이지](https://status.azure.com)** 에서 서비스 중단에 대해 알려줍니다. 페이지는 모든 Azure 지역에서 모든 Azure 서비스의 상태에 대 한 전역 뷰입니다. 상태 페이지는 광범위 한 영향을 주는 인시던트에 대해 좋은 참조 이지만 현재 Azure 사용자는 azure 서비스 상태를 활용 하 여 Azure 인시던트 및 유지 관리에 대 한 정보를 유지 하는 것이 좋습니다.

[Azure 서비스 상태](service-health-overview.md) 는 사용 중인 azure 서비스 및 지역의 상태를 개인 설정 된 보기로 제공 합니다. 인증된 Azure Service Health 환경이 현재 사용하는 서비스 및 리소스를 알고 있기 때문에 여기서는 중단, 계획된 유지 관리 작업 및 기타 상태 권고에 대해 서비스에 영향을 주는 통신을 찾는 것이 좋습니다. 서비스 문제, 계획된 유지 관리 또는 다른 변경 내용이 사용하는 Azure 서비스 및 지역에 영향을 주는 경우 기본 설정된 통신 채널을 통해 알려주도록 Service Health 경고를 설정하는 것이 Service Health를 사용하는 가장 좋은 방법입니다.

[Azure 리소스 상태](resource-health-overview.md) 는 특정 가상 머신 인스턴스와 같은 개별 클라우드 리소스의 상태에 대 한 정보를 제공 합니다. Azure Monitor를 사용하여 클라우드 리소스의 가용성 변경을 알리는 경고를 구성할 수도 있습니다. Azure Monitor 알림과 함께 Azure Resource Health를 통해 분 단위로 리소스의 가용성에 대해 계속 알려주고, 문제가 사용자 쪽에 있는지 아니면 Azure 플랫폼 이벤트로 인해 발생했는지를 신속하게 평가하는 데 도움을 줄 수 있습니다.

이러한 환경은 함께 가장 관련 있는 세분성에서 Azure의 상태에 대한 종합적인 보기를 제공합니다.

**Azure 상태 페이지, Azure Service Health 및 Azure Resource Health에 대 한 개요를 시청 하세요.**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]