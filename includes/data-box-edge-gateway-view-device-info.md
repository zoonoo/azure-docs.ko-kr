---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 94c132421f1e113b667341b094acad8047e5f465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82561766"
---
1. [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. 를 사용 `Get-HcsApplianceInfo` 하 여 장치에 대 한 정보를 가져옵니다.

    다음 예에서는이 cmdlet을 사용 하는 방법을 보여 줍니다.

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

    몇 가지 중요 한 장치 정보를 요약 하는 표는 다음과 같습니다.
    
    | 매개 변수                             | 설명                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | 장치를 배포 하는 동안 로컬 웹 UI를 통해 구성 된 장치의 이름입니다. 기본 친숙한 이름은 디바이스 일련 번호입니다.  |   |
    | SerialNumber                   | 장치 일련 번호는 팩터리에서 할당 된 고유 번호입니다.                                                                             |   |
    | 모델                          | Azure Stack Edge 또는 Data Box Gateway 장치에 대 한 모델입니다. 모델은 Azure Stack Edge 및 Data Box Gateway에 대 한 가상 모델입니다.                   |   |
    | FriendlySoftwareVersion        | 디바이스 소프트웨어 버전에 해당하는 친숙한 문자열입니다. Preview를 실행 하는 시스템의 경우 소프트웨어 버전은 1902 Data Box Edge 됩니다. |   |
    | HcsVersion                     | 사용자 디바이스에서 실행되는 HCS 소프트웨어 버전입니다. 예를 들어 Data Box Edge 1902에 해당 하는 HCS software 버전은 1.4.771.324입니다.            |   |
    | LocalCapacityInMb              | 장치의 총 로컬 용량 (Mbps)입니다.                                                                                                        |   |
    | IsRegistered 됨                   | 이 값은 장치가 서비스로 활성화 되어 있는지 여부를 나타냅니다.                                                                                         |   |


