---
title: 마이크로소프트 Azure 복구 서비스 (MARS) 에이전트 를 업그레이드
description: Microsoft Azure 복구 서비스(MARS) 에이전트를 업그레이드하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672833"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>마이크로소프트 Azure 복구 서비스 (MARS) 에이전트 를 업그레이드

이 문서에서는 다음 방법을 설명합니다.

* 이전 버전의 MARS 에이전트를 통해 서버 식별
* 해당 서버에서 MARS 설치 업데이트

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>이전 버전의 MARS 에이전트를 통해 서버 식별

Azure 백업 에이전트 및 Azure 백업 서버 설치의 경우:

1. 이전 버전의 에이전트에 의해 백업될 가능성이 있는 서버를 등록한 복구 서비스 자격 증명 모음으로 이동합니다. Azure의 Azure 백업 업데이트 경고에서 이전 Azure 백업 에이전트가 있는 볼트의 대표 목록을 찾을 수 있습니다.
1. 복구 서비스 볼트의 왼쪽 **설정** 섹션에서 **관리** 섹션에서 **백업 인프라를** 선택합니다.
1. Azure 백업 서버 설치의 일부로 설치된 Azure 백업 **에이전트를** 검색하려면 관리 서버 아래의 백업 관리 **서버로 이동하십시오.** 그러면 관련 Azure Backup 에이전트의 버전 번호와 함께 Azure Backup 서버가 있는 서버가 나열됩니다.

    ![Azure 백업 서버 설치의 일부로 설치된 MARS 에이전트 목록](./media/upgrade-mars-agent/backup-management-servers.png)

1. Microsoft Azure 복구 서비스(MARS) 에이전트 설치 또는 Azure 백업 에이전트에 대한 에이전트 버전을 확인하려면 관리 서버에서 **보호된 서버로** **이동하십시오.** 그런 다음 백업 관리 유형에서 **Azure 백업 에이전트를** 선택합니다. 그러면 Azure Backup 에이전트 설치가 있는 서버와 설치의 버전 번호가 나열됩니다.

    ![MARS 에이전트가 설치된 서버 목록](./media/upgrade-mars-agent/protected-servers.png)

1. MARS 에이전트 설치에 대한 에이전트 **버전** 열 또는 Azure 백업 서버 설치에 대한 Azure 백업 에이전트 버전 열을 클릭하여 **Azure 백업 에이전트 버전** 열을 정렬합니다.

1. 이전 단계에서는 버전이 2.0.9083.0보다 낮은 Azure Backup 에이전트가 있는 서버 목록 또는 공백으로 나열된 에이전트 버전을 제공합니다. Azure 백업 에이전트를 업데이트해야 하는 서버입니다.

## <a name="update-the-mars-agent-installation-on-the-server"></a>서버에서 MARS 에이전트 설치 업데이트

Azure Backup 에이전트 업데이트가 필요한 서버를 식별한 후에는 확인된 각 서버에 대해 다음 단계를 수행합니다(Azure 백업 서버 또는 MARS 에이전트 사용). 아래 단계를 따르기 전에 [Azure 백업 에이전트의 최신 버전을 다운로드합니다.](https://aka.ms/azurebackup_agent)

1. Azure 백업 에이전트가 2.0.9083.0 보다 낮거나 비어 있는 행을 클릭합니다. 그러면 서버 세부 정보 화면이 열립니다.

    ![오래된 에이전트 버전이 있는 보호된 서버](./media/upgrade-mars-agent/old-agent-version.png)

    ![오래된 에이전트 버전이 있는 Azure 백업 서버](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. **연결을** 클릭하여 원격 데스크톱 연결 파일을 수신하여 서버에 연결하거나 서버의 원격 데스크톱 연결을 통해 서버에 직접 연결합니다.

    ![원격 데스크톱 연결을 통해 서버에 연결](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > 나열된 서버가 없거나 서비스 해제된 경우 아래의 나머지 단계를 무시하고 다음 서버로 건너뛸 수 있습니다.

1. 관리 로그인 세부 정보를 입력하고 로그인합니다.

1. 서버 또는 서버의 프록시에 인터넷 액세스가 제한된 경우 사용 중인 Azure 클라우드에 적합한 URL을 허용하도록 서버/프록시의 방화벽 설정이 구성되어 있는지 확인합니다.

    Azure 클라우드 | URL
    -- | ---
    Azure 클라우드(공용) |   `https://login.windows.net`
    Azure 중국 21Vianet 클라우드   | `https://login.chinacloudapi.cn`
    Azure 미국 정부 클라우드 |   `https://login.microsoftonline.us`
    Azure German Cloud  |  `https://login.microsoftonline.de`

1. Azure 백업 에이전트 업데이트 설치 관리자를 서버에 복사합니다.

    ![Azure 백업 에이전트 업데이트 설치 관리자를 서버에 복사](./media/upgrade-mars-agent/copy-agent-installer.png)

1. 설치 관리자를 실행합니다. Microsoft Azure 복구 서비스 에이전트 업그레이드 마법사가 열립니다.

    ![마이크로소프트 Azure 복구 서비스 에이전트 업그레이드 마법사](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. **다음**을 클릭합니다.

1. **업그레이드**를 클릭합니다.

    ![Microsoft Azure 복구 서비스 에이전트 설치](./media/upgrade-mars-agent/upgrade-installation.png)

1. 최종 확인 화면은 Azure Backup 에이전트가 성공적으로 업데이트되었음을 나타냅니다.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>시스템 센터 데이터 보호 관리자(SC DPM) 고객용

시스템 센터 데이터 보호 관리자(SC DPM) 서버에 Azure 백업 에이전트를 설치한 경우 아래 단계를 수행하여 DPM 서버에 Azure 백업 에이전트 업데이트가 필요한지 여부를 확인해야 합니다.

1. 관리자로 SC DPM 서버에 로그인합니다.
2. DPM 콘솔을 엽니다.
3. 콘솔의 왼쪽 아래 탐색에서 **관리를** 클릭합니다.
4. 왼쪽 탐색에 나타나는 정보 내에서 Azure Backup 에이전트 버전 정보를 찾습니다.
5. 버전이 2.0.9083.0보다 낮은 경우 최신 Azure 백업 에이전트 설치 관리자를 다운로드하고 DPM 서버에서 설치 관리자를 실행하여 Azure 백업 에이전트를 업데이트합니다.

사용자 환경의 모든 DPM 서버에 대해 위의 단계를 반복합니다.

## <a name="next-steps"></a>다음 단계

[Azure 백업 MARS 에이전트를 사용하여 Windows 컴퓨터를 백업하는](backup-windows-with-mars-agent.md) 방법에 대해 알아봅니다.
