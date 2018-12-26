---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 08/06/2018
ms.author: ramamill
ms.openlocfilehash: 81390d38b4c0c38b7ac6883ae2bf18c64542fa00
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582921"
---
프로세스 서버를 등록 취소하는 단계는 구성 서버와의 연결 상태에 따라 달라집니다.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>연결된 상태인 프로세스 서버 등록 취소

1. 관리자 권한으로 프로세스 서버에 원격 로그인합니다.
2. **제어판**을 실행하고 **프로그램 > 프로그램 제거**를 엽니다.
3. Uninstall a program by the name **Microsoft Azure Site Recovery 구성/프로세스 서버** 이름으로 프로그램을 제거합니다.
4. 3단계가 완료되면 **Microsoft Azure Site Recovery 구성/프로세스 서버**를 제거할 수 있습니다.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>연결 해제된 상태인 프로세스 서버 등록 취소

> [!WARNING]
> 아래 단계는 프로세스 서버가 설치된 가상 머신을 복구할 방법이 없는 경우 사용해야 합니다.

1. 관리자 권한으로 구성 서버에 로그인합니다.
2. 관리 명령 프롬프트를 열고 `%ProgramData%\ASR\home\svsystems\bin` 디렉터리를 찾습니다.
3. 이제 명령을 실행합니다.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. 위의 명령은 아래와 같이 일련 번호(S.No), IP 주소(IP), 프로세스 서버가 배포된 VM의 이름, VM의 하트비트(하트비트)를 포함한 프로세스 서버 목록(중복 항목의 경우 둘 이상일 수 있음)을 제공합니다.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. 이제 등록을 취소하려는 프로세스 서버의 일련 번호를 입력합니다.
6. 그러면 이 시스템에서 프로세스 서버의 세부 정보를 삭제하고 **서버 이름> 등록을 성공적으로 취소했습니다(서버 IP 주소).** 라는 메시지가 표시됩니다.

