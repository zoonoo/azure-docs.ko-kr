---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361395"
---
특정 상황에 맞는 단계를 따릅니다.

### <a name="unregister-a-connected-process-server"></a>연결된 프로세스 서버 등록 취소

1. 관리자 권한으로 프로세스 서버에 대한 원격 연결을 설정합니다.
2. **제어판**에서 **프로그램 > 프로그램 제거**를 차례로 엽니다.
3. **Microsoft Azure Site Recovery Mobility Service/마스터 대상 서버** 프로그램을 제거합니다.
4. **Microsoft Azure Site Recovery 구성/프로세스 서버** 프로그램을 제거합니다.
5. 3단계 및 4단계의 프로그램이 제거되면 **Microsoft Azure Site Recovery 구성/프로세스 서버 종속성**을 제거합니다.

### <a name="unregister-a-disconnected-process-server"></a>연결이 끊긴 프로세스 서버 등록 취소

프로세스 서버가 설치된 컴퓨터를 복구할 방법이 없는 경우에만 다음 단계를 사용합니다.

1. 관리자 권한으로 구성 서버에 로그인합니다.
2. 관리 명령 프롬프트를 열고, `%ProgramData%\ASR\home\svsystems\bin`으로 이동합니다.
3. 다음 명령을 실행하여 하나 이상의 프로세스 서버 목록을 가져옵니다.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - 일련 번호: 프로세스 서버 일련 번호입니다.
    - IP/이름: 프로세스 서버를 실행하는 컴퓨터의 IP 주소 및 이름입니다.
    - 하트비트: 프로세스 서버 컴퓨터의 마지막 하트비트입니다.
    ![프로세스 서버 및 텍스트 정보에 대한 일반 텍스트 표시를 보여주는 스크린샷입니다. 등록을 취소하려면 위의 서버 중 하나를 선택하세요.(media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 등록을 취소하려는 프로세스 서버의 일련 번호를 지정합니다.
5. 프로세스 서버의 등록을 취소하면 시스템에서 해당 세부 정보가 모두 제거되고, **server-name> (server-IP-address) 등록을 취소했습니다.** 라는 메시지가 표시됩니다.

