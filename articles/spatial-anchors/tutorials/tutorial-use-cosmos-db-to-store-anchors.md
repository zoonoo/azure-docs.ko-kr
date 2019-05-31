---
title: 자습서 - Azure Cosmos DB 백 엔드를 사용하여 세션 및 디바이스에서 Azure Spatial Anchors 공유 | Microsoft Docs
description: 이 자습서에서는 백 엔드 서비스와 Azure Cosmos DB가 있는 Unity의 Android/iOS 디바이스 간에 Azure Spatial Anchors 식별자를 공유하는 방법을 알아봅니다.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 19c2298c9bda03acba28496a97c89c2a53e3c44e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964905"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>자습서: Azure Cosmos DB 백 엔드를 사용하여 세션 및 디바이스에서 Azure Spatial Anchors 공유

이 자습서에서는 한 세션에서 [Azure Spatial Anchors](../overview.md)를 사용하여 앵커를 만든 다음, 또 다른 세션의 동일한 디바이스 또는 다른 디바이스에서 앵커를 찾는 방법을 알아봅니다. 예를 들어 두 번째 세션은 다른 요일에 진행될 수 있습니다. 같은 위치에 있는 여러 디바이스에서 동시에 동일한 앵커를 찾을 수도 있습니다.

![개체 지속성을 보여주는 GIF](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md)는 시간이 지나도 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 작업이 완료되면 둘 이상의 디바이스에 배포할 수 있는 앱이 제공됩니다. 한 인스턴스에서 만든 공간 앵커는 Azure Cosmos DB를 사용하여 다른 디바이스와 식별자를 공유합니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 앵커 공유에 사용할 수 있는 ASP.NET Core 웹앱을 Azure에 배포하고, Azure Cosmos DB에 저장합니다.
> * Azure 빠른 시작의 Unity 샘플로 공유 앵커 웹앱을 활용하는 AzureSpatialAnchorsLocalSharedDemo 장면을 구성합니다.
> * 하나 이상의 디바이스에 앱을 배포하고 실행합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

이 자습서에서는 Unity와 Azure Cosmos DB를 사용하지만, 디바이스 간에 Spatial Anchor 식별자를 공유하는 방법을 보여드리는 것이 그 목적입니다. 다른 언어와 백 엔드 기술을 사용하여 동일한 목표를 달성할 수 있습니다. 또한 이 자습서에 사용되는 ASP.NET Core 웹앱은 .NET Core 2.2 SDK가 필요합니다. 현재 Windows용 Web Apps에서는 정상적으로 실행되지만, Linux용 Web Apps에서는 실행되지 않습니다.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

`Connection String`을 복사합니다. 이후에 필요합니다.

## <a name="open-the-sample-project-in-unity"></a>Unity에서 샘플 프로젝트 열기

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>공유 앵커 서비스 배포

Visual Studio를 열고, `Sharing\SharingServiceSample` 폴더의 프로젝트를 엽니다.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Azure Cosmos DB 데이터베이스를 사용하도록 서비스 구성

**솔루션 탐색기**에서 `SharingService\Startup.cs` 파일을 엽니다.

파일의 맨 위에서 `#define INMEMORY_DEMO` 줄을 찾아 주석 처리합니다. 파일을 저장합니다.

**솔루션 탐색기**에서 `SharingService\appsettings.json` 파일을 엽니다.

`StorageConnectionString` 속성을 찾은 다음, [데이터베이스 계정 만들기 단계](#create-a-database-account)에서 복사한 `Connection String` 값으로 설정합니다. 파일을 저장합니다.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB를 사용하여 디바이스 간에 앵커 식별자를 공유했습니다. 새 Unity HoloLens 앱에서 Azure Spatial Anchors를 사용하는 방법을 자세히 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [새 Android 앱 시작](./tutorial-new-unity-hololens-app.md)