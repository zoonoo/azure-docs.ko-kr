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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678467"
---
구성을 시작하기 전에 필요한 모듈을 설치하고 가져옵니다. PowerShell에서 모듈을 설치하려면 관리자 권한이 필요합니다.

1. Az 모듈을 설치하고 가져옵니다.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Az.Peering 모듈을 설치하고 가져옵니다.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 다음 명령을 사용하여 모듈을 제대로 가져왔는지 확인합니다.
    ```powershell
    Get-Module
    ```
1. 다음 명령을 사용하여 Azure 계정에 로그인합니다.
    ```powershell
    Connect-AzAccount
    ```
1. 계정에 대한 구독을 확인하고 피어링을 만들 구독을 선택합니다.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 아직 리소스 그룹이 없는 경우 피어링을 만들기 전에 먼저 리소스 그룹을 만들어야 합니다. 다음 명령을 실행하여 수행할 수 있습니다.

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> ASN과 구독을 아직 연결하지 않은 경우 [피어 ASN 연결](../howto-subscription-association-powershell.md)의 단계를 따르세요. 이 작업은 피어링을 요청하는 데 필요합니다.

> [!NOTE]
> 리소스 그룹의 위치는 피어링을 설정하기 위해 선택한 위치와 무관합니다.
&nbsp;
