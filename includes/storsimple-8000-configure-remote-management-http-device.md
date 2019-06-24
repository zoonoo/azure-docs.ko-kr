---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182264"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>클라우드 어플라이언스에서 원격 관리를 구성하려면

1. StorSimple Device Manager 서비스에서 **디바이스**를 클릭합니다. 서비스에 연결된 디바이스 목록에서 클라우드 어플라이언스를 선택하여 클릭합니다.
    ![StorSimple 클라우드 어플라이언스 선택](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. **설정 > 보안**으로 이동하여 **보안 설정** 블레이드를 엽니다.

     ![StorSimple 보안 설정](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. **원격 관리** 섹션으로 이동합니다. **원격 관리** 상자를 클릭합니다.
     ![StorSimple 원격 관리](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. **원격 관리** 블레이드에서:

    1. **원격 관리를 사용**이 활성화되어 있는지 확인합니다.
    2. 기본값은 HTTPS를 통해 연결합니다. HTTP를 사용하여 연결하도록 선택할 수 있습니다. HTTP를 통한 연결은 신뢰할 수 있는 네트워크에서만 허용됩니다. HTTP가 활성화되어 있는지 확인합니다.
    3. 블레이드 맨 위에 있는 명령 모음에서 **... 자세히**를 클릭한 후 **인증서 다운로드**를 클릭하여 원격 관리 인증서를 다운로드합니다. 이 파일을 저장하는 위치를 지정할 수 있습니다. 이 인증서는 클라우드 어플라이언스에 연결하는 데 사용할 클라이언트 또는 호스트 컴퓨터에 설치됩니다.

        ![원격 관리 블레이드](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. **저장**을 클릭하고 메시지가 표시되면 변경 내용을 확인 합니다.