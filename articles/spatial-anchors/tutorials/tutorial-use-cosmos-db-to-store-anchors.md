---
title: 자습서 - Azure Spatial Anchors 및 Azure Cosmos DB 백 엔드를 사용하여 세션과 디바이스 간 공유 | Microsoft Docs
description: 이 자습서에서는 백 엔드 서비스와 Azure Cosmos DB가 있는 Unity의 디바이스 간에 Azure Spatial Anchors 식별자를 공유하는 방법을 알아봅니다.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f0cd42fc37727099ed95a1c6fc2d427b7862412e
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753493"
---
# <a name="tutorial-sharing-across-sessions-and-devices-with-azure-spatial-anchors-and-an-azure-cosmos-db-back-end"></a>자습서: Azure 공간 앵커 및 Azure Cosmos DB 백 엔드를 사용하여 세션과 디바이스 간 공유

이 자습서에서는 [Azure Spatial Anchors](../overview.md)를 사용하여 다음을 수행하는 방법을 보여줍니다.

1. 한 세션에서 앵커를 만든 다음, 동일하거나 다른 디바이스의 다른 세션에서 앵커를 찾습니다. 다른 날이 그 예입니다.
2. 여러 디바이스가 동시에 같은 위치에 있을 수 있는 앵커를 만듭니다.

![지속성](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md)는 시간이 지남에 따라 디바이스에서 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 작업이 완료되면 둘 이상의 디바이스에 배포할 수 있는 앱이 제공됩니다. 한 인스턴스에서 만든 Azure Spatial Anchors는 Cosmos DB를 사용하여 식별자를 다른 사용자와 공유합니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 앵커 공유에 사용할 수 있는 Azure에 ASP.NET Core 웹앱을 배포하여 Cosmos DB에 저장합니다.
> * 빠른 시작의 Unity 샘플 내에 AzureSpatialAnchorsLocalSharedDemo 장면을 구성하여 공유 앵커 웹앱을 활용합니다.
> * 하나 이상의 디바이스에 배포하고 실행합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

이 자습서에서는 Unity와 Azure Cosmos DB를 사용하지만 다른 디바이스 간에 Azure Spatial Anchor 식별자를 공유하는 방법에 대한 예를 보여줄 뿐입니다. 다른 언어와 백 엔드 기술을 사용하여 동일한 목표를 달성할 수 있습니다. 또한 이 자습서에 사용되는 ASP.NET Core 웹앱은 .NET Core 2.2 SDK에 종속됩니다. 일반 Azure Web Apps(Windows용)에서는 정상적으로 실행되지만, 현재 Linux용 Azure Web Apps에서는 작동하지 않습니다.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

나중에 사용되므로 `Connection String`을 기록해 둡니다.

## <a name="deploy-your-sharing-anchors-service"></a>공유 앵커 서비스 배포

Visual Studio를 열고 `Sharing\SharingServiceSample` 폴더에서 프로젝트를 엽니다.

### <a name="configure-the-service-so-that-it-uses-your-cosmos-db"></a>Cosmos DB를 사용하도록 서비스 구성

**솔루션 탐색기**에서 `SharingService\Startup.cs`를 엽니다.

파일의 맨 위에 있는 `#define INMEMORY_DEMO` 줄을 찾아 주석 처리합니다. 파일을 저장합니다.

**솔루션 탐색기**에서 `SharingService\appsettings.json`을 엽니다.

`StorageConnectionString` 속성을 찾아 [데이터베이스 계정 만들기 단계](#create-a-database-account)에서 기록한 `Connection String` 값을 설정합니다. 파일을 저장합니다.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB를 사용하여 디바이스 간에 앵커 식별자를 공유했습니다. Azure Spatial Anchors 라이브러리에 대한 자세한 내용을 알아보려면 앵커를 만들고 찾는 방법에 대한 가이드를 계속 참조하세요.

> [!div class="nextstepaction"]
> [Azure Spatial Anchors를 사용하여 앵커 만들기 및 찾기](../create-locate-anchors-overview.md)
