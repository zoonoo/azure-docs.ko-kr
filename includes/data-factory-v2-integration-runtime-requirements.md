---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529394"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
데이터 저장소가 온-프레미스 네트워크, Azure 가상 네트워크 또는 Amazon Virtual Private Cloud 내에 있는 경우 [자체 호스팅된 통합 런타임](../articles/data-factory/create-self-hosted-integration-runtime.md)을 설정하여 연결해야 합니다.

데이터 저장소가 관리형 클라우드 데이터 서비스인 경우 Azure 통합 런타임을 사용할 수 있습니다. 액세스가 방화벽 규칙의 허용 목록에 추가된 IP로 제한되는 경우 [Azure Integration Runtime IP](../articles/data-factory/azure-integration-runtime-ip-addresses.md)를 허용 목록에 추가하도록 선택할 수 있습니다. 

Data Factory에서 지원하는 네트워크 보안 메커니즘 및 옵션에 대한 자세한 내용은 [데이터 액세스 전략](../articles/data-factory/data-access-strategies.md)을 참조하세요.
