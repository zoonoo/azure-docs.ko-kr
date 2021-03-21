---
title: 파일 포함
description: 파일 포함
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 089ad704a466590a9649107fef245a88d6a4d6e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102245114"
---
다음 구성은 아래 단계에 사용 되었습니다.

- 컴퓨터: Ubuntu Server 18.04
- 종속성: strongSwan


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

다음 명령을 사용 하 여 Azure 명령줄 인터페이스를 설치 합니다.

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Azure CLI를 설치 하는 방법에 대 한 추가 지침](/cli/azure/install-azure-cli-apt)