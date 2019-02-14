---
title: Power BI 애플리케이션 제품 만들기 - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace용 Power BI 앱 제품을 만드는 방법입니다.
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
ms.openlocfilehash: e0e1311276b858f1ac16fe6f17ccad49dd9901c9
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665934"
---
# <a name="create-a-power-bi-application-offer"></a>Power BI 애플리케이션 제품 만들기

이 섹션에서는 [AppSource](https://appsource.microsoft.com)용 새 Power BI 앱 제품을 만드는 데 필요한 단계를 나열합니다. 모든 제품은 AppSource에 고유 엔터티로 나타납니다.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에서 새 제품을 만들 때 제품에 대한 자산의 네 가지 그룹을 제공해야 합니다.

|   자산 그룹      | 설명                                                                         |
| ----------------   | ----------------                                                                    |
| 제품 설정     | 기본 ID와 제품에 대한 이름                                      |
| 기술 정보     | 클라이언트의 Power BI 작업 영역에서 앱을 설치하는 데 사용되는 설치 관리자 URL입니다. 이 URL을 생성하는 방법에 대한 자세한 내용은 [Power BI 앱 설명서](https://go.microsoft.com/fwlink/?linkid=2028636)를 참조하세요.   |
| 상점 세부 정보 | 마케팅, 법적 정보 및 잠재 고객 관리 자산을 포함합니다. 마케팅 자산에는 제품 이름 및 로고가 포함됩니다.  법적 정보 자산에는 개인정보처리방침, 사용 약관 및 기타 법률 문서가 포함됩니다.  잠재 고객 관리 정책을 사용하면 AppSource 최종 사용자 포털에서 잠재 고객을 처리하는 방법을 지정할 수 있습니다. |
| 연락처           | 지원 연락처 및 정책 정보가 포함됩니다.                                     |
|    |     |


## <a name="new-offer-form"></a>새 제안 양식

Cloud 파트너 포털에 로그인한 후 왼쪽 메뉴 모음에서 **+ 새 제품** 항목을 클릭합니다.  결과 메뉴에서 **Power BI 앱**을 클릭하여 **새 제품** 양식을 표시하고, 새 앱 제품에 대한 자산을 정의하는 프로세스를 시작합니다.

![Power BI 제품 메뉴 항목](./media/new-offer-menu.png)

> [!WARNING] 
> **Power BI 앱** 옵션이 표시되지 않거나 활성화되지 않으면 이 제품 유형을 만들 수 있는 권한이 계정에 없는 것입니다. 개발자 계정에 등록하는 것을 포함하여 이 제안 유형에 대한 모든 [필수 조건](./cpp-prerequisites.md)이 충족되었는지 확인합니다.


## <a name="next-steps"></a>다음 단계

이 섹션의 이후 문서에서는 **새 제품** 페이지(Power BI 앱 제품 유형의 경우)의 탭을 미러링합니다. 각 문서에서는 관련 탭을 사용하여 새 앱 제품에 대한 자산 그룹과 지원 서비스를 정의하는 방법을 설명합니다.

-  [제품 설정 탭](./cpp-offer-settings-tab.md)
-  [기술 정보 탭](./cpp-technical-info-tab.md)
-  [상점 세부 정보 탭](./cpp-storefront-details-tab.md)
-  [연락처](./cpp-contacts-tab.md)
