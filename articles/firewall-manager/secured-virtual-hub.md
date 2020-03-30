---
title: 보안 가상 허브란?
description: 보안 가상 허브에 대해 알아보기
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518438"
---
# <a name="what-is-a-secured-virtual-hub"></a>보안 가상 허브란?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

가상 허브는 다른 리소스에서 연결을 가능하게 하는 Microsoft 관리 가상 네트워크입니다. Azure 포털의 가상 WAN에서 가상 허브를 만들면 가상 허브 VNet 및 게이트웨이(선택 사항)가 해당 구성 요소로 만들어집니다.

*보안* 가상 허브는 Azure 방화벽 관리자에서 구성 한 관련 된 보안 및 라우팅 정책 Azure [가상 WAN 허브입니다.](../virtual-wan/virtual-wan-about.md#resources) 보안 가상 허브를 사용하여 트래픽 거버넌스 및 보호를 위한 기본 보안 서비스를 통해 허브 및 스포크 및 전이 아키텍처를 쉽게 만들 수 있습니다. 

온-프레임 연결 없이 보안 가상 허브를 관리되는 중앙 VNet으로 사용할 수 있습니다. Azure 방화벽 배포에 이전에 필요했던 중앙 VNet을 대체합니다. 보안 가상 허브는 자동화된 라우팅을 제공하므로 방화벽을 통해 트래픽을 라우팅하도록 자체 DR(사용자 정의 경로)을 구성할 필요가 없습니다.

또한 전체 가상 WAN 아키텍처의 일부로 보안 가상 허브를 사용할 수도 있습니다. 이 아키텍처는 Azure에 대한 보안, 최적화 및 자동화된 분기 연결을 제공합니다. Azure 방화벽 및 기타 타사 보안을 서비스(SECaaS) 공급자로 포함하여 네트워크 트래픽을 보호하고 제어하는 서비스를 선택할 수 있습니다.

## <a name="create-a-secured-virtual-hub"></a>보안 가상 허브 만들기

Azure 포털에서 방화벽 관리자를 사용하여 새 보안 가상 허브를 만들거나 Azure Virtual WAN을 사용하여 이전에 만든 기존 가상 허브를 변환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

보안 가상 허브를 만들고 이를 사용하여 허브 및 스포크 네트워크를 보호하고 관리하려면 [자습서: Azure 포털을 사용하여 Azure 방화벽 관리자를 사용하여 클라우드 네트워크 보안](secure-cloud-network.md)을 참조하세요.