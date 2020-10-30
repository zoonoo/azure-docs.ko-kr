---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042596"
---
클라이언트의 아키텍처와 일치하는 버전이면 각 Windows 클라이언트 컴퓨터에서 동일한 VPN 클라이언트 구성 패키지를 사용할 수 있습니다. 지원되는 클라이언트 운영 체제의 목록은 [VPN Gateway FAQ](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)의 지점 및 사이트 간 연결 섹션을 참조하세요.

>[!NOTE]
>연결하려는 Windows 클라이언트 컴퓨터에서 관리자 권한이 있어야 합니다.
>

다음 단계에 따라 인증서 인증을 위한 Windows VPN 기본 클라이언트를 구성합니다.

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다. 
1. 해당 패키지를 두 번 클릭하여 설치합니다. SmartScreen 팝업이 표시 되 면 **자세한 정보** 를 클릭 하 고 **실행** 을 클릭 합니다.
1. 클라이언트 컴퓨터에서 **네트워크 설정** 으로 이동 하 고 **VPN** 을 클릭 합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다.
1. 연결을 시도하기 전에 먼저 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. Azure 기본 인증서 인증 유형을 사용할 때 인증을 위해 클라이언트 인증서가 필요합니다.