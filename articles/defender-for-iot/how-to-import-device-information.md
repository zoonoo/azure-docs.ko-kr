---
title: 디바이스 정보 가져오기
description: IoT 센서에 대 한 Defender는 미러된 트래픽을 모니터링 하 고 분석 합니다. 이러한 경우에는 이미 검색 된 장치에 대 한 정보를 보강 하기 위해 데이터를 가져올 수 있습니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 333ffbf4107dfd005ba7e7fae6a079a618e0c645
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509438"
---
# <a name="import-device-information-to-a-sensor"></a>장치 정보를 센서로 가져오기

IoT 센서에 대 한 Azure Defender는 미러된 트래픽을 모니터링 하 고 분석 합니다. 일부 경우에는 조직별 네트워크 구성 정책 때문에 일부 정보가 전송 되지 않을 수 있습니다.

이러한 경우 이미 검색 된 장치에 대 한 정보를 보강 하기 위해 데이터를 가져올 수 있습니다. 센서에 정보를 가져오는 데 사용할 수 있는 두 가지 옵션은 다음과 같습니다.

- **맵에서 가져오기**: 장치 이름, 유형, 그룹 또는 Purdue 계층을 맵으로 업데이트 합니다.

- 가져오기 **설정에서 가져오기**: 장치 OS, IP 주소, 패치 수준 또는 권한 부여 상태를 가져옵니다.

## <a name="import-from-the-map"></a>맵에서 가져오기

이 섹션에서는 장치 이름, 유형, 그룹 또는 Purdue 계층을 장치 맵으로 가져오는 방법을 설명 합니다. 맵에서이 작업을 수행 합니다.

가져오기 요구 사항은 다음과 같습니다.

- **이름**: 최대 30 자까지 지정할 수 있습니다.

- **유형** 또는 **Purdue 계층**: **장치 속성** 대화 상자에 표시 되는 옵션을 사용 합니다. (장치를 마우스 오른쪽 단추로 클릭 하 고 **속성 보기** 를 선택 합니다.)

- **장치 그룹**: 최대 30 자까지 새 그룹을 만듭니다. 

> [!NOTE]
> 충돌을 방지 하려면 한 센서에서 다른 센서로 내보낸 데이터를 가져오지 마세요.

가져오기:

1. 측면 메뉴에서 **장치** 를 선택 합니다.

2. **장치** 창의 오른쪽 위 모퉁이에서을 선택 :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: 합니다.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="장치 창의 스크린샷":::

3. **장치 내보내기** 를 선택 합니다. 내보낸 파일에는 광범위 한 정보가 표시 됩니다. 이 정보에는 장치에서 사용 하는 프로토콜과 장치 권한 부여 상태가 포함 됩니다.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="내보낸 파일의 정보입니다.":::

4. CSV 파일에서 장치 이름, 유형, 그룹 및 Purdue 레이어만 변경 합니다. 그런 다음, 파일을 저장합니다. 

   내보낸 파일에 표시 된 대문자 표준을 사용 합니다. 예를 들어 Purdue 계층의 경우 모든 첫 글자 대문자 표기를 사용 합니다.

5. **장치** 창의 **가져오기/내보내기** 드롭다운 메뉴에서 **장치 가져오기** 를 선택 합니다.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="장치 창을 통해 장치를 가져옵니다.":::

6. **장치 가져오기** 를 선택 하 고 가져올 CSV 파일을 선택 합니다. 가져오기 상태 메시지는 **장치 가져오기** 대화 상자를 닫을 때까지 화면에 표시 됩니다.

## <a name="import-from-import-settings"></a>가져오기 설정에서 가져오기

이 섹션에서는 장치 IP 주소, OS, 패치 수준 또는 권한 부여 상태를 장치 맵으로 가져오는 방법을 설명 합니다. **설정 가져오기** 대화 상자에서이 작업을 수행 합니다.

IP 주소, OS 및 패치 수준을 가져오려면:

1. [도움말 센터](https://cyberx-labs.zendesk.com/hc/en-us) 에서 [devices_info_2.2.8 및 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 파일을 다운로드 하 고 다음과 같이 정보를 입력 합니다.

   - **Ip 주소**: 장치 ip 주소를 입력 합니다.

   - **운영** 체제: 드롭다운 목록에서 선택 합니다.

   - **마지막 업데이트**: Yyyy-mm-dd 형식을 사용 합니다.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="옵션 화면입니다.":::

2. 측면 메뉴에서 **설정 가져오기** 를 선택 합니다.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="설정을 가져옵니다.":::

3. 필요한 구성을 업로드 하려면 **장치 정보** 섹션에서 사용자가 준비한 CSV 파일 **추가** 및 업로드를 선택 합니다.

권한 부여 상태를 가져오려면:

1. IoT 용 Defender 도움말 센터에서 [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 파일을 다운로드 하 여 저장 합니다. 파일을 CSV로 저장 했는지 확인 합니다.

2. 다음과 같이 정보를 입력 합니다.

   - **Ip 주소**: 장치 ip 주소입니다.

   - **이름**: 권한이 부여 된 장치 이름입니다. 이름이 정확한 지 확인 합니다. 가져온 목록의 장치에 지정 된 이름은 장치 맵에 표시 되는 이름을 덮어씁니다.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="가져온 장치 목록이 포함 된 Excel 파일입니다.":::

3. 측면 메뉴에서 **설정 가져오기** 를 선택 합니다.

4. **권한 있는 장치** 섹션에서 **추가** 를 선택 하 고 저장 한 CSV 파일을 업로드 합니다.

정보를 가져오면이 목록에 표시 되지 않은 모든 장치에 대 한 권한이 없는 장치에 대 한 경고가 표시 됩니다.

## <a name="import-device-information-to-the-sensor"></a>장치 정보를 센서로 가져오기

센서가 미러된 트래픽을 모니터링 하 고 분석 합니다. 일부 경우에는 조직별 네트워크 구성 정책 때문에 일부 정보가 전송 되지 않을 수 있습니다.

이러한 경우 이미 검색 된 장치에서 장치 정보를 보강 하기 위해 데이터를 가져올 수 있습니다. 센서에 정보를 가져오는 데 사용할 수 있는 두 가지 옵션은 다음과 같습니다.

- **맵에서 가져오기**: 장치 이름, 유형, 그룹 또는 Purdue 계층을 맵으로 업데이트 합니다.

- 가져오기 **설정에서 가져오기**: 장치 OS, IP 주소, 패치 수준 또는 권한 부여 상태를 가져옵니다.

### <a name="import-from-the-map"></a>맵에서 가져오기

이 섹션에서는 장치 이름, 유형, 그룹 또는 Purdue 계층을 장치 맵으로 가져오는 방법을 설명 합니다. 맵에서이 작업을 수행 합니다.

가져오기 요구 사항은 다음과 같습니다.

- **이름**: 최대 30 자까지 지정할 수 있습니다.

- **유형** 또는 **Purdue 계층**: **장치 속성** 대화 상자에 표시 되는 옵션을 사용 합니다. (장치를 마우스 오른쪽 단추로 클릭 하 고 **속성 보기** 를 선택 합니다.)

- **장치 그룹**: 최대 30 자까지 새 그룹을 만듭니다. 

> [!NOTE]
> 충돌을 방지 하려면 한 센서에서 다른 센서로 내보낸 데이터를 가져오지 마세요.

가져오기:

1. 측면 메뉴에서 **장치** 를 선택 합니다.

2. **장치** 창의 오른쪽 위 모퉁이에서을 선택 :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: 합니다.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="장치를 선택 하는 창입니다.":::

3. **장치 내보내기** 를 선택 합니다. 내보낸 파일에는 광범위 한 정보가 표시 됩니다. 예를 들어 장치에서 사용 하는 프로토콜과 장치 권한 부여 상태가 있습니다.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="내보낸 파일의 정보입니다.":::

4. CSV 파일에서 장치 이름, 유형, 그룹 및 Purdue 계층만 변경 합니다. 그런 다음, 파일을 저장합니다. 

   내보낸 파일에 표시 된 대문자 표준을 사용 합니다. 예를 들어 Purdue 계층의 경우 모든 첫 글자 대문자 표기를 사용 합니다.

5. **장치** 창의 **가져오기/내보내기** 드롭다운 메뉴에서 **장치 가져오기** 를 선택 합니다.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="장치를 가져옵니다.":::

6. **장치 가져오기** 를 선택 하 고 가져올 CSV 파일을 선택 합니다. 가져오기 상태 메시지는 **장치 가져오기** 대화 상자를 닫을 때까지 화면에 표시 됩니다.

### <a name="import-from-import-settings"></a>가져오기 설정에서 가져오기 

이 섹션에서는 장치 IP 주소, OS, 패치 수준 또는 권한 부여 상태를 장치 맵으로 가져오는 방법을 설명 합니다. **설정 가져오기** 대화 상자에서이 작업을 수행 합니다.

IP 주소, OS 및 패치 수준을 가져오려면:

1. [도움말 센터](https://cyberx-labs.zendesk.com/hc/en-us) 에서 [devices_info_2.2.8 및 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 파일을 다운로드 하 고 다음과 같이 정보를 입력 합니다.

   - **Ip 주소**: 장치 ip 주소입니다.

   - **운영** 체제: 드롭다운 목록에서 선택 합니다.

   - **마지막 업데이트 날짜**: Yyyy-mm-dd 형식을 사용 합니다.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="화면의 콘텐츠입니다.":::

2. 측면 메뉴에서 **설정 가져오기** 를 선택 합니다.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="가져오기 설정 화면을 채웁니다.":::

3. 필요한 구성을 업로드 하려면 **장치 정보** 섹션에서 **추가** 를 선택 하 고 준비한 CSV 파일을 업로드 합니다.

권한 부여 상태를 가져오려면:

1. IoT 용 Defender 도움말 센터에서 [authorized_devices examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 파일을 다운로드 하 여 저장 합니다. 파일을 CSV로 저장 했는지 확인 합니다.

2. 다음과 같이 정보를 입력 합니다.

   - **Ip 주소**: 장치 ip 주소입니다.

   - **이름**: 권한이 부여 된 장치 이름입니다. 이름이 정확한 지 확인 합니다. 가져온 목록의 장치에 지정 된 이름은 장치 맵에 표시 되는 이름을 덮어씁니다.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="장치 맵에 대 한 가져오기 목록입니다.":::

3. 측면 메뉴에서 **설정 가져오기** 를 선택 합니다.

4. **권한 있는 장치** 섹션에서 **추가** 를 선택 하 고 저장 한 CSV 파일을 업로드 합니다.

정보를 가져오면이 목록에 표시 되지 않은 모든 장치에 대 한 권한이 없는 장치에 대 한 경고가 표시 됩니다.

## <a name="see-also"></a>참고 항목

[모니터링된 트래픽 제어](how-to-control-what-traffic-is-monitored.md)

[디바이스 인벤토리에서 센서 감지 조사](how-to-investigate-sensor-detections-in-a-device-inventory.md)
