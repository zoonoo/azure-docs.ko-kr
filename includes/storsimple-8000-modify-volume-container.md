---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545421"
---
> [!NOTE] 
> 볼륨 컨테이너를 만든 후에는 볼륨 컨테이너와 연결된 암호화 설정과 스토리지 계정 자격 증명을 수정할 수 없습니다.

#### <a name="to-modify-a-volume-container"></a>볼륨 컨테이너를 수정하려면

1. StorSimple 디바이스 관리자 서비스로 이동한 다음, **관리 > 볼륨 컨테이너** 로 이동합니다.

2. 테이블 형식의 볼륨 컨테이너 목록에서 수정하려는 볼륨 컨테이너를 선택합니다. **장치** 페이지에서 장치를 선택하고 두 번 클릭한 다음 **볼륨 컨테이너** 탭을 클릭합니다.

3. 테이블 형식의 볼륨 컨테이너 목록에서 수정하려는 볼륨 컨테이너를 선택합니다. 열리는 블레이드의 명령 모음에서 **수정** 을 클릭합니다.

    ![볼륨 컨테이너 수정](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. **볼륨 컨테이너 수정** 블레이드에서 다음 단계를 수행합니다.
   
   1. 지정한 후에는 볼륨 컨테이너와 연결된 이름, 암호화 키 및 스토리지 계정을 변경할 수 없습니다. 연결된 대역폭 설정을 변경합니다.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![대역폭 설정 변경](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  **확인** 을 클릭합니다.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. **볼륨 컨테이너 수정** 대화 상자의 다음 페이지에서 아래 작업을 수행합니다.<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. 드롭다운 목록에서 기존 대역폭 템플릿을 선택합니다.
   1. 지정된 대역폭 템플릿에 대한 일정 설정을 검토합니다.
   1. **저장** 을 클릭하고 변경 내용을 확인합니다.
      
       ![변경 내용 확인](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      **볼륨 컨테이너** 블레이드가 변경 내용을 반영하기 위해 업데이트됩니다.
