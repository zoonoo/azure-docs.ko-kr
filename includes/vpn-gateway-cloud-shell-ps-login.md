---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77133620"
---
상승된 권한으로 PowerShell 콘솔을 엽니다.

Azure PowerShell을 로컬로 실행하는 경우 Azure 계정에 연결합니다. *Connect-AzAccount* cmdlet에서 자격 증명을 입력하라는 메시지를 표시합니다. 인증한 다음, Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다. 대신 Azure 클라우드 셸을 사용하는 경우 *Connect-AzAccount*를 실행할 필요가 없습니다. Azure 클라우드 셸은 Azure 계정에 자동으로 연결됩니다.

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