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
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774225"
---
구성을 시작 하기 전에 필요한 모듈을 설치 하 고 가져옵니다. PowerShell에서 모듈을 설치 하려면 관리자 권한이 필요 합니다.

1. 설치 및 가져오기 Az module
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. 설치 및 가져오기 Az. 피어 링 모듈
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 아래의 명령을 사용 하 여 모듈을 제대로 가져왔는지 확인 합니다.
    ```powershell
    Get-Module
    ```
1. 다음 명령을 사용 하 여 Azure 계정에 로그인 합니다.
    ```powershell
    Connect-AzAccount
    ```
1. 계정에 대 한 구독을 확인 하 고 피어 링을 만들려는 구독을 선택 합니다.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 리소스 그룹이 아직 없는 경우 피어 링을 만들기 전에 먼저 리소스 그룹을 만들어야 합니다. 다음 명령을 실행하여 수행할 수 있습니다.

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> 이미 ASN 및 구독을 연결 하지 않은 경우 [피어 ASN 연결](../howto-subscription-association-powershell.md)단계를 수행 합니다. 피어 링을 요청 하는 데 필요 합니다.

> [!NOTE]
> 리소스 그룹의 위치는 피어 링을 설정 하도록 선택 하는 위치와는 독립적입니다.
&nbsp;
