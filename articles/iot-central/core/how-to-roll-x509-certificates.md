---
title: Azure IoT Central에서 x.509 인증서 롤
description: IoT Central 응용 프로그램을 사용 하 여 x.509 인증서를 롤백하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a9e35c7d4d64279c65971dd512bcd2107dad6594
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000070"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>IoT Central 응용 프로그램에서 x.509 장치 인증서를 롤백하는 방법

IoT 솔루션의 수명 주기 동안 인증서를 배포해야 합니다. 인증서를 배포하는 주된 이유 두 가지는 보안 위반 및 인증서 만료입니다.

보안 위반이 발생 하는 경우 시스템을 보호 하는 데 도움이 되는 보안 모범 사례는 인증서를 롤링 하는 것입니다. [보안 위험 가정 방법론](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)의 일환으로 Microsoft는 예방 조치와 함께 사후 보안 프로세스의 필요성을 강조하고 있습니다. 디바이스 인증서 배포는 이러한 보안 프로세스의 일환으로 포함되어야 합니다. 인증서의 배포 빈도는 솔루션의 보안 요구 사항에 따라 달라집니다. 중요도 높은 데이터가 포함된 솔루션을 사용하는 고객은 매일 인증서를 배포하기도 하며 어떤 고객은 2년마다 인증서를 배포할 수도 있습니다.


## <a name="obtain-new-x509-certificates"></a>새 x.509 인증서 가져오기

OpenSSL와 같은 도구를 사용 하 여 고유한 x.509 인증서를 만들 수 있습니다. 이 방법은 X.509 인증서 테스트에 적합하지만 보안과 관련한 보증이 거의 없습니다. 사용자 고유의 CA 공급자로 작업을 준비 하지 않은 경우에만 테스트에이 방법을 사용 합니다.

## <a name="enrollment-groups-and-security-breaches"></a>등록 그룹 및 보안 위반

보안 위반에 대 한 응답으로 그룹 등록을 업데이트 하려면 현재 인증서를 즉시 업데이트 하는 다음 방법을 사용 해야 합니다.

1. 왼쪽 창에서 **관리**  로 이동 하 여 **장치 연결**을 선택 합니다.

2. **등록 그룹**을 선택 하 고 목록에서 그룹 이름을 선택 합니다.

3. 인증서 업데이트의 경우 **기본 관리** 또는 **보조 관리**를 선택 합니다.

4. 등록 그룹에서 루트 x.509 인증서를 추가 하 고 확인 합니다.

   두 인증서 모두 손상 된 경우 기본 및 보조 인증서에 대해 이러한 단계를 완료 합니다.

## <a name="enrollment-groups-and-certificate-expiration"></a>등록 그룹 및 인증서 만료

인증서 만료를 처리 하는 인증서를 롤링 하는 경우 다음 방법을 사용 하 여 현재 인증서를 즉시 업데이트 합니다.

1. 왼쪽 창에서 **관리**  로 이동 하 여 **장치 연결**을 선택 합니다.

2. **등록 그룹**을 선택 하 고 목록에서 그룹 이름을 선택 합니다.

3. 인증서 업데이트의 경우 **기본 관리**를 선택 합니다.

4. 등록 그룹에서 루트 x.509 인증서를 추가 하 고 확인 합니다.

5. 나중에 보조 인증서가 만료 되 면 다시 돌아가서 해당 보조 인증서를 업데이트 합니다.

## <a name="individual-enrollments-and-security-breaches"></a>개별 등록 및 보안 위반

보안 위반에 대 한 응답으로 인증서를 롤링 하는 경우 다음 방법을 사용 하 여 현재 인증서를 즉시 업데이트 합니다.

1. **장치**를 선택 하 고 장치를 선택 합니다.

2. **연결**을 선택 하 고 **개별 등록** 으로 방법 연결을 선택 합니다.

3. 메커니즘으로 **인증서 (x.509)** 를 선택 합니다.

    ![개별 등록 관리](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 인증서 업데이트의 경우 폴더 아이콘을 선택 하 여 등록 항목에 대해 업로드할 새 인증서를 선택 합니다. **저장**을 선택합니다.

    두 인증서 모두 손상 된 경우 기본 및 보조 인증서에 대해이 단계를 완료 합니다.

## <a name="individual-enrollments-and-certificate-expiration"></a>개별 등록 및 인증서 만료

인증서 만료를 처리하기 위해 인증서를 배포하는 경우 다음과 같이 보조 인증서 구성을 사용하여 프로비전을 수행하는 디바이스에서 가동 중지 시간을 줄입니다.

보조 인증서가 만료 되 고 롤백해야 하는 경우 기본 구성을 사용 하 여로 회전할 수 있습니다. 기본 인증서와 보조 인증서를 이런 식으로 전환하면 프로비전을 수행하는 디바이스에서 가동 중지 시간이 줄어듭니다.

1. **장치**를 선택 하 고 장치를 선택 합니다.

2. **연결**을 선택 하 고 **개별 등록** 으로 방법 연결을 선택 합니다.

3. 메커니즘으로 **인증서 (x.509)** 를 선택 합니다.

    ![개별 등록 관리](./media/how-to-roll-x509-certificates/certificate-update.png)

4. 보조 인증서 업데이트의 경우 폴더 아이콘을 선택 하 여 등록 항목에 대해 업로드할 새 인증서를 선택 합니다. **저장**을 선택합니다.

5. 나중에 기본 인증서가 만료 되 면 다시 돌아가서 해당 기본 인증서를 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에서 x.509 인증서를 롤백하는 방법을 배웠으므로 [azure IoT Central에 연결할](concepts-get-connected.md)수 있습니다.


