---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67182731"
---
특정 상황에 대한 단계를 따르십시오.

### <a name="unregister-a-connected-process-server"></a>연결된 프로세스 서버 등록 취소

1. 프로세스 서버에 대한 원격 연결을 관리자로 설정합니다.
2. **제어판에서**프로그램을 **> 프로그램을 엽니다.**
3. 프로그램 제거 **Microsoft Azure 사이트 복구 이동성 서비스/마스터 대상 서버**.
4. 프로그램 제거 **Microsoft Azure 사이트 복구 구성/프로세스 서버**.
5. 3단계와 4단계의 프로그램이 제거된 후 **Microsoft Azure 사이트 복구 구성/프로세스 서버 종속성을 제거합니다.**

### <a name="unregister-a-disconnected-process-server"></a>연결이 끊긴 프로세스 서버 등록 취소

프로세스 서버가 설치된 컴퓨터를 다시 볼 방법이 없는 경우에만 다음 단계를 사용합니다.

1. 구성 서버에 관리자로 로그인합니다.
2. 관리 명령 프롬프트를 `%ProgramData%\ASR\home\svsystems\bin`열고 을 찾아봅
3. 이 명령을 실행하여 하나 이상의 프로세스 서버 목록을 가져옵니다.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S.는 아니오: 프로세스 서버 일련 번호입니다.
    - IP/이름: 프로세스 서버를 실행하는 컴퓨터의 IP 주소 및 이름입니다.
    - 하트비트: 프로세스 서버 컴퓨터에서 마지막 하트비트입니다.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 등록 취소할 프로세스 서버의 일련 번호를 지정합니다.
5. 프로세스 서버 등록취소하면 시스템에서 모든 세부 정보가 제거되고 **등록되지 않은 서버 이름>(서버-IP 주소)이라는** 메시지가 표시됩니다.

