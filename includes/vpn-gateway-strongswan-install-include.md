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
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027250"
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

[Azure CLI를 설치 하는 방법에 대 한 추가 지침](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)