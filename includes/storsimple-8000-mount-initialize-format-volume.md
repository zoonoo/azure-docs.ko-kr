---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ba2985b8b6c92e299e8ab378263c9b4c062561d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171522"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>볼륨을 탑재, 초기화 및 포맷하려면
1. Microsoft iSCSI 초기자를 시작합니다.
2. **iSCSI 초기자 속성** 창의 **검색** 탭에서 **포털 검색**을 클릭합니다.
3. **대상 포털 검색** 대화 상자에서 iSCSI 사용 네트워크 인터페이스의 IP 주소를 입력하고 **확인**을 클릭합니다. 
4. **iSCSI 초기자 속성** 창의 **대상** 탭에서 **검색된 대상**을 찾습니다. 디바이스 상태가 **비활성**으로 표시되어야 합니다.
5. 대상 디바이스를 선택하고 **연결**을 클릭합니다. 디바이스가 연결되면 상태가 **연결됨**으로 변경됩니다. Microsoft iSCSI 개시 장치 사용에 대한 자세한 내용은 [Microsoft iSCSI 초기자 설치 및 구성][1]을 참조하세요.
6. Windows 호스트에서 Windows 로고 키 + X를 누르고 **실행**을 클릭합니다. 
7. **실행** 대화 상자에 **Diskmgmt.msc**를 입력합니다. **확인**을 클릭하면 **디스크 관리** 대화 상자가 나타납니다. 오른쪽 창에 호스트의 볼륨이 표시됩니다.
8. **디스크 관리** 창에 탑재된 볼륨이 다음 그림과 같이 표시됩니다. 검색된 볼륨을 마우스 오른쪽 단추로 클릭(디스크 이름 클릭)한 다음 **온라인**을 클릭합니다.
   
     ![볼륨 포맷 초기화](./media/storsimple-8000-mount-initialize-format-volume/step7initializeformatvolume.png) 
9. 볼륨을 다시 마우스 오른쪽 단추로 클릭(디스크 이름 클릭)한 다음 **초기화**를 클릭합니다.
10. 단순 볼륨을 포맷하려면 다음 단계를 수행합니다.
    
    1. 볼륨을 선택하고 마우스 오른쪽 단추로 클릭(오른쪽 영역 클릭)한 다음 **새 단순 볼륨**을 클릭합니다.
    2. 새 단순 볼륨 마법사에서 볼륨 크기와 드라이브 문자를 지정하고 볼륨을 NTFS 파일 시스템으로 구성합니다.
    3. 64KB 할당 단위 크기를 지정합니다. 이 할당 단위 크기는 StorSimple 솔루션에서 사용되는 중복 제거 알고리즘과 효율적으로 연동됩니다.
    4. 빠른 포맷을 수행합니다.

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx
