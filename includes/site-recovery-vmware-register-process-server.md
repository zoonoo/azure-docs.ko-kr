---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165223"
---
* RDC(원격 데스크톱 연결)을 사용하여 프로세스 서버 가상 머신에 연결합니다.
* 바탕 화면에서 사용할 수 있는 바로 가기를 클릭하여 cspsconfigool.exe를 시작할 수 있습니다. (프로세스 서버에 처음 로그인할 때 도구가 자동으로 시작됩니다.)
  - 구성 서버의 정규화된 이름(FQDN) 또는 IP 주소
  - 구성 서버가 수신 대기하는 포트. 값은 443이어야 합니다.
  - 구성 서버에 연결할 연결 암호.
  - 이 프로세스 서버에 대해 구성할 데이터 전송 포트. 사용자 환경에서 다른 포트 번호로 변경하지 않는다면 기본 값을 그대로 둡니다.

    ![프로세스 서버 등록](./media/site-recovery-vmware-register-process-server/register-ps.png)
* 저장 단추를 클릭하여 구성을 저장하고 프로세스 서버를 등록합니다.
