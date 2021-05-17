---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93061636"
---
PowerShell을 로컬로 실행하는 경우 상승된 권한으로 PowerShell 콘솔을 열고 Azure 계정에 연결합니다. *Connect-AzAccount* cmdlet에서 자격 증명을 입력하라는 메시지를 표시합니다. 인증한 다음, Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

PowerShell을 로컬로 실행하는 대신 Azure Cloud Shell을 사용하는 경우 *Connect-AzAccount* 를 실행하지 않아도 됩니다. **사용해보기** 를 선택하면 Azure Cloud Shell이 자동으로 Azure 계정에 연결됩니다.

1. PowerShell을 로컬로 실행하는 경우 로그인합니다.

   ```azurepowershell
   Connect-AzAccount
   ```

1. 둘 이상의 구독이 있는 경우 Azure 구독 목록을 가져옵니다.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. 사용할 구독을 지정합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
