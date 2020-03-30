---
title: 클라우드에 의한 Azure VMware 솔루션 간단함 - VPN 게이트웨이
description: CloudSimple 사이트 간 VPN 및 지점 간 VPN 개념에 대해 자세히 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024860"
---
# <a name="vpn-gateways-overview"></a>VPN 게이트웨이 개요

VPN 게이트웨이는 온-프레미스 위치에서 CloudSimple 지역 네트워크 또는 공용 인터넷을 통해 컴퓨터 간에 암호화된 트래픽을 전송하는 데 사용됩니다.  각 리전에는 하나의 VPN 게이트웨이가 있을 수 있으며, 이 게이트웨이는 여러 연결을 지원할 수 있습니다. 동일한 VPN Gateway에 대해 여러 연결을 만들면 모든 VPN 터널이 사용 가능한 게이트웨이 대역폭을 공유합니다.

CloudSimple은 두 가지 종류의 VPN 게이트웨이를 제공합니다.

* 사이트 간 VPN 게이트웨이
* 지점 간 VPN 게이트웨이

## <a name="site-to-site-vpn-gateway"></a>사이트 간 VPN 게이트웨이

사이트 간 VPN 게이트웨이는 CloudSimple 지역 네트워크와 온-프레미스 데이터 센터 간에 암호화된 트래픽을 전송하는 데 사용됩니다. 온-프레미스 네트워크와 CloudSimple 지역 네트워크 간의 네트워크 트래픽에 대해 이 연결을 사용하여 서브넷/CIDR 범위를 정의합니다.

VPN 게이트웨이를 사용하면 프라이빗 클라우드의 온-프레미스 서비스 및 온-프레미스 네트워크에서 프라이빗 클라우드의 서비스를 사용할 수 있습니다.  CloudSimple은 온-프레미스 네트워크에서 연결을 설정하기 위한 정책 기반 VPN 서버를 제공합니다.

사이트 간 VPN의 사용 사례:

* 온-프레미스 네트워크의 모든 워크스테이션에서 프라이빗 클라우드 vCenter의 액세스 가능성.
* 온-프레미스 Active Directory를 vCenter ID 원본으로 사용합니다.
* 온-프레미스 리소스에서 프라이빗 클라우드 vCenter로 VM 템플릿, ISO 및 기타 파일을 편리하게 전송할 수 있습니다.
* 온-프레미스 네트워크에서 프라이빗 클라우드에서 실행되는 워크로드의 액세스 가능성

![사이트 간 VPN 연결 토폴로지](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>암호화 매개 변수

사이트 간 VPN 연결은 다음과 같은 기본 암호화 매개 변수를 사용하여 보안 연결을 설정합니다.  온-프레미스 VPN 장치에서 연결을 만들 때 온-프레미스 VPN 게이트웨이에서 지원하는 다음 매개 변수 중 일부를 사용합니다.

#### <a name="phase-1-proposals"></a>1단계 제안

| 매개 변수 | 제안 1 | 제안 2 | 제안 3 |
|-----------|------------|------------|------------|
| IKE 버전 | IKEv1 | IKEv1 | IKEv1 |
| 암호화 | AES 128 | AES 256 | AES 256 |
| 해시 알고리즘| SHA 256 | SHA 256 | SHA 1 |
| 디피 헬만 그룹 (DH 그룹) | 2 | 2 | 2 |
| 수명 | 28,800초 | 28,800초 | 28,800초 |
| 데이터 크기 | 4GB | 4GB | 4GB |

#### <a name="phase-2-proposals"></a>2단계 제안

| 매개 변수 | 제안 1 | 제안 2 | 제안 3 |
|-----------|------------|------------|------------|
| 암호화 | AES 128 | AES 256 | AES 256 |
| 해시 알고리즘| SHA 256 | SHA 256 | SHA 1 |
| 완벽한 전달 비밀 그룹 (PFS 그룹) | None | None | None |
| 수명 | 1,800초 | 1,800초 | 1,800초 |
| 데이터 크기 | 4GB | 4GB | 4GB |


> [!IMPORTANT]
> VPN 장치에서 TCP MSS 클램핑을 1200으로 설정합니다. 또는 VPN 장치가 MSS 클램핑을 지원하지 않는 경우 터널 인터페이스에서 MTU를 대신 1240바이트로 설정할 수 있습니다.

## <a name="point-to-site-vpn-gateway"></a>지점 간 VPN 게이트웨이

지점 간 VPN은 CloudSimple 지역 네트워크와 클라이언트 컴퓨터 간에 암호화된 트래픽을 전송하는 데 사용됩니다.  지점 간 VPN은 프라이빗 클라우드 vCenter 및 워크로드 VM을 포함하여 프라이빗 클라우드 네트워크에 가장 쉽게 액세스할 수 있는 방법입니다.  프라이빗 클라우드에 원격으로 연결하는 경우 지점 간 VPN 연결을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [VPN 게이트웨이 설정](vpn-gateway.md)
