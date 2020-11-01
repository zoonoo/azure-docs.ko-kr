---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145001"
---
지점 및 사이트 간 네이티브 Azure 인증서 인증 연결은이 연습에서 구성 하는 다음 항목을 사용 합니다.

* RouteBased VPN 게이트웨이입니다.
* Azure에 업로드된 루트 인증서에 대한 공개 키(.cer 파일)입니다. 인증서가 업로드되면 신뢰할 수 있는 인증서로 간주되며 인증에 사용됩니다.
* 루트 인증서에서 생성된 클라이언트 인증서. 클라이언트 인증서는 VNet에 연결할 각 클라이언트 컴퓨터에 설치됩니다. 클라이언트 인증에 사용됩니다.
* VPN 클라이언트 구성. Vpn 클라이언트는 VPN 클라이언트 구성 파일을 사용 하 여 구성 됩니다. 이러한 파일에는 클라이언트에서 VNet에 연결 하는 데 필요한 정보가 포함 되어 있습니다. 파일은 운영 체제에 기본적으로 제공된 기존의 VPN 클라이언트를 구성합니다. 연결되는 각 클라이언트는 구성 파일에서 설정을 사용하여 구성해야 합니다.
