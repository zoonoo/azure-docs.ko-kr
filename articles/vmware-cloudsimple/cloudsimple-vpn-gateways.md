---
title: Azure VMware Solution by CloudSimple - VPN Gateway
description: CloudSimple 지역과 다른 리소스 간에 암호화된 트래픽을 전송하는 데 사용되는 CloudSimple 사이트 간 VPN Gateway와 지점 및 사이트 간 VPN Gateway에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e013bb96990a8f3a0ef7d3a58529b200919e276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140635"
---
# <a name="vpn-gateways-overview"></a>VPN Gateway 개요

VPN Gateway는 온-프레미스 위치의 CloudSimple 지역 네트워크 간이나 퍼블릭 인터넷을 통해 컴퓨터 간에 암호화된 트래픽을 전송하는 데 사용됩니다.  각 지역에서 여러 연결을 지원할 수 있는 VPN Gateway 1개를 사용할 수 있습니다. 동일한 VPN Gateway에 대해 여러 연결을 만들면 모든 VPN 터널이 사용 가능한 게이트웨이 대역폭을 공유합니다.

CloudSimple은 다음 두 가지 종류의 VPN Gateway를 제공합니다.

* 사이트 간 VPN Gateway
* 지점 및 사이트 간 VPN Gateway

## <a name="site-to-site-vpn-gateway"></a>사이트 간 VPN Gateway

사이트 간 VPN Gateway는 CloudSimple 지역 네트워크와 온-프레미스 데이터 센터 간에 암호화된 트래픽을 전송하는 데 사용됩니다. 이 연결을 사용하여 온-프레미스 네트워크와 CloudSimple 지역 네트워크 간 네트워크 트래픽의 서브넷/CIDR 범위를 정의할 수 있습니다.

VPN Gateway를 통해 프라이빗 클라우드의 온-프레미스 서비스와 온-프레미스 네트워크의 프라이빗 클라우드 서비스를 사용할 수 있습니다.  CloudSimple은 온-프레미스 네트워크에서 연결을 설정하기 위한 정책 기반의 VPN 서버를 제공합니다.

사이트 간 VPN의 사용 사례:

* 프라이빗 클라우드 vCenter에 대한 온-프레미스 네트워크 워크스테이션에서의 접근성
* 온-프레미스 Active Directory를 vCenter ID 원본으로 사용
* 온-프레미스 리소스에서 프라이빗 클라우드 vCenter로 VM 템플릿, ISO, 기타 파일을 편리하게 전송
* 프라이빗 클라우드에서 실행되는 워크로드에 대한 온-프레미스 네트워크에서의 접근성

![사이트 간 VPN 연결 토폴로지](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>암호화 매개 변수

사이트 간 VPN 연결은 다음과 같은 기본 암호화 매개 변수를 사용하여 보안 연결을 설정합니다.  온-프레미스 VPN 디바이스에서 연결을 만드는 경우 온-프레미스 VPN Gateway에서 지원하는 다음 매개 변수를 사용합니다.

#### <a name="phase-1-proposals"></a>1단계 제안

| 매개 변수 | 제안 1 | 제안 2 | 제안 3 |
|-----------|------------|------------|------------|
| IKE 버전 | IKEv1 | IKEv1 | IKEv1 |
| 암호화 | AES 128 | AES 256 | AES 256 |
| 해시 알고리즘| SHA 256 | SHA 256 | SHA 1 |
| DH 그룹(Diffie Hellman 그룹) | 2 | 2 | 2 |
| 수명 | 28,800초 | 28,800초 | 28,800초 |
| 데이터 크기 | 4GB | 4GB | 4GB |

#### <a name="phase-2-proposals"></a>2단계 제안

| 매개 변수 | 제안 1 | 제안 2 | 제안 3 |
|-----------|------------|------------|------------|
| 암호화 | AES 128 | AES 256 | AES 256 |
| 해시 알고리즘| SHA 256 | SHA 256 | SHA 1 |
| PFS 그룹(Perfect Forward Secrecy 그룹) | 없음 | 없음 | 없음 |
| 수명 | 1,800초 | 1,800초 | 1,800초 |
| 데이터 크기 | 4GB | 4GB | 4GB |


> [!IMPORTANT]
> VPN 디바이스에서 TCP MSS 제한을 1200으로 설정합니다. 또는 VPN 디바이스가 MSS 제한을 지원하지 않는 경우 대신 터널 인터페이스의 MTU를 1,240바이트로 설정할 수 있습니다.

## <a name="point-to-site-vpn-gateway"></a>지점 및 사이트 간 VPN Gateway

지점 및 사이트 간 VPN은 CloudSimple 지역 네트워크와 클라이언트 컴퓨터 간에 암호화된 트래픽을 전송하는 데 사용됩니다.  지점 및 사이트 간 VPN은 프라이빗 클라우드 vCenter와 워크로드 VM을 포함하여 프라이빗 클라우드 네트워크에 액세스하는 가장 쉬운 방법입니다.  프라이빗 클라우드에 원격으로 연결하는 경우 지점 및 사이트 간 VPN 연결을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [VPN Gateway 설정](vpn-gateway.md)
