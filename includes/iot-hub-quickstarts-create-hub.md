---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7987ee8c9a127c7a41ec2cd1f726450c771d527f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34666969"
---
첫 번째 단계는 Azure Portal을 사용하여 구독에 IoT Hub를 만드는 것입니다. IoT Hub를 사용하면 다양한 장치에서 클라우드로 다량의 원격 분석을 수집할 수 있습니다. 그런 다음, 허브는 클라우드에서 실행 중인 하나 이상의 백 엔드 서비스가 원격 분석을 읽고 처리할 수 있게 합니다.

1. [Azure 포털](http://portal.azure.com)에 로그인합니다.

1. **리소스 만들기** > **사물 인터넷** > **IoT Hub**를 선택합니다.

    ![IoT Hub를 설치하도록 선택][1]

1. **IoT Hub** 창에서 IoT Hub에 다음 정보를 입력합니다.

   * **구독**: 이 IoT Hub를 만드는 데 사용할 구독을 선택합니다.

   * **리소스 그룹**: IoT Hub를 호스트할 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리][lnk-resource-groups]를 참조하세요.

   * **지역**: 가장 가까운 위치를 선택합니다.

   * **이름**: IoT Hub에 대한 이름을 만듭니다. 입력한 이름을 사용할 수 있으면 녹색 확인 표시가 나타납니다.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![IoT Hub 기본 사항 창][2]

2. **다음: 크기 및 규모**를 선택하여 IoT Hub를 계속 만듭니다. 

3. **가격 책정 및 규모 계층**을 선택합니다. 이 문서의 경우 구독에서 아직 사용할 수 있다면 **F1 - 무료** 계층을 선택합니다. 자세한 내용은 [가격 및 크기 계층][lnk-pricing]을 참조하세요.

   ![IoT Hub 크기 및 규모 창][3]

4. **검토 + 만들기**를 선택합니다.

1. IoT Hub 정보를 검토한 다음, **만들기**를 클릭합니다. IoT Hub를 만드는 데 몇 분 정도 걸릴 수 있습니다. **알림** 창에서 진행 상황을 모니터링할 수 있습니다.


<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md