---
title: Azure Spatial Anchors 개요 | Microsoft Docs
description: Azure Spatial Anchors가 플랫폼 간 혼합 현실 환경을 개발하는 데 어떻게 도움이 되는지 알아봅니다.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2451922f0eb49a5ccee036db72eb046760287dca
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60240461"
---
# <a name="azure-spatial-anchors-overview"></a>Azure Spatial Anchors 개요

Azure Spatial Anchors를 시작합니다. Azure Spatial Anchors는 공간 인식 혼합 현실 애플리케이션을 빌드하는 데 꼭 필요한 기능을 개발자에게 제공합니다. 이러한 애플리케이션은 Microsoft HoloLens, ARKit 지원 iOS 기반 디바이스, ARCore 지원 Android 기반 디바이스를 지원할 수 있습니다. 개발자는 Azure Spatial Anchors를 통해 혼합 현실 플랫폼을 사용하여 공간을 인지하고, 정확한 관심 지점을 지정하며, 지원되는 디바이스에서 해당 관심 지점을 회수할 수 있습니다.
이러한 정확한 관심 지점을 Spatial Anchors라고 합니다.

![플랫폼 간](./media/cross-platform.png)

## <a name="examples"></a>예

Spatial Anchors가 설정된 몇 가지 예제 사용 사례는 다음과 같습니다.

- [다중 사용자 환경](tutorials/tutorial-share-anchors-across-devices.md). Spatial Anchors를 통해 동일한 장소에 있는 사람들은 다중 사용자 혼합 현실 애플리케이션에 쉽게 참여할 수 있습니다. 예를 들어, 두 명의 사람이 가상 체스 보드를 테이블에 배치하여 혼합 현실 체스 게임을 시작할 수 있습니다. 그런 다음, 테이블에서 디바이스를 가리켜서 가상 체스 보드를 보고 함께 상호 작용할 수 있습니다.

- [방법 찾기](concepts/anchor-relationships-way-finding.md). 개발자는 Spatial Anchors를 함께 연결하여 이들 간의 관계를 만들 수도 있습니다. 예를 들어, 앱에는 사용자가 작업을 완료하기 위해 반드시 상호 작용해야 하는 두 개 이상의 관심 지점이 있는 환경이 포함될 수 있습니다. 이러한 관심 지점은 연결된 방식으로 만들 수 있습니다. 나중에 사용자가 다단계 작업을 완료하면 앱은 현재 주변의 앵커를 요청하여 사용자를 작업의 다음 단계로 리디렉션할 수 있습니다.

- [실제에서 가상 콘텐츠 유지](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). 사용자는 앱을 통해 가상 달력을 회의실 벽에 배치할 수 있으며 사람들은 휴대폰 앱 또는 HoloLens 디바이스를 사용하여 이를 볼 수 있습니다. 산업용 설정에서는 사용자가 지원되는 디바이스 카메라로 대상을 가리켜서 머신에 대한 컨텍스트 정보를 받을 수 있습니다.

Azure Spatial Anchors는 지원되는 디바이스 플랫폼에 대한 관리 서비스 및 클라이언트 SDK로 구성됩니다. 다음 섹션에서는 Azure Spatial Anchors를 사용하여 앱 빌드를 시작하는 방법에 대한 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계

Spatial Anchors를 사용하여 첫 번째 앱을 만듭니다.

> [!div class="nextstepaction"]
> [Unity](unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)
