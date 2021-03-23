---
title: IoT 용 Defender 포털에서 센서 및 구독 등록 및 관리
description: IoT 포털 용 Defender에서 센서를 등록 하 고, 보고, 관리 하는 방법에 대해 알아봅니다.
ms.date: 2/18/2021
ms.topic: how-to
ms.openlocfilehash: 5c529a5679e8b3d4879b003fa3d168e911f9b518
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781826"
---
# <a name="onboard-and-manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>IoT 용 Defender 포털에서 센서 및 구독 등록 및 관리

이 문서에서는 [IoT 포털 용 Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)에서 센서를 등록, 확인 및 관리 하는 방법을 설명 합니다.

## <a name="onboard-sensors"></a>온보드 센서

IoT 용 Azure Defender에 등록 하 고 센서 활성화 파일을 다운로드 하 여 센서를 등록 합니다.

### <a name="register-the-sensor"></a>센서 등록

등록하려면 다음을 수행합니다.

1. [IoT 용 Defender 포털](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)의 **시작** 페이지로 이동 합니다.
1. **온보드 센서** 를 선택 합니다.
1. 센서 이름을 만듭니다. 이름의 일부로 설치한 센서의 IP 주소를 포함 하거나 쉽게 식별할 수 있는 이름을 사용 하는 것이 좋습니다. 이렇게 하면 Azure [Defender For IoT 포털](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 의 등록 이름과 센서 콘솔에 표시 되는 배포 된 센서의 IP 간에 더 쉽게 추적 하 고 일관 된 이름을 지정할 수 있습니다.
1. 센서를 Azure 구독과 연결 합니다.
1. **클라우드 연결** 설정/해제를 사용 하 여 센서 관리 모드를 선택 합니다. 설정/해제가 설정 되어 있으면 센서가 클라우드로 연결 된 것입니다. 토글이 해제 된 경우에는 센서가 로컬로 관리 됩니다.

   - **클라우드 연결 센서**: 센서가 검색 하는 정보가 센서 콘솔에 표시 됩니다. 경고 정보는 IoT hub를 통해 전달 되며 Azure 센티널와 같은 다른 Azure 서비스와 공유할 수 있습니다.

   - **로컬로 관리 되는 센서**: 센서 검색 정보가 센서 콘솔에 표시 됩니다. Gapped 네트워크에서 작업 중이 고 로컬로 관리 되는 여러 센서에서 검색 된 모든 정보에 대 한 통합 보기를 사용 하는 경우 온-프레미스 관리 콘솔을 사용 합니다.

   클라우드 연결 센서의 경우 온 보 딩 중에 정의 된 이름은 센서 콘솔에 표시 되는 이름입니다. 콘솔에서 직접이 이름을 변경할 수 없습니다. 로컬로 관리 되는 센서의 경우 온 보 딩 중에 적용 되는 이름은 Azure에 저장 되며 센서 콘솔에서 업데이트할 수 있습니다.

1. Iot hub를 선택 하 여이 센서와 IoT 용 Azure Defender 간의 게이트웨이 역할을 합니다.
1. 센서가 클라우드로 연결 된 경우 IoT hub와 연결 하 고 사이트 이름 및 영역을 정의 합니다. 설명 태그를 추가할 수도 있습니다. 사이트 이름, 영역 및 태그는 사이트 [및 센서 페이지](#view-onboarded-sensors)의 설명 항목입니다.

### <a name="download-the-sensor-activation-file"></a>센서 활성화 파일 다운로드

센서 활성화 파일에는 센서의 관리 모드에 대 한 지침이 포함 되어 있습니다. 배포 하는 각 센서에 대해 고유한 정품 인증 파일을 다운로드 합니다. 처음으로 센서 콘솔에 로그인 한 사용자는 활성화 파일을 센서에 업로드 합니다.

정품 인증 파일을 다운로드 하려면:

1. **온보드 센서** 페이지에서 **활성화 파일 다운로드** 를 선택 합니다.
1. 처음으로 센서 콘솔에 로그인 한 사용자가 파일에 액세스할 수 있도록 설정 합니다.

## <a name="view-onboarded-sensors"></a>등록 센서 보기

[IoT 용 Defender 포털](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)에서 등록 센서에 대 한 기본 정보를 볼 수 있습니다.

1. **사이트 및 센서** 를 선택 합니다.
1. 필터 및 검색 도구를 사용 하 여 필요한 센서 및 위협 인텔리전스 정보를 찾을 수 있습니다.

- 등록 된 센서 수
- 클라우드로 연결 되 고 로컬로 관리 되는 센서의 수
- 등록 센서와 연결 된 허브입니다.
- 등록 하는 동안 센서에 할당 된 이름, 센서와 연결 된 영역 또는 태그가 추가 된 기타 설명 정보 등 센서에 대 한 세부 정보가 추가 됨

## <a name="manage-onboarded-sensors"></a>등록 센서 관리

센서와 관련 된 관리 작업에는 [IoT 용 Defender 포털](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) 을 사용 합니다.

등록 센서는 **사이트 및 센서** 페이지에서 볼 수 있습니다. 이 페이지에서 센서 정보를 편집할 수도 있습니다.

### <a name="export-sensor-details"></a>센서 세부 정보 내보내기

등록 센서 정보를 내보내려면 **사이트 및 센서** 페이지 맨 위에 있는 **내보내기** 아이콘을 선택 합니다.

### <a name="edit-sensor-zone-details"></a>센서 영역 정보 편집

**사이트 및 센서** 편집 옵션을 사용 하 여 센서 이름 및 영역을 편집할 수 있습니다.

편집 하려면:

1. 편집 하려는 센서에 대 한 **줄임표** (**...**)를 선택 합니다.
1. **편집** 을 선택합니다.
1. 센서 영역을 업데이트 하거나 새 영역을 만듭니다.

### <a name="delete-a-sensor"></a>센서 삭제

클라우드 연결 센서를 삭제 하면 정보가 IoT hub로 전송 되지 않습니다. 더 이상 사용 하지 않는 경우 로컬에서 연결 된 센서를 삭제 합니다.

센서를 삭제 하려면:

1. 삭제 하려는 센서에 대 한 줄임표 (**...**)를 선택 합니다.
1. 삭제를 확인합니다.

### <a name="reactivate-a-sensor"></a>센서 다시 활성화 

다음을 수행 하려는 경우 센서를 다시 활성화 해야 할 수 있습니다.

- **로컬로 관리 되는 모드가 아니라 클라우드 연결 모드에서 작업**: 다시 활성화 한 후 센서 검색은 센서에 표시 되 고 새로 검색 된 경고 정보는 IoT hub를 통해 전달 됩니다. 이 정보는 Azure 센티널과 같은 다른 Azure 서비스와 공유할 수 있습니다.

- **클라우드 연결 모드 대신 로컬로 관리 되는 모드에서 작업**: 다시 활성화 한 후 센서 검색 정보가 센서에만 표시 됩니다.

- **새 IoT hub에 센서 연결**: 이렇게 하려면 새 허브로 센서를 다시 등록 한 다음 새 활성화 파일을 다운로드 합니다.

센서를 다시 활성화 하려면:

1. [IoT 용 Defender 포털](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)에서 **사이트 및 센서** 페이지로 이동 합니다.

2. 새 정품 인증 파일을 업로드 하려는 센서를 선택 합니다.

3. 센서를 삭제 합니다.

4. 시작 페이지에서 **센서** 등록을 선택 하 여 새 모드 또는 새 IoT hub에서 센서를 다시 등록 합니다.

5. 활성화 파일을 다운로드 합니다.

1. IoT 센서 콘솔용 Defender에 로그인 합니다.

7. 센서 콘솔에서 **시스템 설정** 을 선택 하 고 다시 **활성화** 를 선택 합니다.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="활성화 파일을 업로드 하 여 센서를 다시 활성화 합니다.":::

8. **업로드** 를 선택 하 고 온보드 센서 페이지에서 저장 한 파일을 선택 합니다.

9. **활성화** 를 선택합니다.

## <a name="offboard-a-subscription"></a>구독 보드 오프

구독은 월 단위로 관리 됩니다. 언제 든 지 구독을 보드 아웃 하면 해당 구독에 대해 해당 월이 종료 될 때까지 요금이 청구 됩니다. 

구독을 오프 보 딩 하기 전에 구독과 연결 된 모든 센서를 제거 합니다. 센서를 삭제 하는 방법에 대 한 자세한 내용은 [센서 삭제](#delete-a-sensor)를 참조 하세요. 

구독을 오프 보드로 만들려면:

1. **가격 책정** 페이지로 이동 합니다.
1. 구독을 선택한 다음 **삭제** 아이콘을 선택 합니다 :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. 확인 팝업에서 확인란을 선택 하 여 구독과 연결 된 모든 센서를 삭제 했는지 확인 합니다.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="확인란을 선택 하 고 등록 취소를 선택 하 여 센서를 보드 오프 합니다.":::

1. **Offboard** 단추를 선택 합니다. 

온-프레미스 환경에는 영향을 주지 않지만 온-프레미스 환경에서 센서를 제거 하거나 다른 구독에 센서를 재할당 하 여 관련 데이터가 온-프레미스 관리 콘솔로 전달 되지 않도록 해야 합니다. 

## <a name="see-also"></a>참고 항목

[센서 활성화 및 설정](how-to-activate-and-set-up-your-sensor.md)
