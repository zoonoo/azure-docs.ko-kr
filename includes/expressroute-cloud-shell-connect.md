---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67181936"
---
 Azure Cloud Shell을 사용하는 경우 '사용해 보세요'를 클릭한 후 자동으로 Azure 계정에 로그인합니다. 로컬로 로그인하려면 상승된 권한으로 PowerShell 콘솔을 열고 cmdlet을 실행하여 연결합니다.

```azurepowershell
Connect-AzAccount
```

둘 이상의 구독이 있는 경우 Azure 구독 목록을 가져옵니다.

```azurepowershell-interactive
Get-AzSubscription
```

사용할 구독을 지정합니다.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```