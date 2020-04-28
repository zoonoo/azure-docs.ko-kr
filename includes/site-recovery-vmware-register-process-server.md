---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67534676"
---
1. 프로세스 서버를 실행 하는 컴퓨터에 대 한 원격 데스크톱 연결를 설정 합니다. 
2. Cspsconfigtool .exe를 실행 하 여 Azure Site Recovery Process Server 구성 도구를 시작 합니다.
    - 이 도구는 프로세스 서버에 처음으로 로그인 할 때 자동으로 시작 됩니다.
    - 자동으로 열리지 않으면 바탕 화면에서 바로 가기를 클릭 합니다.

3. **구성 서버 FQDN 또는 ip**에서 프로세스 서버를 등록할 구성 서버의 이름 또는 ip 주소를 지정 합니다.
4. **구성 서버 포트**에서 443이 지정 되어 있는지 확인 합니다. 구성 서버가 요청을 수신 대기 하는 포트입니다.
5. **연결 암호**에서 구성 서버를 설정할 때 지정한 암호를 지정 합니다. 암호를 찾으려면 다음을 수행 합니다.
    -  구성 서버에서 Site Recovery 설치 폴더 **\home\svssystems\bin\** 로 이동 합니다.
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - 아래 명령을 실행 하 여 현재 암호를 출력 합니다.
    ```
    genpassphrase.exe -n
    ```

6. **데이터 전송 포트**에서 사용자 지정 포트를 지정 하지 않은 경우 기본값을 그대로 둡니다.

7. **저장** 을 클릭 하 여 설정을 저장 하 고 프로세스 서버를 등록 합니다.

    
    ![프로세스 서버 등록](./media/site-recovery-vmware-register-process-server/register-ps.png)
