---
title: Azure Monitor의 IT 서비스 관리 커넥터
description: 이 문서에서는 ITSM 제품/서비스를 Azure Monitor의 ITSMC(IT 서비스 관리 커넥터)와 연결하여 ITSM 작업 항목을 중앙에서 모니터링하고 관리하는 방법에 대한 정보를 제공합니다.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 046fcb9d7473de5666b3acb25cbcaa1f9549e679
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039498"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM 제품/서비스를 IT Service Management Connector에 연결
이 문서에서는 ITSM 제품/서비스와 Log Analytics의 ITSMC(IT 서비스 관리 커넥터) 사이 연결을 구성하여 사용자의 작업 항목을 중앙에서 관리하는 방법에 대한 정보를 제공합니다. ITSMC에 대한 자세한 내용은 [개요](./itsmc-overview.md)를 참조하세요.

다음 ITSM 제품/서비스는 지원되지 않습니다. 제품을 ITSMC에 연결하는 방법에 대한 자세한 정보를 보려면 제품을 선택합니다.

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> Cherwell 및 Provance 고객에 게 웹 후크 [작업](./action-groups.md#webhook) 을 사용 하 여 Cherwell 및 Provance 끝점을 통합에 대 한 다른 솔루션으로 제안 합니다.

## <a name="next-steps"></a>다음 단계

* [ITSM 커넥터의 문제 해결](./itsmc-resync-servicenow.md)