---
title: Azure Firewall Manager Preview 배포 개요
description: Azure Firewall Manager Preview에 필요한 상위 수준 배포 단계에 대해 알아봅니다.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488259"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Azure Firewall Manager Preview 배포 개요

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager Preview를 배포하는 방법은 여러 가지지만 다음과 같은 일반적인 프로세스가 권장됩니다.

## <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> `Register-AzProviderFeature` PowerShell 명령을 사용하여 Azure Firewall Manager Preview를 명시적으로 사용하도록 설정해야 합니다.
>PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>기능 등록이 완료될 때까지 최대 30분이 걸립니다. 다음 명령을 실행하여 등록 상태를 확인합니다.
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>일반 배포 프로세스

1. 허브 및 스포크 아키텍처 만들기

   - Azure Firewall Manager를 사용하여 보안 가상 허브를 만들고 가상 네트워크 연결을 추가합니다.<br>*or*<br>
   - 가상 WAN 허브를 만들고 가상 네트워크 연결을 추가합니다.
2. 보안 공급자 선택

   - 보안 가상 허브를 만드는 동안 완료됩니다.<br>*or*<br>
   - 기존 가상 WAN 허브를 보안 가상 허브로 변환합니다.
3. 방화벽 정책을 만들고 허브와 연결

   - Azure Firewall을 사용하는 경우에만 적용 가능합니다.
   - 타사 SECaaS(SECurity as a Service) 정책은 파트너 관리 환경을 통해 구성됩니다.
4. 보안 허브로 트래픽을 라우팅하도록 경로 설정 구성

   - 보안 가상 허브 경로 설정 페이지를 사용하여 스포크 가상 네트워크에서 UDR(사용자 정의 경로) 없이 필터링 및 로깅을 위해 보안 허브로 트래픽을 쉽게 라우팅할 수 있습니다.

> [!NOTE]
> - 지역별 가상 WAN당 허브가 두 개 이상 있을 수 없습니다. 그러나 지역에서 가상 WAN을 더 추가할 수 있습니다.
> - vWAN에서 허브의 IP 공간이 겹쳐서도 안 됩니다.
> - 허브 VNet 연결은 허브와 같은 지역에 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용하여 Azure Firewall Manager Preview로 클라우드 네트워크 보호](secure-cloud-network.md)