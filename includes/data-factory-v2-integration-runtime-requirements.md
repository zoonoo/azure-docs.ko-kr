---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91672224"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
데이터 저장소가 온-프레미스 네트워크, Azure 가상 네트워크 또는 Amazon Virtual Private Cloud 내에 있는 경우 [자체 호스팅된 통합 런타임](../articles/data-factory/create-self-hosted-integration-runtime.md)을 구성하여 연결해야 합니다.

또는 데이터 저장소가 관리형 클라우드 데이터 서비스인 경우 Azure 통합 런타임을 사용할 수 있습니다. 방화벽 규칙에서 승인된 IP에 액세스가 제한되는 경우 [Azure Integration Runtime IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md)를 허용 목록에 추가할 수 있습니다. 

Data Factory에서 지원하는 네트워크 보안 메커니즘 및 옵션에 대한 자세한 내용은 [데이터 액세스 전략](../articles/data-factory/data-access-strategies.md)을 참조하세요.
