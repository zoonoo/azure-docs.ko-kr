---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170010"
---
해당 특정 환경에 대 한 단계를 따릅니다.

### <a name="unregister-a-connected-process-server"></a>연결 된 프로세스 서버 등록 취소

1. 관리자 권한으로 프로세스 서버에 원격 연결을 설정 합니다.
2. 에 **Control Panel**오픈 **프로그램 > 프로그램 제거**합니다.
3. 프로그램을 제거 **Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버**합니다.
4. 프로그램을 제거 **Microsoft Azure Site Recovery 구성/프로세스 서버**합니다.
5. 3 ~ 4 단계에서 프로그램 제거 되 면 제거 **Microsoft Azure Site Recovery 구성/프로세스 서버 종속성**합니다.

### <a name="unregister-a-disconnected-process-server"></a>연결이 끊긴된 프로세스 서버 등록 취소

프로세스 서버가 설치 되어 있는 컴퓨터를 복구할 방법이 없는 경우에 다음이 단계를 사용 합니다.

1. 구성 서버에 관리자로 로그인 합니다.
2. 관리 명령 프롬프트를 열고 `%ProgramData%\ASR\home\svsystems\bin`합니다.
3. 하나 이상의 프로세스 서버의 목록을 가져오려면이 명령을 실행 합니다.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. 아니요: 프로세스 서버 일련 번호입니다.
    - IP/이름: IP 주소와 프로세스 서버를 실행 하는 컴퓨터의 이름입니다.
    - 하트 비트: 프로세스 서버 컴퓨터에서 마지막 하트 비트입니다.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 프로세스 서버 등록 취소 하려는의 일련 번호를 지정 합니다.
5. 프로세스 서버를 등록 취소 하면 시스템에서 모든 세부 정보 제거 된 메시지를 표시 합니다. **서버 이름이 등록을 취소 했습니다 > (서버 IP 주소)**

