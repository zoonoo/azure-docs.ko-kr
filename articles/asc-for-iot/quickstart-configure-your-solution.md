---
title: '빠른 시작: IoT용 Azure Security Center 솔루션 구성'
description: 이 빠른 시작에서는 IoT용 Azure Security Center를 사용하여 엔드투엔드 IoT 솔루션을 구성하는 방법을 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: e670df359cc33c9eaca089d0ed8f9614ef8c0468
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "73904142"
---
# <a name="quickstart-configure-your-iot-solution"></a>빠른 시작: IoT 솔루션 구성

이 문서에서는 IoT용 Azure Security Center를 사용하여 IoT 보안 솔루션의 초기 구성을 수행하는 방법에 대해 설명합니다. 

## <a name="azure-security-center-for-iot"></a>IoT용 Azure Security Center

IoT용 Azure Security Center는 Azure 기반 IoT 솔루션에 포괄적인 엔드투엔드 보안을 제공합니다.

IoT용 Azure Security Center를 사용하면 하나의 대시보드에서 전체 IoT 솔루션을 모니터링하고, Azure의 IoT 디바이스, IoT 플랫폼 및 백 엔드 리소스를 모두 표시할 수 있습니다.

IoT Hub에서 사용하도록 설정되면 IoT용 Azure Security Center에서 IoT Hub에 연결되고 IoT 솔루션과 관련된 다른 Azure 서비스도 자동으로 식별합니다.

자동 관계 검색 외에도 IoT 솔루션의 일부로 태그를 지정할 다른 Azure 리소스 그룹을 찾아서 선택할 수도 있습니다. 

선택하는 경우 전체 구독, 리소스 그룹 또는 단일 리소스를 추가할 수 있습니다. 

모든 리소스 관계가 정의되면 IoT용 Azure Security Center에서 Azure Security Center를 활용하여 이러한 리소스에 대한 보안 추천 사항과 경고를 제공합니다.

## <a name="add-azure-resources-to-your-iot-solution"></a>IoT 솔루션에 Azure 리소스 추가

새 리소스를 IoT 솔루션에 추가하려면 다음을 수행합니다. 

1. Azure Portal에서 **IoT Hub**를 엽니다. 
1. 왼쪽 메뉴의 **보안** 아래에서 **리소스**를 선택하고 엽니다. 
1. **편집**을 선택하고, IoT 솔루션에 속한 리소스 그룹을 선택합니다.
1. **추가**를 클릭합니다. 

축하합니다! 새 리소스 그룹을 IoT 솔루션에 추가했습니다.

이제 IoT용 Azure Security Center에서 새로 추가된 리소스 그룹을 모니터링하고, IoT 솔루션의 일부로 관련 보안 추천 사항 및 경고를 표시합니다.

## <a name="next-steps"></a>다음 단계

보안 모듈을 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [보안 모듈 만들기](quickstart-create-security-twin.md)