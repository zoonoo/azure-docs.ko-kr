---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e40f421c0fa45d772cd333dac51fe2bdf2779f48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828994"
---
Windows 10 VPN 클라이언트 Always On의 새로운 기능은 VPN 연결을 유지 관리할 수 있는 기능입니다. Always On 사용 하면 활성 VPN 프로필이 자동으로 연결 될 수 있으며 사용자 로그인, 네트워크 상태 변경, 장치 화면 활성 등의 트리거에 따라 연결 된 상태를 유지할 수 있습니다.

Windows 10 Always On와 함께 게이트웨이를 사용 하 여 Azure에 대 한 영구 사용자 터널 및 장치 터널을 설정할 수 있습니다.

Always On VPN 연결에는 다음 두 가지 유형의 터널 중 하나가 포함 됩니다.

* **장치 터널**: 사용자가 장치에 로그인 하기 전에 지정 된 VPN 서버에 연결 합니다. 사전 로그인 연결 시나리오 및 장치 관리는 장치 터널을 사용 합니다.

* **사용자 터널**: 사용자가 장치에 로그인 한 후에만 연결 합니다. 사용자 터널을 사용 하 여 VPN 서버를 통해 조직 리소스에 액세스할 수 있습니다.

장치 터널 및 사용자 터널은 VPN 프로필에 독립적으로 작동 합니다. 동시에 연결할 수 있으며, 다른 인증 방법 및 다른 VPN 구성 설정을 적절 하 게 사용할 수 있습니다.
