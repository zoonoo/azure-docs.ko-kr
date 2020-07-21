---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544681"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
데이터 저장소가 온-프레미스 네트워크, Azure virtual network 또는 Amazon 가상 사설 클라우드에 있는 경우 [자체 호스팅 integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) 을 설정 하 여 연결 해야 합니다.

데이터 저장소가 방화벽 규칙에 허용 목록 되는 Ip로 제한 된 관리 되는 클라우드 데이터 서비스인 경우 Azure integration runtime을 사용 하 여 [해당 ip](../articles/data-factory/azure-integration-runtime-ip-addresses.md) 를 허용 목록에 추가할 수 있습니다. 

일반적으로 데이터 저장소에 액세스 하는 Data Factory에서 지 원하는 네트워크 보안 메커니즘에 대 한 자세한 내용은 [데이터 액세스 전략](../articles/data-factory/data-access-strategies.md) 을 참조 하세요.
