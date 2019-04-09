---
title: 자습서 - 세션 및 디바이스 간에 Azure Spatial Anchors 공유 | Microsoft Docs
description: 이 자습서에서는 백 엔드 서비스가 있는 Unity의 Android/iOS 디바이스 간에 Azure Spatial Anchors 식별자를 공유하는 방법을 알아봅니다.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff9868dd7347812eb6ef566288ec364bc89b6955
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629317"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>자습서: 세션 및 디바이스에서 Azure Spatial Anchors 공유

이 자습서에서는 [Azure Spatial Anchors](../overview.md)를 사용하여 한 세션에서 앵커를 만든 다음, 동일한 디바이스나 다른 디바이스에서 이 세션을 찾는 방법을 알아봅니다. 동시에 같은 위치에 있는 여러 디바이스에서 동일한 앵커를 찾을 수도 있습니다.

![지속성](./media/persistence.gif)

Azure Spatial Anchors는 시간이 지남에 따라 디바이스에서 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 작업이 완료되면 둘 이상의 디바이스에 배포할 수 있는 앱이 제공됩니다. 한 인스턴스에서 만든 Azure Spatial Anchors는 다른 사용자와 공유할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 앵커 공유에 사용할 수 있는 Azure에 ASP.NET Core 웹앱을 배포하여 일정 기간 메모리에 저장합니다.
> * 빠른 시작의 Unity 샘플 내에 AzureSpatialAnchorsLocalSharedDemo 장면을 구성하여 공유 앵커 웹앱을 활용합니다.
> * 하나 이상의 디바이스에 배포하고 실행합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

이 자습서에서는 Unity와 Azure ASP.NET Core 웹앱을 사용하지만 다른 디바이스 간에 Azure Spatial Anchor 식별자를 공유하는 방법에 대한 예를 보여줄 뿐입니다. 다른 언어와 백 엔드 기술을 사용하여 동일한 목표를 달성할 수 있습니다. 또한 이 자습서에 사용되는 ASP.NET Core 웹앱은 .NET Core 2.2 SDK에 종속됩니다. 일반 Azure Web Apps(Windows용)에서는 정상적으로 실행되지만, 현재 Linux용 Azure Web Apps에서는 작동하지 않습니다.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Unity에서 샘플 프로젝트 열기

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>공유 앵커 서비스 배포

Visual Studio를 열고 `Sharing\SharingServiceSample` 폴더에서 프로젝트를 엽니다.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure에서 ASP.NET Core 웹앱을 배포한 다음, Unity 앱을 구성하고 배포했습니다. 앱으로 Spatial Anchors를 만들고 ASP.NET Core 웹앱을 사용하여 다른 디바이스와 공유했습니다.

Azure Cosmos DB를 사용하여 공유된 Spatial Anchors 식별자를 저장하도록 ASP.NET Core 웹앱을 개선하는 방법을 자세히 알아보려면 다음 자습서를 계속 진행하세요. Azure Cosmos DB는 ASP.NET Core 웹앱에 지속성을 제공합니다. 이렇게 하면 오늘 앱에서 앵커를 만들고 며칠 후 돌아와서 웹앱에 저장된 앵커 식별자를 사용하여 다시 찾을 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: Azure Cosmos DB를 사용하여 앵커 저장](./tutorial-use-cosmos-db-to-store-anchors.md)
