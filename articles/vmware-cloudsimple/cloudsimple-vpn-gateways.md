---
title: Azure VMware 솔루션 (AVS)-VPN 게이트웨이
description: AVS 사이트 간 VPN 및 지점 및 사이트 간 VPN 개념에 대 한 자세한 정보
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73171e2c46bdf6c934db5777efe36ba51153a686
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024860"
---
# <a name="vpn-gateways-overview"></a>VPN gateway 개요

VPN gateway는 온-프레미스 위치의 AVS 지역 네트워크 또는 공용 인터넷을 통해 컴퓨터 간에 암호화 된 트래픽을 보내는 데 사용 됩니다. 각 지역에는 여러 연결을 지원할 수 있는 하나의 VPN gateway가 있을 수 있습니다. 동일한 VPN Gateway에 대해 여러 연결을 만들면 모든 VPN 터널이 사용 가능한 게이트웨이 대역폭을 공유합니다.

AVS는 두 종류의 VPN 게이트웨이를 제공 합니다.

* 사이트 간 VPN gateway
* 지점 및 사이트 간 VPN gateway

## <a name="site-to-site-vpn-gateway"></a>사이트 간 VPN gateway

사이트 간 VPN 게이트웨이는 AVS 지역 네트워크와 온-프레미스 데이터 센터 간에 암호화 된 트래픽을 보내는 데 사용 됩니다. 이 연결을 사용 하 여 온-프레미스 네트워크와 AVS 지역 네트워크 간의 네트워크 트래픽에 대 한 서브넷/CIDR 범위를 정의 합니다.

VPN gateway를 사용 하면 AVS 사설 클라우드의 온-프레미스 서비스와 온-프레미스 네트워크의 AVS 사설 클라우드의 서비스를 사용할 수 있습니다. AVS는 온-프레미스 네트워크에서 연결을 설정 하기 위한 정책 기반 VPN 서버를 제공 합니다.

사이트 간 VPN에 대 한 사용 사례:

* 온-프레미스 네트워크의 모든 워크스테이션에서 AVS 사설 클라우드 vCenter의 접근성
* 온-프레미스 Active Directory를 vCenter id 원본으로 사용 합니다.
* 온-프레미스 리소스의 VM 템플릿, Iso 및 기타 파일을 AVS 사설 클라우드 vCenter로 편리 하 게 전송할 수 있습니다.
* 온-프레미스 네트워크에서 사용자의 AVS 사설 클라우드에서 실행 되는 워크 로드의 접근성

![사이트 간 VPN 연결 토폴로지](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>암호화 매개 변수

사이트 간 VPN 연결은 다음과 같은 기본 암호화 매개 변수를 사용 하 여 보안 연결을 설정 합니다. 온-프레미스 VPN 장치에서 연결을 만들 때 온-프레미스 VPN 게이트웨이에서 지원 되는 다음 매개 변수를 사용 합니다.

#### <a name="phase-1-proposals"></a>1 단계 제안

| 매개 변수 | 제안 1 | 제안 2 | 제안 3 |
|-----------|------------|------------|------------|
| IKE 버전 | IKEv1 | IKEv1 | IKEv1 |
| 암호화 | AES 128 | AES 256 | AES 256 |
| 해시 알고리즘| SHA 256 | SHA 256 | SHA 1 |
| Diffie-hellman 그룹 (DH 그룹) | 2 | 2 | 2 |
| 수명 시간 | 28,800초 | 28,800초 | 28,800초 |
| 데이터 크기 | 4GB | 4GB | 4GB |

#### <a name="phase-2-proposals"></a>2 단계 제안

| 매개 변수 | 제안 1 | 제안 2 | 제안 3 |
|-----------|------------|------------|------------|
| 암호화 | AES 128 | AES 256 | AES 256 |
| 해시 알고리즘| SHA 256 | SHA 256 | SHA 1 |
| 완벽 한 전달 보안 그룹 (PFS 그룹) | 없음 | 없음 | 없음 |
| 수명 시간 | 1800 초 | 1800 초 | 1800 초 |
| 데이터 크기 | 4GB | 4GB | 4GB |


> [!IMPORTANT]
> VPN 장치에서 TCP MSS 고정 (1200)를 설정 합니다. 또는 VPN 장치에서 MSS 고정을 지원 하지 않는 경우 터널 인터페이스의 MTU를 1240 바이트로 설정할 수 있습니다.

## <a name="point-to-site-vpn-gateway"></a>지점 및 사이트 간 VPN gateway

지점 및 사이트 간 VPN은 AVS 지역 네트워크와 클라이언트 컴퓨터 간에 암호화 된 트래픽을 보내는 데 사용 됩니다. 지점 및 사이트 간 VPN은 AVS 사설 클라우드 vCenter 및 워크 로드 Vm을 포함 하 여 AVS 사설 클라우드 네트워크에 액세스 하는 가장 쉬운 방법입니다. 원격으로 AVS 사설 클라우드에 연결 하는 경우 지점 및 사이트 간 VPN 연결을 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [VPN gateway 설정](vpn-gateway.md)
