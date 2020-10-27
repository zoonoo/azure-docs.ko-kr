---
title: '빠른 시작: 솔루션 구성'
description: 이 빠른 시작에서는 Azure Defender for IoT를 사용하여 엔드투엔드 IoT 솔루션을 구성하는 방법을 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: f949d52d4ba461da94153cbbd17ca5fb3eb214d7
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092089"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>빠른 시작: Azure Defender for IoT 솔루션 구성

이 문서에서는 Defender for IoT를 사용하여 IoT 보안 솔루션의 초기 구성을 수행하는 방법에 대해 설명합니다.

## <a name="what-is-defender-for-iot"></a>Defender for IoT란?

Defender for IoT는 Azure 기반 IoT 솔루션에 포괄적인 엔드투엔드 보안 기능을 제공합니다.

Defender for IoT를 사용하면 하나의 대시보드에서 전체 IoT 솔루션을 모니터링하여 Azure의 IoT 디바이스, IoT 플랫폼 및 백 엔드 리소스를 모두 검색할 수 있습니다.

Defender for IoT는 IoT Hub에서 사용하도록 설정되면 IoT Hub에 연결되고 IoT 솔루션과 관련된 다른 Azure 서비스를 자동으로 식별합니다.

자동 관계 검색 외에도 IoT 솔루션의 일부로 태그를 지정할 다른 Azure 리소스 그룹을 찾아서 선택할 수도 있습니다.

선택하는 경우 전체 구독, 리소스 그룹 또는 단일 리소스를 추가할 수 있습니다.

모든 리소스 관계가 정의되면 Defender for IoT는 Defender를 활용하여 이러한 리소스에 대한 보안 권장 사항과 경고를 제공합니다.

## <a name="add-azure-resources-to-your-iot-solution"></a>IoT 솔루션에 Azure 리소스 추가

새 리소스를 IoT 솔루션에 추가하려면 다음을 수행합니다.

1. Azure Portal에서 **IoT Hub** 를 엽니다.
1. **보안** 에서 **개요** 와 **설정** 을 차례로 선택한 다음, **모니터링된 리소스** 를 선택합니다.
1. **편집** 을 선택하고 IoT 솔루션에 속하는 모니터링된 리소스를 선택합니다.
1. **추가** 를 클릭합니다.

축하합니다! 새 리소스 그룹을 IoT 솔루션에 추가했습니다.

이제 Defender for IoT에서 새로 추가된 리소스 그룹을 모니터링하고, IoT 솔루션의 일부로 관련 보안 권장 사항 및 경고를 표시합니다.

## <a name="next-steps"></a>다음 단계

보안 모듈을 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [보안 모듈 만들기](quickstart-create-security-twin.md)
