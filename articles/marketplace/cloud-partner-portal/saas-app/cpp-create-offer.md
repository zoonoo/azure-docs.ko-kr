---
title: Azure SaaS 응용 프로그램 제품 만들기 | Azure Marketplace
description: SaaS 애플리케이션 제안을 Azure Marketplace에 만드는 방법입니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 9ca85e4414739bff05176eddc1f31963e453dcf0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258114"
---
# <a name="create-a-new-saas-application-offer"></a>새 SaaS 애플리케이션 제안 만들기

이 문서에서는 Azure Marketplace에 SaaS 애플리케이션(앱) 제안 항목을 만들고 게시하는 방법에 대해 설명합니다.

> [!IMPORTANT] 
> SaaS로 마이그레이션되는 기능을 제공 합니다 [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory)합니다.  모든 새 게시자 새 SaaS 제품을 만들고 기존 제품을 관리 하기 위한 파트너 센터를 사용 해야 합니다.  SaaS 제공 서비스를 사용 하 여 현재 게시자 batchwise 마이그레이션될 클라우드 파트너 포털에서 파트너 센터에 있습니다.  클라우드 파트너 포털에는 특정 기존 제품 마이그레이션된 경우를 나타내기 위해 상태 메시지가 표시 됩니다.
> 자세한 내용은 [새 SaaS 제품을 만드는](../../partner-center-portal/create-new-saas-offer.md)합니다.

## <a name="offer-process"></a>제안 프로세스

다음 다이어그램에서는 SaaS 앱 제안을 만드는 프로세스를 보여 줍니다.

![SaaS 제안 만들기 프로세스](./media/saas-offer-process-overview.png)

## <a name="offer-components"></a>제안 구성 요소

SaaS 앱 제품은 다음 표에 설명된 5개 섹션으로 구성됩니다.

|  **자산 그룹**   |  **설명**  |
|  ---------------   |  ---------------  |
|    제품 설정  |  SaaS 앱에 대한 고유 ID를 구성하는 데 사용합니다.                 |
|  기술 정보    |  SaaS 솔루션 유형을 구성하고 애플리케이션에 대한 연결 세부 정보를 제공하는 데 사용합니다.                |
|  채널 정보      |   GTM 자료 및 연락처와 같은 채널 정보를 제공합니다.                |
|  시험 사용        |   고객이 구입하기 전에 제안을 테스트할 수 있는 서비스를 정의하기 위한 선택적 섹션입니다.                |
|  상점 세부 정보       | 마케팅, 법적 정보 및 잠재 고객 관리 자산 및 사양이 포함됩니다.  <ul><li> 마케팅 자산에는 제안 이름, 설명 및 로고가 포함됩니다.</li> <li> 법적 자산은 개인 정보 취급 방침, 사용 약관 및 기타 법률 문서를 포함합니다.</li>  <li> 잠재 고객 관리 정책을 사용하면 Azure Marketplace 최종 사용자 포털에서 잠재 고객을 처리하는 방법을 지정할 수 있습니다.</li> </ul> |
| 연락처            | 지원 연락처 및 정책 정보가 포함됩니다. |

## <a name="new-offer-form"></a>새 제안 양식

[Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인한 다음, 왼쪽 메뉴 모음에서 **+ 새 제품**을 선택합니다. [새 제안] 메뉴에서 **SaaS 앱**을 선택하여 [새 제안] 양식을 표시하고 새 SaaS 애플리케이션 제안에 대한 자산을 정의하는 프로세스를 시작합니다.

![SaaS 앱에 대한 새 제안 메뉴](./media/azure-new-saas-offer.png)

## <a name="next-steps"></a>다음 단계

SaaS 제안 유형에 대한 [새 제안] 페이지에는 새 제안을 만드는 데 사용할 일단의 탭과 양식 필드가 제공됩니다. 다음의 각 문서에서는 탭을 사용하여 새 제안에 대한 자산 그룹 및 지원 서비스를 정의하는 방법에 대해 설명합니다.

- [제품 설정 탭](./cpp-offer-settings-tab.md)
- [기술 정보 탭](./cpp-technical-info-tab.md)
- [채널 정보 탭](./cpp-channel-info-tab.md)
- [시험 사용 탭](./cpp-testdrive-tab.md)
- [상점 세부 정보 탭](./cpp-storefront-tab.md)
- [연락처 탭](./cpp-contacts-tab.md)
