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
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "69520861"
---
다음 구성은 아래 단계에 사용되었습니다.

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |종속성| strongSwan |


다음 명령을 사용하여 필요한 strongSwan 구성을 설치합니다.

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

다음 명령을 사용하여 Azure 명령줄 인터페이스를 설치합니다.

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Azure CLI 를 설치하는 방법에 대한 추가 지침](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
