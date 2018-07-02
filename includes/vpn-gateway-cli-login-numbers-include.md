---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70a9e2a8f6327c0af92698b49d5b622bebba3661
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313611"
---
1. [az login](/cli/azure/#login) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 로그인에 대한 자세한 내용은 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.

  ```azurecli
  az login
  ```
2. Azure 구독이 두 개 이상인 경우 계정의 구독을 나열합니다.

  ```azurecli
  az account list --all
  ```
3. 사용할 구독을 지정합니다.

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```