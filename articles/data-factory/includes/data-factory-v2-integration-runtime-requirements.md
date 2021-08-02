---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 66b7159d19838cf08158d794f9b8e8819efc3f41
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112042380"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime setup from connector articles.
-->
데이터 저장소가 온-프레미스 네트워크, Azure 가상 네트워크 또는 Amazon Virtual Private Cloud 내에 있는 경우 [자체 호스팅된 통합 런타임](../create-self-hosted-integration-runtime.md)을 구성하여 연결해야 합니다.

데이터 저장소가 관리형 클라우드 데이터 서비스인 경우 Azure Integration Runtime을 사용할 수 있습니다. 액세스가 방화벽 규칙에서 승인된 IP로 제한되는 경우 허용 목록에 [Azure Integration Runtime IP](../azure-integration-runtime-ip-addresses.md)를 추가할 수 있습니다. 

또한 Azure Data Factory의 [관리형 가상 네트워크 통합 런타임](../tutorial-managed-virtual-network-on-premise-sql-server.md) 기능을 사용하면 자체 호스팅 통합 런타임을 설치하고 구성하지 않고도 온-프레미스 네트워크에 액세스할 수 있습니다.

Data Factory에서 지원하는 네트워크 보안 메커니즘 및 옵션에 대한 자세한 내용은 [데이터 액세스 전략](../data-access-strategies.md)을 참조하세요.
