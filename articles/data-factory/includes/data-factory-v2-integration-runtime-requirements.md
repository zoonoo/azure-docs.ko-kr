---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: f2d93449744ea7993ec22f200c51fea8280d1576
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108747805"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
데이터 저장소가 온-프레미스 네트워크, Azure 가상 네트워크 또는 Amazon Virtual Private Cloud 내에 있는 경우 [자체 호스팅된 통합 런타임](../create-self-hosted-integration-runtime.md)을 구성하여 연결해야 합니다.

또는 데이터 저장소가 관리형 클라우드 데이터 서비스인 경우 Azure 통합 런타임을 사용할 수 있습니다. 방화벽 규칙에서 승인된 IP에 액세스가 제한되는 경우 [Azure Integration Runtime IP](../azure-integration-runtime-ip-addresses.md)를 허용 목록에 추가할 수 있습니다. 

Data Factory에서 지원하는 네트워크 보안 메커니즘 및 옵션에 대한 자세한 내용은 [데이터 액세스 전략](../data-access-strategies.md)을 참조하세요.
