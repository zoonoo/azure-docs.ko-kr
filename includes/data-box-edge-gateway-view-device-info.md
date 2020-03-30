---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67182663"
---
1. [PowerShell 인터페이스에 연결합니다.](#connect-to-the-powershell-interface)
2. 을 `Get-HcsApplianceInfo` 사용하여 장치에 대한 정보를 가져옵니다.

    다음 예제에서는 이 cmdlet의 사용을 보여 주며 있습니다.

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

    다음은 몇 가지 중요한 장치 정보를 요약한 표입니다.
    
    | 매개 변수                             | 설명                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | 장치 배포 중에 로컬 웹 UI를 통해 구성된 장치의 친숙한 이름입니다. 기본 친숙한 이름은 디바이스 일련 번호입니다.  |   |
    | SerialNumber                   | 장치 일련 번호는 공장에서 할당된 고유 번호입니다.                                                                             |   |
    | 모델                          | 데이터 상자 가장자리 또는 데이터 상자 게이트웨이 장치의 모델입니다. 모델은 데이터 상자 게이트웨이에 대한 가상이며 데이터 상자 가장자리에 대한 물리적입니다.                   |   |
    | FriendlySoftwareVersion        | 디바이스 소프트웨어 버전에 해당하는 친숙한 문자열입니다. 미리 보기를 실행하는 시스템의 경우 친숙한 소프트웨어 버전은 데이터 박스 에지 1902입니다. |   |
    | HcsVersion                     | 사용자 디바이스에서 실행되는 HCS 소프트웨어 버전입니다. 예를 들어 데이터 박스 에지 1902에 해당하는 HCS 소프트웨어 버전은 1.4.771.324입니다.            |   |
    | 로컬 용량InMb              | 메가 비트에서 장치의 총 로컬 용량입니다.                                                                                                        |   |
    | 등록되었습니다.                   | 이 값은 서비스가 활성화되어 있는 경우를 나타냅니다.                                                                                         |   |


