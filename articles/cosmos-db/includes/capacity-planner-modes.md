---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/21/2021
ms.author: sngun
ms.openlocfilehash: cebf848f673b9e2651e6598d64aefff0cd371553
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064738"
---
## <a name="capacity-planner-modes"></a>Capacity planner 모드


|**모드**  |**설명**  |
|---------|---------|
|Basic|빠르고 높은 수준의 RU/s와 추정 비용을 제공합니다. 이 모드에서는 인덱싱 정책, 일관성 및 기타 매개 변수의 기본 Azure Cosmos DB 설정을 가정합니다. <br/><br/>Azure Cosmos DB에서 실행할 잠재적 워크로드를 평가하는 경우 빠르고 높은 수준의 추정이 가능한 기본 모드를 사용합니다. 자세한 내용은 [기본 모드를 사용하여 비용을 추정](#basic-mode)하는 방법을 참조하세요.|
|고급|인덱싱 정책, 일관성 수준 및 비용과 처리량에 영향을 미치는 기타 매개 변수와 같은 추가 설정을 조정할 수 있는 기능과 함께 더욱 자세한 RU/s 및 추정 비용을 제공합니다. <br/><br/>새 프로젝트의 RU/s를 추정하거나 더욱 자세한 추정을 원하면 고급 모드를 사용합니다. 자세한 내용은 [고급 모드를 사용하여 비용을 추정](#advanced-mode)하는 방법을 참조하세요.|
