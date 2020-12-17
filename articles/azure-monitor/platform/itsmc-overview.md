---
title: IT 서비스 관리 커넥터 개요
description: 이 문서에서는 ITSMC (IT 서비스 관리 커넥터의 개요를 제공 합니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93759cf239a2e7ef79c719c83299740ea3722130
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614560"
---
# <a name="it-service-management-connector-overview"></a>IT 서비스 관리 커넥터 개요

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

ITSMC (IT 서비스 관리 커넥터)를 사용 하면 지원 되는 ITSMC (IT Service Management) 제품 또는 서비스에 Azure를 연결할 수 있습니다.

Azure Log Analytics 및 Azure Monitor와 같은 azure 서비스는 Azure 및 비 Azure 리소스의 문제를 검색, 분석 및 해결 하는 도구를 제공 합니다. 하지만 문제와 관련 된 작업 항목은 일반적으로 ITSM 제품 또는 서비스에 있습니다. ITSMC는 Azure와 ITSMC 도구 간에 양방향 연결을 제공 하 여 문제를 더 빠르게 해결할 수 있도록 합니다.

## <a name="configuration-steps"></a>구성 단계

ITSMC는 다음 ITSM 도구와의 연결을 지원합니다.

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Cherwell 및 Provance 고객에 게 웹 후크 [작업](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) 을 사용 하 여 Cherwell 및 Provance 끝점을 통합에 대 한 다른 솔루션으로 제안 합니다.

ITSMC를 사용하면 다음 작업을 수행할 수 있습니다.

-  Azure 경고 (메트릭 경고, 활동 로그 경고 및 Log Analytics 경고)에 따라 ITSM 도구에서 작업 항목을 만듭니다.
-  필요에 따라 인시던트 및 변경 요청 데이터를 ITSM 도구에서 Azure Log Analytics 작업 영역으로 동기화할 수 있습니다.

약관 및 개인 정보 취급 방침에 대 한 자세한 내용은 [Microsoft 개인 정보 취급](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)방침을 참조 하세요.

다음 단계를 완료 하 여 ITSMC 사용을 시작할 수 있습니다.

1. [IT 서비스 관리 커넥터를 사용 하 여 ITSM 제품/서비스를 연결 합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
2. [ITSMC를 추가 합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector)
3. [ITSM 연결을 만듭니다.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-an-itsm-connection)
4. [연결을 사용 합니다.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#use-itsmc)

## <a name="next-steps"></a>다음 단계

[IT 서비스 관리 커넥터에 ITSM 제품/서비스를 추가 합니다.](./itsmc-connections.md)
