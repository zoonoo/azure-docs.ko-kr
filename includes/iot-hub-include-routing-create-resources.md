---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 710bb8cba7fbbe4bc9b9fdc52b0767c96f97fe72
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182822"
---
## <a name="create-base-resources"></a>기본 리소스 만들기

메시지 라우팅을 구성하려면 먼저 IoT 허브, 스토리지 계정 및 Service Bus 큐를 만들어야 합니다. 이러한 리소스는 이 자습서의 1부에 제공되는 4가지 문서 중 하나를 사용하여 만들 수 있습니다(Azure CLI, Azure PowerShell, Azure Portal 또는 Azure Resource Manager 템플릿).

모든 리소스에 대해 동일한 리소스 그룹 및 위치를 사용합니다. 그런 다음, 마지막에 리소스 그룹을 삭제하여 모든 내용을 한 번에 삭제할 수 있습니다.

다음 섹션은 수행할 단계를 설명합니다.

1. [리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md)을 만듭니다.

2. S1 계층에서 IoT Hub를 만듭니다. IoT Hub에 소비자 그룹을 추가합니다. 소비자 그룹은 Azure Stream Analytics에서 데이터를 검색할 때 사용됩니다.

   > [!NOTE]
   > 이 자습서를 완료하려면 유료 계층의 IoT Hub를 사용해야 합니다. 체험 계층에서는 하나의 엔드포인트만 설정할 수 있으며, 이 자습서에는 여러 개의 엔드포인트가 필요합니다.
   > 

3. Standard_LRS 복제본을 사용하여 표준 V1 저장소 계정을 만듭니다.

4. Service Bus 네임스페이스 및 큐를 만듭니다.

5. 허브에 메시지를 보내는 시뮬레이션된 디바이스에 사용할 디바이스 ID를 만듭니다. 테스트 단계에 대한 키를 저장합니다. (Resource Manager 템플릿을 만드는 경우에는 템플릿을 배포한 후에 수행합니다.)