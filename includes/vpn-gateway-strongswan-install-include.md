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
ms.openlocfilehash: 089ad704a466590a9649107fef245a88d6a4d6e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102245114"
---
다음 구성이 아래 단계에 사용되었습니다.

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

다음 명령을 사용하여 Azure 명령줄 인터페이스를 설치합니다.

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Azure CLI를 설치하는 방법에 대한 추가 지침](/cli/azure/install-azure-cli-apt)