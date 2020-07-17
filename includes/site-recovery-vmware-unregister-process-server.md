---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67182731"
---
특정 상황에 대 한 단계를 수행 합니다.

### <a name="unregister-a-connected-process-server"></a>연결 된 프로세스 서버 등록 취소

1. 관리자 권한으로 프로세스 서버에 대 한 원격 연결을 설정 합니다.
2. **제어판**에서 프로그램 **을 열고 프로그램 > 제거**합니다.
3. **모바일 서비스/마스터 대상 서버를 Site Recovery Microsoft Azure**프로그램을 제거 합니다.
4. 프로그램 **Microsoft Azure Site Recovery 구성/프로세스 서버**를 제거 합니다.
5. 3 단계 및 4 단계에서 프로그램을 제거한 후 **Microsoft Azure Site Recovery 구성/프로세스 서버 종속성**을 제거 합니다.

### <a name="unregister-a-disconnected-process-server"></a>연결 되지 않은 프로세스 서버 등록 취소

프로세스 서버가 설치 된 컴퓨터를 액세스 권한을 복원 방법이 없는 경우에만이 단계를 사용 합니다.

1. 관리자 권한으로 구성 서버에 로그인 합니다.
2. 관리 명령 프롬프트를 열고로 이동 `%ProgramData%\ASR\home\svsystems\bin` 합니다.
3. 하나 이상의 프로세스 서버 목록을 가져오려면이 명령을 실행 합니다.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. 아니요: 프로세스 서버 일련 번호입니다.
    - I p/이름: 프로세스 서버를 실행 하는 컴퓨터의 IP 주소 및 이름입니다.
    - 하트 비트: 프로세스 서버 컴퓨터에서 마지막 하트 비트입니다.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 등록을 취소할 프로세스 서버의 일련 번호를 지정 합니다.
5. 프로세스 서버의 등록을 취소 하면 시스템에서 모든 세부 정보를 제거 하 고 **성공적으로 등록 취소 된 서버 이름> (서버-IP 주소)** 메시지를 표시 합니다.

