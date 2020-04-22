---
title: 포함 파일
titleSuffix: Azure
description: 포함 파일
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678467"
---
구성을 시작하기 전에 필요한 모듈을 설치하고 가져옵니다. PowerShell에 모듈을 설치하려면 관리자 권한이 필요합니다.

1. Az 모듈을 설치하고 가져옵니다.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Az.피어링 모듈을 설치하고 가져옵니다.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 이 명령을 사용하여 모듈을 올바르게 가져왔는지 확인합니다.
    ```powershell
    Get-Module
    ```
1. 다음 명령을 사용하여 Azure 계정에 로그인합니다.
    ```powershell
    Connect-AzAccount
    ```
1. 계정의 구독을 확인하고 피어링을 만들 구독을 선택합니다.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 리소스 그룹이 아직 없는 경우 피어링을 만들기 전에 리소스 그룹을 만들어야 합니다. 다음 명령을 실행하여 수행할 수 있습니다.

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> ASN 및 구독을 아직 연결하지 않은 경우 [동료 피어 ASN](../howto-subscription-association-powershell.md)의 단계를 따릅니다. 이 작업은 피어링을 요청하는 데 필요합니다.

> [!NOTE]
> 리소스 그룹의 위치는 피어링을 설정하도록 선택한 위치와 독립적입니다.
&nbsp;
