---
title: "Azure의 고가용성 포트 개요 | Microsoft Docs"
description: "내부 부하 분산 장치에서 고가용성 포트 부하 분산에 대해 알아보기"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: e72fc0d4323f7a2d203fee66311c3fea10ad7a09
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2017
---
# <a name="high-availability-ports-overview-preview"></a>고가용성 포트 개요(미리 보기)

Azure Load Balancer 표준에는 내부 Load Balancer를 사용하는 경우 모든 포트에서 TCP 및 UDP 흐름을 동시에 분산시키는 새로운 기능이 제공되어 있습니다. 

>[!NOTE]
> 고가용성 포트 기능은 Load Balancer 표준에서 사용할 수 있으며 현재 미리 보기 상태입니다. 미리 보기 중 이 기능은 일반 공급 릴리스에 있는 기능과 동일한 수준의 가용성 및 안정성을 제공하지 못할 수도 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 Microsoft Azure 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. Load Balancer 표준 리소스와 함께 HA 포트를 사용하려면 Load Balancer 표준 미리 보기에 등록해야 합니다. Load Balancer [표준 미리 보기](https://aka.ms/lbpreview#preview-sign-up)와 등록에 대한 지침을 따르십시오.

HA 포트 규칙은 내부 Load Balancer 표준에 구성된 부하 분산 규칙의 변형입니다.  모든 TCP 및 UDP 흐름이 내부 Load Balancer 표준 프런트 엔드의 모든 포트에 도달하도록 부하를 분산하기 위한 단일 LB 규칙을 제공하면 시나리오가 간소화됩니다. 부하 분산 의사 결정은 원본 IP 주소, 원본 포트, 대상 IP 주소, 대상 포트 및 프로토콜의 5 튜플을 기준으로 흐름별로 진행됩니다.

HA 포트는 많은 수의 포트에 부하를 분산해야 하는 기타 시나리오 뿐만 아니라 가상 네트워크 내의 NVA(네트워크 가상 어플라이언스)에 대한 고가용성 및 확장을 보장하는 것과 같은 중대한 시나리오를 가능하게 합니다. 

HA 포트는 프런트 엔드 및 백 엔드 포트를 **0**으로 설정하고 프로토콜을 **모두**로 설정하여 구성합니다.  이제 내부 부하 분산 장치 리소스는 포트 번호에 관계없이 모든 TCP 및 UDP 흐름의 부하를 분산합니다.

## <a name="why-use-ha-ports"></a>HA 포트를 사용하는 이유

### <a name="nva"></a>네트워크 가상 어플라이언스

여러 유형의 보안 위협에서 Azure 워크로드를 보호하기 위해 NVA(네트워크 가상 어플라이언스)를 사용할 수 있습니다. NVA가 이러한 시나리오에서 사용되는 경우 신뢰할 수 있고 가용성이 뛰어나며 요청 시 확장 가능해야 합니다.

Azure 내부 Load Balancer의 백 엔드 풀에 NVA 인스턴스를 추가하고 HA 포트 부하 Load Balancer 규칙을 구성하기만 하여 시나리오에서 이러한 목표를 달성할 수 있습니다.

HA 포트는 NVA HA 시나리오에 대해 다음과 같은 몇 가지 이점을 제공합니다.
- 인스턴스 상태 프로브별로 정상 인스턴스로 빠르게 장애 조치(Failover)
- n개 활성 인스턴스로 확장 가능한 뛰어난 성능
- n개의 활성 및 활성-수동 시나리오
- 어플라이언스를 모니터링하기 위해 Zookeeper 노드 같은 복합 솔루션의 필요성 해소

다음 예제는 허브 및 스포크 가상 네트워크 배포를 제공하며 스포크가 신뢰할 수 있는 공간을 나가기 전에 허브 가상 네트워크 및 NVA를 통해 트래픽을 터널링합니다. NVA는 HA 포트가 구성된 내부 Load Balancer 표준을 통해 지원됩니다.  그에 따라 모든 트래픽을 처리하고 전달할 수 있습니다. 

![ha 포트 예제](./media/load-balancer-ha-ports-overview/nvaha.png)

그림 1 - HA 모드로 배포된 NVA가 있는 허브 및 스포크 가상 네트워크

네트워크 가상 어플라이언스를 사용하는 경우 해당 공급자에게 HA 포트를 사용하는 최선의 방법과 지원되는 시나리오에 대해 문의하세요.

### <a name="load-balancing-large-numbers-of-ports"></a>많은 수의 포트에서 부하 분산

또한 많은 수의 포트에서 부하를 분산해야 하는 응용 프로그램 시나리오에 대해 HA 포트를 사용할 수도 있습니다. 이러한 시나리오는 HA 포트에 내부 [Load Balancer 표준](https://aka.ms/lbpreview)을 사용하여 간소화됩니다. 여기서는 단일 부하 분산 규칙이 모든 포트에 대해 하나씩 여러 개의 개별 부하 분산 규칙을 대신합니다.

## <a name="region-availability"></a>지역 가용성

HA 포트는 [Load Balancer 표준과 같은 지역](https://aka.ms/lbpreview#region-availability)에서 사용할 수 있습니다.  

## <a name="preview-sign-up"></a>미리 보기 등록

Load Balancer 표준에서 HA 포트 기능의 미리 보기에 참여하려면 Azure CLI 2.0 또는 PowerShell을 사용하여 액세스 권한을 얻도록 구독을 등록합니다.  다음 세 가지 단계를 따르세요.

>[!NOTE]
>이 기능을 사용하려면 HA 포트 외에 Load Balancer [표준 미리 보기](https://aka.ms/lbpreview#preview-sign-up)에도 등록해야 합니다. HA 포트 또는 Load Balancer 표준 미리 보기 등록에는 최대 1시간이 걸릴 수 있습니다.

### <a name="sign-up-using-azure-cli-20"></a>Azure CLI 2.0을 사용하여 등록

1. 공급자에 기능 등록
    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
2. 이전 작업을 완료하려면 최대 10분이 걸릴 수 있습니다.  다음 명령을 사용하여 작업 상태를 확인할 수 있습니다.

    ```cli
    az feature show --name AllowILBAllPortsRule --namespace Microsoft.Network
    ```
    
    아래와 같이 기능 등록 상태가 '등록됨'인 경우 3단계를 계속 진행합니다.
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowILBAllPortsRule",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. 리소스 공급자에 구독을 다시 등록하여 미리 보기 등록을 완료합니다.

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-using-powershell"></a>PowerShell을 사용하여 등록

1. 공급자에 기능 등록
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    
2. 이전 작업을 완료하려면 최대 10분이 걸릴 수 있습니다.  다음 명령을 사용하여 작업 상태를 확인할 수 있습니다.

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```
    아래와 같이 기능 등록 상태가 '등록됨'인 경우 3단계를 계속 진행합니다.
   
    ```
    FeatureName          ProviderName      RegistrationState
    -----------          ------------      -----------------
    AllowILBAllPortsRule Microsoft.Network Registered
    ```
    
3. 리소스 공급자에 구독을 다시 등록하여 미리 보기 등록을 완료합니다.

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```


## <a name="limitations"></a>제한 사항

다음은 HA 포트에 대해 지원되는 구성 또는 예외 사항입니다.

- 단일 프런트 엔드 IP 구성에는 HA 포트가 있는 단일 DSR Load Balancer 규칙이나 HA 포트가 있는 단일 비 DSR Load Balancer 규칙을 설정할 수 있습니다. 둘 다 사용할 수는 없습니다.
- 단일 네트워크 인터페이스 IP 구성에는 HA 포트가 있는 비 DSR 부하 분산 장치 규칙 하나만 설정할 수 있습니다. 이 ipconfig에 대해 다른 규칙을 구성할 수는 없습니다.
- 단일 네트워크 인터페이스 IP 구성에 HA 포트가 있는 하나 이상의 DSR 부하 분산 장치 규칙을 설정할 수 있습니다. 단, 각각의 프런트 엔드 IP 구성은 고유해야 합니다.
- 모든 부하 분산 규칙이 HA 포트(DSR 전용)이거나 모든 규칙이 비HA 포트(DSR 및 비DSR)인 경우 동일한 백 엔드 풀을 가리키는 두 개(또는 그 이상)의 부하 분산 장치 규칙이 공존할 수 있습니다. HA 포트와 비 HA 포트 규칙의 조합이 있는 경우 이러한 두 가지 부하 분산 규칙이 공존할 수 없습니다.
- HA 포트를 IPv6에는 사용할 수 없습니다.
- NVA 시나리오에 대한 흐름 대칭은 단일 NIC에서만 지원됩니다. [네트워크 가상 어플라이언스](#nva)에 대한 설명과 다이어그램을 참조하세요. 



## <a name="next-steps"></a>다음 단계

- [내부 Load Balancer 표준에서 HA 포트 구성](load-balancer-configure-ha-ports.md)
- [Load Balancer 표준 미리 보기에 대해 자세히 알아보기](https://aka.ms/lbpreview)

