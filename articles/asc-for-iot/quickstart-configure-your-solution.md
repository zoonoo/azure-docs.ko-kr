---
title: IoT용 Azure Security Center 솔루션 구성 - 미리 보기 | Microsoft Docs
description: IoT용 Azure Security Center를 사용하여 엔드투엔드 IoT 솔루션을 구성하는 방법을 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 64a04861b14e48eaa14a369546dd9d54bf59f019
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58861924"
---
# <a name="quickstart-configure-your-iot-solution"></a>빠른 시작: IoT 솔루션 구성

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 IoT용 ASC를 사용하여 IoT 보안 솔루션의 초기 구성을 수행하는 방법에 대해 설명합니다. 

## <a name="azure-security-center-asc-for-iot"></a>IoT용 Azure Security Center(ASC)

IoT용 ASC는 Azure 기반 IoT 솔루션에 포괄적인 엔드투엔드 보안 기능을 제공합니다.

IoT용 ASC를 사용하면 하나의 대시보드에서 전체 IoT 솔루션을 모니터링하여 Azure의 IoT 디바이스, IoT 플랫폼 및 백 엔드 리소스를 모두 검색할 수 있습니다.

IoT용 ASC는 IoT Hub에서 사용하도록 설정되면 IoT Hub에 연결되어 있고 IoT 솔루션과 관련된 다른 Azure 서비스를 자동으로 식별합니다.

자동 관계 검색 외에도 IoT 솔루션의 일부로 태그를 지정할 다른 Azure 리소스를 찾아서 선택할 수도 있습니다.
선택하는 경우 전체 구독, 리소스 그룹 또는 단일 리소스를 추가할 수 있습니다.

모든 리소스 관계가 정의되면 IoT용 ASC에서 Azure Security Center를 활용하여 이러한 리소스에 대한 보안 추천 사항과 경고를 제공합니다.

## <a name="add-azure-resources-to-your-iot-solution"></a>IoT 솔루션에 Azure 리소스 추가

새 리소스를 IoT 솔루션에 추가하려면 다음을 수행합니다. 

1. Azure Portal에서 **IoT Hub**를 엽니다. 
2. 왼쪽 메뉴의 **보안** 아래에서 **리소스**를 선택하고 엽니다. 
3. **리소스 추가**를 선택합니다.
4. IoT 솔루션에 속한 리소스를 선택합니다.
5. **추가**를 클릭합니다. 

축하합니다! 새 리소스를 IoT 솔루션에 추가했습니다.

이제 IoT용 ASC에서 새로 추가된 리소스를 모니터링하고 IoT 솔루션의 일부로 관련 보안 추천 사항 및 경고를 표시합니다.

## <a name="next-steps"></a>다음 단계

보안 모듈을 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [보안 모듈 만들기](quickstart-create-security-twin.md)