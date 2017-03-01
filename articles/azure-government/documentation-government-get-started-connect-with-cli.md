---
title: "Azure CLI를 사용하여 Azure Government에 연결 | Microsoft Docs"
description: "Azure CLI와 연결하여 Azure Government에서 구독을 관리하는 방법에 대한 정보"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c7cbe993-375e-4aed-9aa5-2f4b2111f71c
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 6e070870a93d2636f076b339dd401358735a9bd0
ms.lasthandoff: 02/21/2017


---


# <a name="connect-to-azure-government-with-azure-cli"></a>Azure CLI를 사용하여 Azure Government에 연결

Azure CLI를 사용하려면 공용 Azure가 아닌 Azure Government에 연결해야 합니다. Azure CLI를 사용하여 스크립트를 통해 많은 구독을 관리하거나 Azure Portal에서 현재 사용할 수 없는 기능에 액세스할 수 있습니다. Azure Public에서 Azure CLI를 사용한 경우 대부분 동일합니다.  Azure Government의 차이점은 다음과 같습니다.

[Azure CLI를 설치](https://docs.microsoft.com/en-us/azure/xplat-cli-install)하는 여러 방법이 있습니다. 노드를 이미 설치한 경우 npm 패키지를 설치하는 것이 가장 쉬운 방법입니다.

npm 패키지에서 CLI를 설치하려면 [최신 Node.js 및 npm](https://nodejs.org/en/download/package-manager/)을 다운로드하고 설치했는지 확인합니다. 그런 다음 **npm install** azure-cli 패키지를 설치합니다.

```bash
npm install -g azure-cli
```

Linux 배포에서는 **npm** 명령을 정상적으로 실행하기 위해 다음과 같이 **sudo**를 사용해야 할 수도 있습니다.

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Linux 배포 또는 OS에 Node.js 및 npm을 설치하거나 업데이트해야 하는 경우 최신 Node.js LTS 버전(4.x)을 설치하는 것이 좋습니다. 이전 버전을 사용하는 경우 설치 오류가 발생할 수 있습니다.


Azure CLI를 설치했다면 Azure Government에 로그인해야 합니다.

```
azure login --username your-user-name@your-gov-tenant.onmicrosoft.com  --environment AzureUSGovernment
```

로그인하면 일반적인 방법으로 Azure CLI 명령을 실행할 수 있습니다.

```
azure webapp list my-resource-group
```
