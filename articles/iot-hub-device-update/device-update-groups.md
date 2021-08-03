---
title: Azure IoT Hub 디바이스 그룹에 대한 디바이스 업데이트 이해 | Microsoft Docs
description: 디바이스 그룹을 사용하는 방법을 이해합니다.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: aa3ee7e5b92044c35ac1856309f7265ad06923a1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110083533"
---
# <a name="device-groups"></a>디바이스 그룹

디바이스 그룹은 디바이스의 컬렉션을 말합니다. 디바이스 그룹은 여러 디바이스에 배포를 확장하는 방법을 제공합니다. 각 디바이스는 한 번에 정확히 하나의 디바이스 그룹에 속합니다.
디바이스를 구성하기 위해 여러 디바이스 그룹을 만들도록 선택할 수 있습니다. 예를 들어, Contoso는 검사 실험실에서 디바이스에 "Flighting" 디바이스 그룹을 사용하고, 해당 필드 팀이 운영 센터에서 사용하는 디바이스에 "평가" 디바이스 그룹을 사용할 수 있습니다. 또한 Contoso는 지리적 지역에 따라 프로덕션 디바이스를 그룹으로 묶도록 선택할 수 있습니다. 그러면 지역 시간대에 맞는 일정에 따라 디바이스를 업데이트할 수 있습니다. 


## <a name="using-device-or-module-twin-tag-for-device-group-creation"></a>디바이스 그룹을 만드는 데 디바이스 또는 모듈 쌍 태그 사용

태그를 사용하면 사용자가 디바이스를 그룹화할 수 있습니다. 디바이스를 그룹으로 묶으려면 디바이스 또는 모듈 쌍에 ADUGroup 키와 값이 있어야 합니다.

### <a name="device-or-module-twin-tag-format"></a>디바이스 또는 모듈 쌍 태그 형식

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>범주화되지 않음 디바이스 그룹

범주화되지 않음은 다음을 수행하는 디바이스를 그룹으로 묶는 데 사용되는 예약어입니다.
- ADUGroup 디바이스 또는 모듈 쌍 태그가 없습니다.
- ADUGroup 디바이스 또는 모듈 쌍 태그가 있지만, 그룹은 이 그룹 이름으로 만들어지지 않습니다.

예를 들어, 아래의 디바이스 쌍 태그를 사용하는 디바이스들이 여기에 해당합니다.

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

디바이스 및 해당 디바이스에 대해 만들 수 있는 가능한 그룹은 다음과 같습니다.

|디바이스 |그룹  |
|-----------|--------------|
|Device1    |Group1|
|Device2    |Group1|
|Device3    |Group2|
|Device4    |범주화되지 않음|



## <a name="next-steps"></a>다음 단계

[디바이스 그룹 만들기](./create-update-group.md)
