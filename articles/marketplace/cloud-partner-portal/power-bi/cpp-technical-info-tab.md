---
title: Power BI 앱 제안에 대한 기술 정보 - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace의 Power BI 앱 제안에 대한 기술 정보 필드를 구성합니다.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: d96ef2fd318d6164e1b7dfc5c4b72d6957af0f3e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744437"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI 앱 기술 정보 탭

**새 제안** 페이지의 **기술 정보** 탭은 Power BI 설치 관리자 패키지 URL 및 새 제안의 유효성 검사에 필요한 추가 정보를 제공합니다.  초기 릴리스의 경우, 모든 Power BI 앱은 무료이며 Microsoft AppSource에서 추가 비용 없이 다운로드할 수 있습니다. 따라서 이 제안 유형에 대한 SKU(Stock Keeping Unit)을 정의할 수 없습니다.

![기술 정보 탭](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>기술 정보 필드 

**기술 정보** 탭에서 다음 필드를 제공해야 합니다.  필드 레이블에 별표(*)가 추가되면 필수 항목임을 나타냅니다.

|        필드          |  설명                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **설치 관리자 URL**     | 앱을 게시하고 프로덕션으로 승격할 때 Power BI에 의해 생성되는 주소입니다.  URL 생성 방법에 대한 자세한 내용은 [Power BI에 서비스 앱 게시](https://docs.microsoft.com/power-bi/service-create-distribute-apps)를 참조하세요.  |
|  **유효성 검사 지침**  |  Microsoft 유효성 검사 팀이 사용자의 앱을 구성, 연결 및 테스트하도록 지원하기 위한 선택적 텍스트 지침(최대 3000문자)입니다(예: 일반적인 구성 설정, 테스트 계정 또는 "데이터 연결" 옵션을 테스트하는 데 사용할 수 있는 매개 변수 등). 이 정보는 유효성 검사 팀만 볼 수 있으며 유효성 검사 목적을 위해서만 사용됩니다.  |
| **이 앱은 Power BI 콘텐츠 팩으로 만들어집니까?** | 현재 이는 내부적으로 사용되는 필드입니다. 값을 기본값 `No`로 설정된 상태로 두십시오. 그렇지 않고 이 필드를 `Yes`로 변경하면 게시에 방해가 될 수 있습니다.  |  
|  |  |


## <a name="next-steps"></a>다음 단계

다음 [상점 세부 정보](./cpp-storefront-details-tab.md) 탭에서 사용자의 앱에 대한 마케팅 및 법적 정보를 제공합니다.

