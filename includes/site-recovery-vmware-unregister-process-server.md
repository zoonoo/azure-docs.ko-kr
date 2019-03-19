---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908538"
---
프로세스 서버를 등록 취소하는 단계는 구성 서버와의 연결 상태에 따라 달라집니다.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>연결된 상태인 프로세스 서버 등록 취소

1. 관리자 권한으로 프로세스 서버에 원격 로그인합니다.
2. 시작 합니다 **Control Panel** 연 **프로그램 > 프로그램 제거**합니다.
3. 프로그램 제거 **Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버**합니다.
4. 프로그램 제거 **Microsoft Azure Site Recovery 구성/프로세스 서버**합니다.
5. 3 ~ 4 단계에서 프로그램 제거 되 면 제거할 수 있습니다 **Microsoft Azure Site Recovery 구성/프로세스 서버 종속성**합니다.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>연결 해제된 상태인 프로세스 서버 등록 취소

> [!WARNING]
> 사용 된 프로세스 서버가 설치 된 가상 컴퓨터를 복구할 방법이 없는 경우 아래 단계입니다.

1. 관리자 권한으로 구성 서버에 로그인합니다.
2. 관리 명령 프롬프트를 열고 `%ProgramData%\ASR\home\svsystems\bin` 디렉터리를 찾습니다.
3. 이제 명령을 실행합니다.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. 위의 명령은 아래와 같이 일련 번호(S.No), IP 주소(IP), 프로세스 서버가 배포된 VM의 이름, VM의 하트비트(하트비트)를 포함한 프로세스 서버 목록(중복 항목의 경우 둘 이상일 수 있음)을 제공합니다.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. 이제 등록을 취소하려는 프로세스 서버의 일련 번호를 입력합니다.
6. 이 시스템에서 프로세스 서버의 세부 정보를 삭제 하 고 메시지가 표시 됩니다. **서버 이름이 등록을 취소 했습니다 > (서버 IP 주소)**

