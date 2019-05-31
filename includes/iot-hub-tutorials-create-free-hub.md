---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a7a86c6a2661a8a1f30491391fc76f4dc5d71f54
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66163713"
---
Azure Portal을 사용하여 IoT Hub를 만들려면

1. [Azure Portal](http://portal.azure.com)에 로그인합니다.

1. **리소스 만들기** > **사물 인터넷** > **IoT Hub**를 선택합니다.

    ![IoT Hub를 설치하도록 선택](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. 체험 계층 IoT Hub를 만들려면 다음 표의 값을 사용합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 구독 | 드롭다운에서 Azure 구독을 선택합니다. |
    | 리소스 그룹 | 새로 만듭니다. 이 자습서에서는 **tutorials-iot-hub-rg**라는 이름을 사용합니다. |
    | 지역 | 이 자습서에서는 **미국 서부**를 사용합니다. 자신에게 가장 가까운 지역을 선택할 수 있습니다. |
    | Name | 다음 스크린샷에서는 **tutorials-iot-hub**라는 이름을 사용합니다. 허브를 만들 때 고유한 이름을 선택해야 합니다. |

    ![허브 설정 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | 설정 | 값 |
    | ------- | ----- |
    | 가격 책정 및 규모 계층 | F1 체험 계층입니다. 구독에는 하나의 체험 계층 허만 있을 수 있습니다. |
    | IoT Hub 단위 | 1 |

    ![허브 설정 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. **만들기**를 클릭합니다. 허브를 만드는 데 몇 분 정도 걸릴 수 있습니다.

    ![허브 설정 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. 선택한 IoT 허브 이름을 적어 둡니다. 이 값은 자습서 뒷부분에서 사용합니다.