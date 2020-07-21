---
title: DPM을 사용 하 여 Azure에 SharePoint 팜 백업
description: 이 문서는 Azure에 대한 SharePoint 팜 DPM/Azure Backup 서버 보호에 관한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 447776af7cb021fd0c3381334bf3acf258ff22ab
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539111"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>DPM을 사용 하 여 Azure에 SharePoint 팜 백업

SharePoint 팜은 다른 데이터 원본을 백업하는 것과 같은 방법으로 System Center DPM(Data Protection Manager)을 사용하여 Microsoft Azure에 백업합니다. Azure Backup은 일간, 주간, 월간 혹은 연간 백업 지점을 생성하도록 백업 일정에 유연성을 제공하고 다양한 백업 지점에 관한 보존 정책 옵션을 제공합니다. DPM은 빠른 복구 시간 목표(RTO)를 위해 로컬 디스크 복사본을 저장하는 기능과 경제적인 장기 보존을 위해 Azure에 사본을 복사하는 기능을 제공합니다.

DPM을 사용 하 여 SharePoint를 Azure에 백업 하는 작업은 SharePoint를 로컬로 DPM에 백업 하는 것과 매우 유사 합니다. Azure에 대한 특정 고려 사항은 이 문서에 나와 있습니다.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint가 지원하는 버전 및 관련 보호 시나리오

지원되는 SharePoint 버전 및 백업에 필요한 DPM 버전 목록은 [DPM이 백업할 수 있는 항목](/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup)을 참조하세요.

## <a name="before-you-start"></a>시작하기 전에

SharePoint 팜을 Azure에 백업하기 전에 몇 가지 확인이 필요합니다.

### <a name="prerequisites"></a>사전 준비 사항

진행에 앞서, 워크로드를 보호하기 위해 [Microsoft Azure Backup 사용의 필수 조건](backup-azure-dpm-introduction.md#prerequisites-and-limitations) 을 모두 충족해야 합니다. 필수 조건을 위한 작업에는 백업 자격 증명 모음 만들기, 보관 자격 증명 모음 다운로드, Azure Backup 에이전트 설치, 자격 증명 모음에 DPM/Azure Backup 서버 등록 등이 포함됩니다.

추가 필수 구성 요소 및 제한 사항은 [DPM을 사용 하 여 SharePoint 백업](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations) 문서를 참조 하세요.

## <a name="configure-backup"></a>백업 구성

SharePoint 팜을 백업하려면 ConfigureSharePoint.exe를 사용하여 SharePoint 보호를 구성한 다음 DPM에서 보호 그룹을 만듭니다. 지침은 DPM 설명서의 [백업 구성](//system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) 을 참조 하세요.

## <a name="monitoring"></a>모니터링

백업 작업을 모니터링 하려면 [DPM 백업 모니터링](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring) 의 지침을 따르세요.

## <a name="restore-sharepoint-data"></a>SharePoint 데이터 복원

DPM을 사용 하 여 디스크에서 SharePoint 항목을 복원 하는 방법에 대 한 자세한 내용은 [sharepoint 데이터 복원](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data)을 참조 하세요.

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>DPM을 사용하여 Azure에서 SharePoint 데이터베이스 복원

1. SharePoint 콘텐츠 데이터베이스를 복구하려면, 다양한 복구 지점을 살펴보고(이전과 같이), 복원할 복구 지점을 선택합니다.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. SharePoint 복구 지점을 두번 클릭하면 사용할 수 있는 SharePoint 카탈로그 정보가 나타납니다.

   > [!NOTE]
   > SharePoint 팜은 Azure에서 장기 보존으로 보호되기 때문에 DPM 서버에서 사용할 수 있는 카탈로그 정보(메타데이터)가 없습니다. 따라서, 지정 시간 SharePoint 콘텐츠 데이터베이스 복구가 필요할 때마다, SharePoint 팜 카탈로그를 다시 만들어야 합니다.
   >
   >
3. **카탈로그 다시 만들기**를 클릭합니다.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **클라우드 카탈로그 다시 만들기** 상태 창이 열립니다.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    카탈로그 만들기가 완료되면, 상태가 *성공*으로 변경됩니다. **닫기**를 클릭합니다.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. DPM **복구** 탭의 SharePoint 개체를 클릭하여 콘텐츠 데이터베이스 구조를 가져옵니다. 항목을 마우스 오른쪽 단추로 클릭한 다음 **복구**를 클릭합니다.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. 이제 이 문서 앞부분의 복구 단계를 수행하여 디스크로 SharePoint 콘텐츠 데이터베이스를 복구합니다.

## <a name="switching-the-front-end-web-server"></a>프런트 엔드 웹 서버 전환

프런트 엔드 웹 서버가 둘 이상 있고 DPM에서 팜을 보호 하기 위해 사용 하는 서버를 전환 하려면 [프런트 엔드 웹 서버 전환](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server)의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

* [Azure Backup Server 및 DPM-FAQ](backup-azure-dpm-azure-server-faq.md)
* [System Center Data Protection Manager 문제 해결](backup-azure-scdpm-troubleshooting.md)
