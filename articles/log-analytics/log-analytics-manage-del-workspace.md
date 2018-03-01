---
title: "Azure Log Analytics 작업 영역 삭제 | Microsoft Docs"
description: "개인 구독에서 작업 영역을 만들었거나 작업 영역 모델을 재구성한 경우 Log Analytics 작업 영역을 삭제하는 방법을 알아봅니다."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 4f455e26f078360f17f8118f8b5b1db5bd75d473
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Azure Portal에서 Azure Log Analytics 작업 영역 삭제
이 항목에서는 Azure Portal을 사용하여 더 이상 필요하지 않을 수 있는 Log Analytics 작업 영역을 삭제하는 방법을 보여 줍니다. 

## <a name="to-delete-a-workspace"></a>작업 영역을 삭제하려면 
Log Analytics 작업 영역을 삭제하면 30일 내에 작업 영역과 관련된 모든 데이터가 서비스에서 삭제됩니다.  서비스 작업에 부정적인 영향을 미칠 수 있는 중요한 데이터 및 구성이 있을 수 있으므로 작업 영역을 삭제할 때는 주의해야 합니다.  
 
관리자이고 여러 사용자가 작업 영역과 연결되어 있는 경우 해당 사용자와 작업 영역 간의 연결이 끊어집니다. 사용자가 다른 작업 영역과 연결되어 있으면 다른 작업 영역에서 Log Analytics를 계속 사용할 수 있습니다. 그러나 다른 작업 영역과 연결되어 있지 않으면 Log Analytics를 사용하기 위해 작업 영역을 만들어야 합니다. 

1. [Azure 포털](http://portal.azure.com)에 로그인합니다. 
2. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
3. Log Analytics 구독 창에서 작업 영역을 선택한 다음 가운데 창 위쪽에서 **삭제**를 클릭합니다.<br><br> ![작업 영역 속성 창에서 옵션 삭제](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. 작업 영역 삭제를 확인할지 묻는 확인 메시지 창이 나타나면 **예**를 클릭합니다.<br><br> ![작업 영역 삭제 확인](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

