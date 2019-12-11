---
title: 자습서-Azure 예산 만들기 및 관리 | Microsoft Docs
description: 이 자습서는 사용 하는 Azure 서비스의 비용을 계획 하 고 고려 하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 4b0e5be0e5f71fa61e3a6c6fa39e720c4c298898
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967946"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>자습서: Azure 예산 만들기 및 관리

A Cost Management költségvetései segítenek a tervezésben, és elősegítik a vállalaton belüli elszámolhatóságot. A költségvetések segítségével elszámolhat az egy adott időszak alatt használt vagy előfizetett Azure-szolgáltatásokkal. 이를 통해 다른 사용자에 게 비용을 사전에 관리 하 고 시간에 따른 지출 진행 방식을 모니터링할 수 있습니다. 만든 예산 임계값을 초과 하는 경우 알림만 트리거됩니다. 영향을 받지 않는 리소스가 없으며 사용이 중지 되지 않습니다. 예산을 사용 하 여 비용을 분석할 때 지출을 비교 하 고 추적할 수 있습니다.

비용 및 사용 현황 데이터는 일반적으로 12-16 시간 이내에 사용할 수 있으며 예산 비용은 4 시간 마다 계산 됩니다. 전자 메일 알림은 일반적으로 12-16 시간 이내에 수신 됩니다.

미래의 만료 날짜를 선택 하는 경우 동일한 예산 금액에 대해 (월별, 분기별 또는 매년) 예산이 자동으로 설정 됩니다. 동일한 예산 양으로 다시 설정 되므로 예산 통화 금액이 이후 기간에 대해 다를 때 별도의 예산을 만들어야 합니다.

이 자습서의 예제에서는 EA (Azure 기업계약) 구독의 예산 작성 및 편집 과정을 안내 합니다.

Azure Portal 비디오를 [사용 하 여 구독에 대 한 예산 적용](https://www.youtube.com/watch?v=UrkHiUx19Po) 을 시청 하 여 지출을 모니터링 하는 Azure에서 예산을 만드는 방법을 살펴봅니다.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

## <a name="prerequisites"></a>Előfeltételek

예산은 다양 한 Azure 계정 유형에 대해 지원 됩니다. 지원 되는 계정 유형의 전체 목록을 보려면 [데이터 Cost Management 이해](understand-cost-mgt-data.md)를 참조 하세요. 예산을 보려면 Azure 계정에 대 한 읽기 이상의 권한이 필요 합니다.

 Azure EA 구독의 경우 예산 보기에 대 한 읽기 권한이 있어야 합니다. 예산을 만들고 관리 하려면 참가자 권한이 있어야 합니다. EA 구독 및 리소스 그룹에 대 한 개별 예산을 만들 수 있습니다. 그러나 EA 청구 계정에 대 한 예산을 만들 수는 없습니다.

다음 Azure 권한 또는 범위는 사용자 및 그룹별 예산에 대 한 구독 당 지원 됩니다. 범위에 대 한 자세한 내용은 [범위 이해 및 사용](understand-work-scopes.md)을 참조 하세요.

- Tulajdonos – Költségvetéseket hozhat létre, módosíthat vagy törölhet az előfizetésben.
- 참가자 및 Cost Management 참여자 – 자신의 예산을 생성, 수정 또는 삭제할 수 있습니다. Módosíthatja a mások által létrehozott költségvetések költségvetési összegét.
- 판독기 및 Cost Management 판독기 – 사용 권한이 있는 예산을 볼 수 있습니다.

Cost Management 데이터에 대 한 사용 권한을 할당 하는 방법에 대 한 자세한 내용은 [Cost Management 데이터에 대 한 액세스 할당](assign-access-acm-data.md)을 참조 하세요.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-budget-in-the-azure-portal"></a>Azure Portal에서 예산 만들기

Létrehozhat egy Azure-előfizetési költségkeretet egy havi, negyedéves vagy éves időszakra. Az Azure Portal navigációs tartalma attól függ, hogy előfizetéshez vagy felügyeleti csoporthoz hozza létre a költségvetést.

Költségvetés létrehozásához vagy megtekintéséhez nyissa meg a kívánt hatókört az Azure Portalon, majd válassza ki a **Költségvetések** lehetőséget a menüben. 예를 들어 **구독**으로 이동 하 여 목록에서 구독을 선택한 다음 메뉴에서 **예산** 을 선택 합니다. 약 **범위** 를 사용 하 여 예산에서 관리 그룹과 같은 다른 범위로 전환 합니다. 범위에 대 한 자세한 내용은 [범위 이해 및 사용](understand-work-scopes.md)을 참조 하세요.

A költségvetések létrehozása után egy egyszerű nézetben tekintheti meg a hozzájuk tartozó jelenlegi kiadásait.

Válassza a **Hozzáadás** lehetőséget.

![A példa a már létrehozott költségvetések listáját mutatja](./media/tutorial-acm-create-budgets/budgets01.png)

Győződjön meg róla, hogy a **Költségvetés létrehozása** ablakban feltüntetett hatókör helyes. Válassza ki a hozzáadni kívánt szűrőket. A szűrők lehetővé teszik, hogy adott költségekre, például az előfizetésben lévő erőforráscsoportokkal, illetve egy szolgáltatással, például a virtuális gépekkel járó költségekre vonatkozóan hozzon létre költségvetést. A költségelemzések során használt szűrőket alkalmazhatja a költségvetésekre is.

Miután azonosította a hatókört és a szűrőket, gépelje be a költségvetés nevét. 그런 다음 월별, 분기별 또는 연간 예산 다시 설정 기간을 선택 합니다. Ez a visszaállítási időtartam határozza meg azt az időszakot, amelyet a költségvetés vizsgál. A költségvetés által kiértékelt költség minden új periódus elején nulláról kezdődik. A negyedéves költségvetés ugyanúgy működik, mint a havi költségvetés. A különbség az, hogy a negyedévre vonatkozó költségvetési összeg egyenlően oszlik meg a negyedév három hónapja között. Az éves költségvetési összeg a naptári év mind a 12 hónapja között oszlik meg egyenlően.

Ha használatalapú, MSDN- vagy Visual Studio-előfizetéssel rendelkezik, a számlázási időszak nem feltétlenül esik egybe a naptári hónapokkal. Az ilyen típusú előfizetésekhez és erőforráscsoportokhoz olyan költségvetést is létrehozhat, amely a számlázási időszakhoz vagy a naptári hónapokhoz igazodnak. 청구서 기간에 맞게 조정 된 예산을 만들려면 **청구 월**, **청구 분기**또는 **청구 연도의**다시 설정 기간을 선택 합니다. 월에 정렬 된 예산을 만들려면 **월별**, **분기별**또는 **매년**다시 설정 기간을 선택 합니다.

Ezután határozza meg a lejárati dátumot, amikortól a költségvetés érvénytelenné válik, és leáll a költségek kiértékelése.

A költségvetésben eddig kiválasztott mezők alapján egy grafikon jelenik meg, amely segítséget nyújt a költségvetéshez használandó küszöbérték kiválasztásában. A javasolt költségvetés a jövőbeli időszakokban potenciálisan felmerülő legmagasabb becsült költségen alapul. Igény esetén módosíthatja a költségvetési összeget.

![Példa költségvetés létrehozására havi költségadatokkal ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

A költségvetés összegének konfigurálása után válassza a **Következő** lehetőséget a költségvetési riasztások konfigurálásához. Meg kell adnia legalább egy költségküszöböt (a költségvetés %-ában) és egy hozzá tartozó e-mail-címet. Igény szerint akár öt küszöbértéket és öt e-mail-címet is megadhat egyetlen költségvetéshez. 예산 임계값이 충족 되 면 전자 메일 알림은 일반적으로 20 시간 이내에 수신 됩니다. 알림에 대 한 자세한 내용은 [비용 경고 사용](cost-mgt-alerts-monitor-usage-spending.md)을 참조 하세요. 아래 예제에서는 예산의 90%에 도달 하면 전자 메일 경고가 생성 됩니다. 예산 API를 사용 하 여 예산을 만드는 경우 사용자에 게 경고를 받을 역할을 할당할 수도 있습니다. 사용자에 게 역할 할당은 Azure Portal 지원 되지 않습니다. Azure 예산 API에 대 한 자세한 내용은 [예산 api](/rest/api/consumption/budgets)를 참조 하세요.

![경고 조건을 보여 주는 예제](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Miután létrehozta a költségvetést, a rendszer megjeleníti annak költségelemzését. 지출 추세와 관련 하 여 예산을 확인 하는 것은 [비용과 지출 분석](quick-acm-cost-analysis.md)을 시작할 때의 첫 번째 단계 중 하나입니다.

![Példa költségelemzésben kimutatott költségvetésre és kiadásokra](./media/tutorial-acm-create-budgets/cost-analysis.png)

Az előző példában létrehozott egy költségvetést egy előfizetéshez. Költségvetést azonban erőforráscsoportokhoz is létrehozhat. 리소스 그룹에 대 한 예산을 만들려면 **Cost Management + 청구** &gt; **구독** 으로 이동 하 &gt; 구독 > **리소스 그룹** 을 선택 하 > 리소스 그룹 > **예산 > 선택한** 다음 예산을 **추가** 합니다.

## <a name="trigger-an-action-group"></a>작업 그룹 트리거

Amikor költségvetést hoz létre vagy módosít egy előfizetés vagy erőforráscsoport hatókörére vonatkozóan, konfigurálhatja úgy, hogy meghívjanak egy műveletcsoportot. A műveletcsoport különböző műveleteket hajthat végre a költségvetési küszöbérték elérésekor. 작업 그룹은 현재 구독 및 리소스 그룹 범위 에서만 지원 됩니다. 작업 그룹에 대 한 자세한 내용은 [Azure Portal에서 작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조 하세요. 작업 그룹에서 예산 기반 자동화를 사용 하는 방법에 대 한 자세한 내용은 [Azure 예산으로 비용 관리](../billing/billing-cost-management-budget-scenario.md)를 참조 하세요.



작업 그룹을 만들거나 업데이트 하려면 예산을 만들거나 편집 하는 동안 **작업 그룹 관리** 를 선택 합니다.

![관리 작업 그룹을 표시 하는 예산을 만드는 예](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


그런 다음 **작업 그룹 추가** 를 선택 하 고 작업 그룹을 만듭니다.


![작업 그룹 추가 상자의 이미지](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

작업 그룹을 만든 후에는 상자를 닫아 예산으로 돌아갑니다.

개별 임계값이 충족 될 때 작업 그룹을 사용 하도록 예산을 구성 합니다. 최대 5 개의 서로 다른 임계값이 지원 됩니다.

![경고 조건에 대 한 작업 그룹 선택을 보여 주는 예제](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

다음 예제에서는 50%, 75% 및 100%로 설정 된 예산 임계값을 보여 줍니다. 각는 지정 된 작업 그룹 내에서 지정 된 작업을 트리거하기 위해 구성 됩니다.

![다양 한 작업 그룹 및 동작 유형을 사용 하 여 구성 된 경고 조건을 보여 주는 예제](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

작업 그룹에 대 한 예산 통합은 공통 경고 스키마를 사용 하지 않는 작업 그룹에 대해서만 작동 합니다. 스키마를 사용 하지 않도록 설정 하는 방법에 대 한 자세한 내용은 [일반적인 경고 스키마 사용 어떻게 할까요?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) 을 참조 하세요.

## <a name="edit-an-existing-budget"></a>기존 예산 편집
기존 예산을 변경 하려면 주 예산 창으로 이동 하 고 편집 하려는 예산을 선택 합니다. 예산 정보 창에서 **예산 편집** 을 선택 하 고 필요한 내용을 변경한 다음 **저장**을 선택 합니다.

![예산 편집을 보여 주는 예제](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * 예산 편집

다음 자습서로 이동 하 여 cost management 데이터에 대 한 되풀이 내보내기를 만듭니다.

> [!div class="nextstepaction"]
> [내보낸 데이터 만들기 및 관리](tutorial-export-acm-data.md)
