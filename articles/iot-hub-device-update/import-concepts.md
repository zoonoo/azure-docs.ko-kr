---
title: IoT Hub 가져오기에 대 한 장치 업데이트 이해 | Microsoft Docs
description: IoT Hub에 대 한 장치 업데이트로 새 업데이트를 가져오기 위한 주요 개념입니다.
author: andbrown
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: c6c6707a74007898c3cd73250709f4df3fabb37d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663346"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>IoT Hub에 대 한 장치 업데이트로 업데이트 가져오기
IoT Hub에 대 한 장치 업데이트에서 장치에 대 한 업데이트를 배포 하려면 먼저 해당 업데이트를 장치 업데이트 서비스로 _가져와야_ 합니다. 업데이트를 가져오는 경우를 이해 하기 위한 몇 가지 중요 한 개념에 대 한 개요는 다음과 같습니다.

## <a name="import-manifest"></a>매니페스트 가져오기

가져오기 매니페스트는 가져오는 업데이트에 대 한 중요 한 정보를 정의 하는 JSON 파일입니다. 가져오기 매니페스트와 연결 된 업데이트 파일 (예: 펌웨어 업데이트 패키지)을 가져오기 프로세스의 일부로 모두 전송 합니다. 가져오기 매니페스트에 정의 된 메타 데이터는 업데이트를 수집 하는 데 사용 됩니다. 일부 메타 데이터는 배포 시에도 사용 됩니다. 예를 들어 업데이트가 제대로 설치 되었는지 확인 합니다.

가져오기 매니페스트에는 IoT Hub 개념에 대 한 중요 한 장치 업데이트를 나타내는 여러 항목이 포함 되어 있습니다. 이러한 개념은 아래에 설명 되어 있습니다.

### <a name="update-identity-update-id"></a>Id 업데이트 (업데이트 ID)

업데이트 id는 업데이트의 고유 식별자를 나타냅니다. 가져오는 업데이트에 대 한 중요 한 속성을 정의 합니다. 업데이트 id는 세 부분으로 구성 됩니다.
* 공급자: 업데이트를 만들거나 직접 담당 하는 엔터티입니다. 회사 이름이 될 수도 있습니다.
* 이름: 업데이트 클래스의 식별자입니다. 클래스는 사용자가 선택 하는 것이 될 수 있습니다. 일반적으로 장치 또는 모델 이름이 됩니다.
* 버전: 공급자와 이름이 같은 다른 사용자와이 업데이트를 구분 하는 버전 번호입니다. 이 버전은 IoT Hub 서비스에 대 한 장치 업데이트에서 사용 되며, 장치의 개별 소프트웨어 구성 요소 버전과 일치 하거나 일치 하지 않을 수 있습니다. 

### <a name="compatibility"></a>호환성

업데이트 배포를 간소화 하기 위해 IoT Hub에 대 한 장치 업데이트는 가져오기 매니페스트에 정의 된 업데이트의 호환성 속성과 해당 하는 장치 속성을 비교 합니다. 일치 하는 속성이 있는 업데이트만 반환 되 고 배포에 사용할 수 있습니다.

### <a name="installedcriteria"></a>InstalledCriteria

InstalledCriteria는 장치의 업데이트 에이전트가 업데이트를 성공적으로 설치 했는지 확인 하는 데 사용 됩니다.


## <a name="next-steps"></a>다음 단계

준비가 되 면 가져오기 프로세스를 단계별로 안내 하는 [가져오기 How-To 가이드](./import-update.md)를 사용해 보세요.


