---
title: Dynamics 365 솔루션 준비 | Microsoft Docs
description: 구성 요소 패키징, 설치 및 제거를 위한 프레임워크
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricarod.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ea62a826b9f99e264cb1b2242f8d8108453d8e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397325"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365 솔루션 준비

Dynamics 365 솔루션 시스템은 특정 비즈니스 기능을 제공하는 구성 요소를 패키징, 설치 및 제거하기 위한 프레임워크입니다. ISV 및 다른 Microsoft Dynamics 365 파트너는 만든 확장을 배포하는 데 솔루션을 사용합니다.

기존 Dynamics 365(xRM) ISV인 경우 관리 솔루션을 이미 만들었고 solution.zip 파일을 보유하고 있을 것입니다. 솔루션에서 “표시 이름” 및 “설명” 필드에 고객에게 표시하려는 내용이 표시되는지 확인하세요. 이러한 내용은 CRM Online 관리 센터에 표시됩니다.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**참고:** 다음에 나오는 패키지 예제에서는 솔루션 이름을 "SampleSolution.zip"으로 가정합니다._

새 ISV인 경우 [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)에서 솔루션을 만드는 방법에 대한 자세한 내용을 확인할 수 있습니다.

솔루션에 지원 데이터가 필요한 경우:

* 테스트 환경에서 샘플 데이터 만들기
* 구성 마이그레이션 도구를 사용하여 데이터에 대한 비교 규칙으로 스키마를 만듭니다.
* 프로젝트 파일과 함께 구성 스키마를 저장합니다. 구성 데이터를 업데이트하는 경우 나중에 이 스키마가 필요합니다.
* 구성 데이터를 내보냅니다. 내보내기 파일에 내보내기를 나타내는 의미 있는 이름을 지정해야 합니다.
