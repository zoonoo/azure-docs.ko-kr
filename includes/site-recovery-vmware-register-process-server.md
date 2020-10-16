---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67534676"
---
1. 프로세스 서버를 실행하는 컴퓨터에 대한 원격 데스크톱 연결을 설정합니다. 
2. cspsconfigtool.exe를 실행하여 Azure Site Recovery 프로세스 서버 구성 도구를 시작합니다.
    - 이 도구는 프로세스 서버에 처음 로그인할 때 자동으로 시작됩니다.
    - 자동으로 열리지 않으면 바탕 화면에서 바로 가기를 클릭합니다.

3. **구성 서버 FQDN 또는 IP**에서 프로세스 서버를 등록할 구성 서버의 이름 또는 IP 주소를 지정합니다.
4. **구성 서버 포트**에서 443이 지정되어 있는지 확인합니다. 이는 구성 서버에서 요청을 수신 대기하는 포트입니다.
5. **연결 암호**에서 구성 서버를 설정할 때 지정한 암호를 지정합니다. 암호를 확인하려면 다음을 수행합니다.
    -  구성 서버에서 Site Recovery 설치 폴더인 * *\home\svssystems\bin\** 으로 이동합니다.
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - 아래 명령을 실행하여 현재 암호를 출력합니다.
    ```
    genpassphrase.exe -n
    ```

6. **데이터 전송 포트**에서 사용자 지정 포트를 지정하지 않은 경우 기본값을 그대로 둡니다.

7. **저장**을 클릭하여 설정을 저장하고, 프로세스 서버를 등록합니다.

    
    ![프로세스 서버 등록](./media/site-recovery-vmware-register-process-server/register-ps.png)
