---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 3312d1ec7c2535e103cf8959599c0d4c3014f520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86218342"
---
1. [PowerShell 인터페이스에 연결하세요](#connect-to-the-powershell-interface).
2. `Get-HcsApplianceInfo`를 사용하여 디바이스에 대한 정보를 가져옵니다.

    다음 예제에서는 이 cmdlet의 사용법을 보여줍니다.

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    몇 가지 중요한 디바이스 정보를 요약하는 표는 다음과 같습니다.

    | 매개 변수 | 설명 |
    |-----------|-------------|
    | FriendlyName                   | 디바이스를 배포하는 동안 로컬 웹 UI를 통해 구성된 디바이스의 친숙한 이름입니다. 기본 친숙한 이름은 디바이스 일련 번호입니다.  |
    | SerialNumber                   | 디바이스 일련 번호는 팩터리에서 할당된 고유 번호입니다.                                                                             |
    | 모델                          | Azure Stack Edge 또는 Data Box Gateway 디바이스에 대한 모델입니다. 이 모델은 Azure Stack Edge의 경우 실제 모델이고 Data Box Gateway의 경우 가상 모델입니다.                   |
    | FriendlySoftwareVersion        | 디바이스 소프트웨어 버전에 해당하는 친숙한 문자열입니다. 미리 보기를 실행하는 시스템의 경우 친숙한 소프트웨어 버전은 Data Box Edge 1902입니다. |
    | HcsVersion                     | 사용자 디바이스에서 실행되는 HCS 소프트웨어 버전입니다. 예를 들어 Data Box Edge 1902에 해당하는 HCS 소프트웨어 버전은 1.4.771.324입니다.            |
    | LocalCapacityInMb              | 디바이스의 총 로컬 용량(메가비트)입니다.                                                                                                        |
    | IsRegistered                   | 이 값은 디바이스가 서비스로 활성화되었는지 여부를 나타냅니다.                                                                                         |


