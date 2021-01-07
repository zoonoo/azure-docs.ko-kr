---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553212"
---
1. Data Box 디바이스에 로그인합니다. 잠겨 있지 않은지 확인합니다.

    ![스크린샷은 디바이스가 잠금 해제된 상태로 표시되는 대시보드를 보여줍니다.](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. **세트 네트워크 인터페이스** 로 이동합니다. 클라이언트에 연결하는 데 사용된 네트워크 인터페이스의 디바이스 IP 주소를 기록해 둡니다.

    ![스크린샷은 IP 주소를 볼 수 있는 네트워크 설정을 보여줍니다.](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. **연결 및 복사** 로 이동하고 **나머지** 를 클릭합니다.

    ![스크린샷은 액세스 설정으로 REST를 선택할 수 있는 연결 및 복사 창을 보여줍니다.](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. **스토리지 계정 액세스 및 데이터 업로드** 대화 상자에서 **Blob Service 엔드포인트** 를 복사합니다.

    ![스크린샷은 Blob Service 엔드포인트를 복사할 수 있는 스토리지 계정 액세스 및 데이터 업로드 대화 상자를 보여줍니다.](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. 관리자 권한으로 **메모장** 을 시작하고 `C:\Windows\System32\Drivers\etc`에 있는 **호스트** 파일을 엽니다.
6. 다음 항목을 **호스트** 파일에 추가합니다. `<device IP address> <Blob service endpoint>`
7. 참조를 위해 다음 이미지를 사용합니다. **호스트** 파일을 저장합니다.

    ![스크린샷은 IP 주소 및 Blob 서비스 엔드포인트가 추가된 메모장 문서를 보여줍니다.](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
