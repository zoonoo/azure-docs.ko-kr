---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: cf39baf34096691144181332566cf567ebc02310
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925595"
---
1. 프로세스 서버를 실행 하는 컴퓨터에 원격 데스크톱 연결을 설정 합니다. 
2. Azure Site Recovery 프로세스 서버 구성 도구를 시작 하려면 cspsconfigtool.exe를 실행 합니다.
    - 도구는 자동으로 프로세스 서버에 로그인 하면 처음으로 시작 됩니다.
    - 자동으로 열리지 않으면 바탕 화면 바로 가기를 클릭 합니다.

3. **구성 서버 FQDN 또는 IP**, 이름 또는 프로세스 서버를 등록 하는 구성 서버의 IP 주소를 지정 합니다.
4. **구성 서버 포트**, 443 지정 되어 있는지 확인 합니다. 요청에 대 한 구성 서버가 수신 하는 포트입니다.
5. **연결 암호**, 구성 서버를 설정할 때 지정한 암호를 지정 합니다. 암호를 찾으려면:
    -  구성 서버에서 Site Recovery 설치 폴더를 이동 **\home\svssystems\bin\** 합니다. 
    - 이 명령을 실행 합니다. **genpassphrase.exe.n**합니다. 이 암호를 기록할 수 있습니다 위치를 표시 합니다.

6. **데이터 전송 포트**, 사용자 지정 포트를 지정 하지 않으면 기본값을 그대로 둡니다.

7. 클릭 **저장할** 설정을 저장 하 고 프로세스 서버를 등록 합니다.

    
    ![프로세스 서버를 등록 합니다.](./media/site-recovery-vmware-register-process-server/register-ps.png)
