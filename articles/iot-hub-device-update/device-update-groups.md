---
title: Azure IoT Hub 장치 그룹에 대 한 장치 업데이트 이해 | Microsoft Docs
description: 장치 그룹을 사용 하는 방법을 이해 합니다.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679498"
---
# <a name="device-groups"></a>디바이스 그룹

장치 그룹은 장치 모음입니다. 장치 그룹은 여러 장치에 대 한 배포를 확장 하는 방법을 제공 합니다. 각 장치는 정확히 한 번에 하나의 장치 그룹에 속합니다.
장치를 구성 하는 여러 장치 그룹을 만들도록 선택할 수 있습니다. 예를 들어 Contoso는 테스트 실험에서 장치에 대 한 "Flighting" 장치 그룹을 사용 하 고, 해당 필드 팀이 운영 센터에서 사용 하는 장치에 대 한 "평가" 장치 그룹을 사용할 수 있습니다. 또한 Contoso는 지리적 지역에 따라 프로덕션 장치를 그룹화 하도록 선택할 수 있습니다. 그러면 지역 timezones에 맞는 일정에 따라 장치를 업데이트할 수 있습니다. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>장치 그룹을 만드는 데 장치 쌍 태그 사용

장치 쌍 태그는 사용자가 장치를 그룹화 할 수 있도록 합니다. 장치를 그룹화 하려면 장치 쌍에 ADUGroup 키와 값이 있어야 합니다.

### <a name="device-twin-tag-format"></a>장치 쌍 태그 형식

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>범주화 되지 않은 장치 그룹

범주화 되지 않음은 다음을 수행 하는 장치를 그룹화 하는 데 사용 되는 예약어입니다.
- ADUGroup 장치 쌍 태그는 없습니다.
- ADUGroup 장치 쌍 태그가 있지만 그룹은이 그룹 이름으로 만들어지지 않습니다.

예를 들어 장치 쌍 태그를 사용 하는 장치는 아래와 같습니다.

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

장치 및 해당 장치에 대해 만들 수 있는 가능한 그룹은 다음과 같습니다.

|디바이스 |그룹  |
|-----------|--------------|
|Device1    |Group1|
|Device2    |Group1|
|Device3    |Group2|
|장치 4    |범주화되지 않음|



## <a name="next-steps"></a>다음 단계

[디바이스 그룹 만들기](./create-update-group.md)
