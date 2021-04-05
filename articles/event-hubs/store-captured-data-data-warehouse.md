---
title: '자습서: Azure Synapse Analytics로 이벤트 데이터 마이그레이션 - Azure Event Hubs'
description: Azure Event Grid 및 Functions를 사용하여 Event Hubs 캡처된 데이터를 Azure Synapse Analytics로 마이그레이션하는 방법을 설명합니다.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854241"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>자습서: Event Grid 및 Azure Functions를 사용하여 캡처된 Event Hubs 데이터를 Azure Synapse Analytics로 마이그레이션
Azure Event Hubs [캡처](./event-hubs-capture-overview.md)를 사용하면 Azure Blob Storage 또는 Azure Data Lake Storage의 Event Hubs에서 스트리밍 데이터를 자동으로 캡처할 수 있습니다. 이 자습서에서는 [Event Grid](../event-grid/overview.md)에 의해 트리거된 Azure 함수를 사용하여 캡처된 Event Hubs 데이터를 스토리지에서 Azure Synapse Analytics로 마이그레이션하는 방법을 보여줍니다.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>다음 단계 
데이터 웨어하우스에서 강력한 데이터 시각화 도구를 사용하여 실행 가능한 인사이트를 얻을 수 있습니다.

이 문서에서는 [Azure Synapse Analytics에서 Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)를 사용하는 방법을 보여줍니다.