---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116980"
---
Windows 10 VPN 클라이언트의 새로운 기능인 Always On은 VPN 연결을 유지하는 기능입니다. Always On을 사용하면 활성 VPN 프로필이 자동으로 연결되고 사용자 로그인, 네트워크 상태 변경 또는 장치 화면 활성과 같은 트리거에 따라 연결된 상태로 유지될 수 있습니다.

Windows 10 Always On이 있는 게이트웨이를 사용하여 영구 사용자 터널 및 장치 터널을 Azure에 설정할 수 있습니다. 이 문서에서는 항상 켜져 있는 VPN 사용자 터널을 구성하는 데 도움이 됩니다.

항상 VPN 연결에는 두 가지 유형의 터널이 포함됩니다.

* **장치 터널**: 사용자가 장치에 로그인하기 전에 지정된 VPN 서버에 연결합니다. 사전 로그인 연결 시나리오 및 장치 관리는 장치 터널을 사용합니다.

* **사용자 터널**: 사용자가 장치에 로그인한 후에만 연결됩니다. 사용자 터널을 사용하면 VPN 서버를 통해 조직 리소스에 액세스할 수 있습니다.

장치 터널과 사용자 터널은 VPN 프로필과 독립적으로 작동합니다. 동시에 연결할 수 있으며 다른 인증 방법 및 기타 VPN 구성 설정을 적절하게 사용할 수 있습니다.
