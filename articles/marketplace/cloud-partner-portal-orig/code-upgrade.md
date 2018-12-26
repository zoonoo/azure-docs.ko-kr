---
title: 최신 플랫폼으로 코드 업그레이드 | Microsoft Docs
description: 이 항목에서는 Microsoft Dynamics 365 Operations 플랫폼 버전을 최신 플랫폼 릴리스로 업그레이드하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808015"
---
# <a name="upgrading-code-to-the-latest-platform"></a>최신 플랫폼으로 코드 업그레이드

이 문서에서는 Microsoft Dynamics 365 Operations 플랫폼 버전을 최신 플랫폼 릴리스로 업그레이드하는 방법을 설명합니다.

## <a name="overview"></a>개요

Microsoft Dynamics 365 Operations 플랫폼은 다음과 같은 구성 요소로 이루어져 있습니다.

AOS(Application Object Server), 데이터 관리 프레임워크, 보고 및 BI(비즈니스 인텔리전스) 프레임워크, 개발 도구, 분석 서비스 등의 Dynamics 365 for Operations 플랫폼 이진 파일. 다음 AOT(응용 프로그램 개체 트리) 패키지

1. 응용 프로그램 플랫폼
2. Application Foundation
3. Test Essentials

**중요**: 최신 Dynamics 365 for Operations 플랫폼으로 이동하려는 경우 플랫폼에 속하는 AOT 패키지의 사용자 지정(초과 계층)이 Dynamics 365 for Operations 구현에 포함되어 있으면 안 됩니다. 이 제한은 플랫폼 연속 업데이트를 원활하게 진행할 수 있도록 플랫폼 업데이트 3에서 도입되었습니다. 플랫폼 업데이트 3 이전 버전의 플랫폼에서 실행 중이라면 이 문서 끝부분의 이전 빌드에서 플랫폼 업데이트 3으로 업그레이드 섹션을 참조하세요.

코드 업그레이드에 대한 자세한 내용은 [여기](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update)를 참조하세요.