---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ade787370278de147e446ebb12f0f06637fd8125
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035737"
---
다음 구성은 아래 단계에 사용 되었습니다.

  | | |
  |---|---|
  |Computer| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
  |종속성| strongSwan |


다음 명령을 사용하여 필요한 strongSwan 구성을 설치합니다.

```
apt-get install strongswan-ikev2 strongswan-plugin-eap-tls
```

```
apt-get install libstrongswan-standard-plugins
```

```
apt-get install strongswan-pki
```
