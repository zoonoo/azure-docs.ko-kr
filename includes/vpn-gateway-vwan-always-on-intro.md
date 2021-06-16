---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/26/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1345df8991acba09feb65eb11e9eadc21b9c7af4
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579701"
---
Windows 10 VPN 클라이언트의 새로운 기능인 Always On은 VPN 연결을 유지할 수 있는 기능입니다. Always On을 사용하면 활성 VPN 프로필이 자동으로 연결되어 사용자 로그인, 네트워크 상태 변경, 디바이스 화면 활성 등의 트리거에 따라 연결된 상태를 유지할 수 있습니다.

Windows 10 Always On과 함께 게이트웨이를 사용하여 Azure에 대한 영구 사용자 터널 및 디바이스 터널을 설정할 수 있습니다.

Always On VPN 연결에는 다음 두 가지 유형의 터널 중 하나가 포함됩니다.

* **디바이스 터널**: 사용자가 디바이스에 로그인하기 전에 지정된 VPN 서버에 연결합니다. 로그인 전 연결 시나리오와 디바이스 관리는 디바이스 터널을 사용합니다.

* **사용자 터널**: 사용자가 디바이스에 로그인한 후에만 연결합니다. 사용자 터널을 사용하면 VPN 서버를 통해 조직 리소스에 액세스할 수 있습니다.

디바이스 터널과 사용자 터널은 VPN 프로필과 독립적으로 작동합니다. 동시에 연결할 수 있으며, 서로 다른 인증 방법과 다른 VPN 구성 설정을 적절하게 사용할 수 있습니다.
