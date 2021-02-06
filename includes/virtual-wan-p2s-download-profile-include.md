---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628908"
---
1. Virtual WAN에 대한 페이지에서 **사용자 VPN 구성** 을 선택합니다.
1. **사용자 vpn 구성** 페이지에서 구성을 선택 하 고 **가상 WAN 사용자 VPN 프로필 다운로드** 를 선택 합니다. WAN 수준 구성을 다운로드 하는 경우 기본 제공 Traffic Manager 기반 사용자 VPN 프로필을 가져옵니다. 글로벌 프로필 또는 허브 기반 프로필에 대한 자세한 내용은 [Hub 프로필](../articles/virtual-wan/global-hub-profile.md)을 참조하세요. 장애 조치 시나리오는 글로벌 프로필을 사용하여 간소화됩니다.

   
   어떤 이유로 허브를 사용할 수 없는 경우 서비스에서 제공하는 기본 제공 트래픽 관리를 사용하면 지점 및 사이트 간 사용자를 위해 Azure 리소스에 연결(다른 허브를 통해)할 수 있습니다. 언제든지 허브로 이동하여 허브별 VPN 구성을 다운로드할 수 있습니다. **사용자 VPN(지점 및 사이트 간)** 에서 가상 허브 **사용자 VPN** 프로필을 다운로드합니다.
1. **가상 WAN 사용자 VPN 프로필 다운로드** 페이지에서 **인증 유형을** 선택한 다음, **프로필 생성 및 다운로드** 를 선택 합니다. 프로필 패키지가 생성 되 고 구성 설정이 포함 된 zip 파일이 다운로드 됩니다.
