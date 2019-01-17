---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201485"
---
상승된 권한으로 PowerShell 콘솔을 엽니다.



Azure PowerShell을 로컬로 실행하는 경우 Azure 계정에 연결합니다. *Connect-AzureRmAccount* cmdlet에서 자격 증명을 입력하라는 메시지를 표시합니다. 인증한 다음, Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다. PowerShell을 로컬로 실행하지 않고, 대신 브라우저에서 Azure Cloud Shell '사용해 보세요.'를 사용하는 경우 이 첫 번째 단계를 건너뜁니다. 자동으로 Azure 계정에 연결됩니다.

```azurepowershell
Connect-AzureRmAccount
```

둘 이상의 구독이 있는 경우 Azure 구독 목록을 가져옵니다.

```azurepowershell-interactive
Get-AzureRmSubscription
```

사용할 구독을 지정합니다.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```