---
title: Import/Export 서비스를 사용한 초기값 오프 라인 백업
description: Azure Import/Export 서비스를 사용 하 여 네트워크에서 데이터를 전송 하는 Azure Backup 방법에 대해 알아봅니다. 이 문서에서는 Azure 가져오기 내보내기 서비스를 사용 하 여 초기 백업 데이터의 오프 라인 시드에 대해 설명 합니다.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 47d4c4fb63c2aa0e2944456048b06070e235f012
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997363"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Offline biztonsági mentési munkafolyamat az Azure Backupban

Azure Backup에는 Azure에 데이터를 처음 전체 백업 하는 동안 네트워크 및 저장소 비용을 절감할 수 있는 몇 가지 기본 제공 효율성이 있습니다. 초기 전체 백업은 일반적으로 많은 양의 데이터를 전송 하 고 델타/증분만 전송 하는 후속 백업과 비교할 때 더 많은 네트워크 대역폭을 요구 합니다. 오프 라인 시드 프로세스를 통해 디스크를 사용 하 여 오프 라인 백업 데이터를 Azure에 업로드할 수 Azure Backup.

Azure Backup 오프 라인 시드 프로세스는 [Azure Import/Export 서비스](../storage/common/storage-import-export-service.md) 와 긴밀 하 게 통합 되므로 디스크를 사용 하 여 초기 백업 데이터를 azure에 전송할 수 있습니다. 대기 시간이 짧고 대역폭이 높은 네트워크를 통해 전송 해야 하는 테라바이트 (테라바이트)의 초기 백업 데이터가 있는 경우 오프 라인 시드 워크플로를 사용 하 여 하나 이상의 하드 드라이브에 있는 초기 백업 복사본을 Azure 데이터 센터에 제공할 수 있습니다. 다음 이미지는 워크플로의 단계에 대 한 개요를 제공 합니다.

  ![오프 라인 가져오기 워크플로 프로세스 개요](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

오프 라인 백업 프로세스에는 다음 단계가 포함 됩니다.

1. 네트워크를 통해 백업 데이터를 전송 하는 대신 백업 데이터를 준비 위치에 기록 합니다.
2. AzureOfflineBackupDiskPrep 유틸리티를 사용 하 여 준비 위치의 데이터를 하나 이상의 SATA 디스크에 씁니다.
3. Azure 가져오기 작업의 일부로 AzureOfflineBackupDiskPrep 유틸리티는 Azure 가져오기 작업을 만듭니다. SATA 드라이브를 가장 가까운 Azure 데이터 센터로 보내고, 작업을 연결 하는 가져오기 작업을 참조 합니다.
4. Azure 데이터 센터에서 디스크의 데이터는 Azure storage 계정에 복사 됩니다.
5. Azure Backup는 저장소 계정에서 Recovery Services 자격 증명 모음으로 백업 데이터를 복사 하 고 증분 백업이 예약 됩니다.

## <a name="supported-configurations"></a>Támogatott konfigurációk

다음 Azure Backup 기능 또는 작업은 오프 라인 백업 사용을 지원 합니다.

> [!div class="checklist"]
>
> * Microsoft Azure Recovery Services (MARS) 에이전트를 사용 하 여 파일 및 폴더를 백업 합니다 (Azure Backup 에이전트 라고도 함).
> * System Center Data Protection Manager (SC DPM)를 사용 하 여 모든 워크 로드 및 파일의 백업
> * Microsoft Azure Backup Server를 사용 하 여 모든 워크 로드 및 파일의 백업
 
   > [!NOTE]
   > Azure Backup 에이전트를 사용 하 여 수행 된 시스템 상태 백업에는 오프 라인 백업이 지원 되지 않습니다.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Előfeltételek

  > [!NOTE]
  > 다음 필수 구성 요소 및 워크플로는 [최신 MARS 에이전트](https://aka.ms/azurebackup_agent)를 사용 하 여 파일 및 폴더의 오프 라인 백업에만 적용 됩니다. System Center DPM 또는 Azure Backup Server를 사용 하 여 워크 로드에 대 한 오프 라인 백업을 수행 하려면 [이 문서](backup-azure-backup-server-import-export-.md)를 참조 하세요.

오프 라인 백업 워크플로를 시작 하기 전에 다음 필수 구성 요소를 완료 합니다.

* [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)을 만듭니다. 자격 증명 모음을 만들려면 [이 문서의](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) 단계를 참조 하세요.
* 해당 하는 경우 Windows Server/Windows 클라이언트에 [최신 버전의 Azure Backup 에이전트만](https://aka.ms/azurebackup_agent) 설치 되어 있는지 확인 하 고, 해당 컴퓨터는 Recovery Services 자격 증명 모음에 등록 되어 있어야 합니다.
* Azure PowerShell 3.7.0는 Azure Backup 에이전트를 실행 하는 컴퓨터에 필요 합니다. [Azure PowerShell 3.7.0 버전](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)을 다운로드 하 여 설치 하는 것이 좋습니다.
* Azure Backup 에이전트를 실행 하는 컴퓨터에서 Microsoft Edge 또는 Internet Explorer 11이 설치 되어 있고 JavaScript가 사용 하도록 설정 되어 있는지 확인 합니다.
* Recovery Services 자격 증명 모음과 동일한 구독에 Azure Storage 계정을 만듭니다.
* Azure Active Directory 응용 프로그램을 만드는 데 [필요한 권한이](../active-directory/develop/howto-create-service-principal-portal.md) 있는지 확인 합니다. 오프 라인 백업 워크플로는 Azure Storage 계정과 연결 된 구독에 Azure Active Directory 응용 프로그램을 만듭니다. 응용 프로그램의 목표는 오프 라인 백업 워크플로에 필요한 Azure 가져오기 서비스에 대 한 보안 및 범위 액세스를 Azure Backup 제공 하는 것입니다.
* Azure Storage 계정이 포함 된 구독과 함께 Microsoft ImportExport 리소스 공급자를 등록 합니다. 리소스 공급자를 등록 하려면:
    1. 주 메뉴에서 **구독**을 클릭 합니다.
    2. 여러 구독을 구독 하는 경우 오프 라인 백업에 사용 중인 구독을 선택 합니다. 구독을 하나만 사용 하는 경우 구독이 표시 됩니다.
    3. 구독 메뉴에서 **리소스 공급자** 를 클릭 하 여 공급자 목록을 봅니다.
    4. 공급자 목록에서 Microsoft. ImportExport로 스크롤합니다. 상태가 **Notregistered**인 경우 등록을 클릭 합니다.
    리소스 공급자를 등록 하는 ![](./media/backup-azure-backup-import-export/registerimportexport.png)
* 스테이징 위치는 네트워크 공유 또는 컴퓨터의 추가 드라이브 (초기 복사본을 저장할 충분 한 디스크 공간이 있는 내부 또는 외부)가 생성 됩니다. 예를 들어 500-GB 파일 서버를 백업 하려는 경우 준비 영역이 500 GB 이상 인지 확인 합니다. 압축으로 인해 더 작은 양이 사용 됩니다.
* Azure로 디스크를 전송 하는 경우 2.5 인치 SSD 또는 2.5 인치 또는 3.5 인치 SATA II/III 내부 하드 드라이브만 사용 합니다. 최대 10TB의 하드 드라이브를 사용할 수 있습니다. 서비스에서 지 원하는 최신 드라이브 집합은 [Azure Import/Export 서비스 설명서](../storage/common/storage-import-export-requirements.md#supported-hardware) 를 참조 하세요.
* SATA 드라이브는 *준비 위치* 에서 sata 드라이브로 백업 데이터를 복사 하는 컴퓨터 ( *복사 컴퓨터*라고 함)에 연결 되어 있어야 합니다. *복사 컴퓨터*에서 BitLocker가 사용 하도록 설정 되어 있는지 확인 합니다.

## <a name="workflow"></a>Munkafolyamat

이 섹션에서는 데이터를 Azure 데이터 센터에 배달 하 고 Azure Storage에 업로드할 수 있도록 오프 라인 백업 워크플로에 대해 설명 합니다. 가져오기 서비스 또는 프로세스의 모든 측면에 대 한 질문이 있는 경우 [서비스 가져오기 개요 설명서](../storage/common/storage-import-export-service.md)를 참조 하세요.

## <a name="initiate-offline-backup"></a>오프 라인 백업 시작

1. MARS 에이전트에서 백업을 예약할 때 다음 화면이 표시 됩니다.

    ![가져오기 화면](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   입력에 대 한 설명은 다음과 같습니다.

    * **스테이징 위치**: 초기 백업 복사본을 쓸 임시 저장소 위치입니다. 스테이징 위치는 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 서로 다른 경우 스테이징 위치의 전체 네트워크 경로를 지정 하는 것이 좋습니다.
    * **Azure Resource Manager Storage 계정**: Azure 구독의 리소스 관리자 유형 저장소 계정 (범용 v1 또는 범용 v2) 이름입니다.
    * **Azure Storage 컨테이너**: Recovery Services 자격 증명 모음에 복사 하기 전에 백업 데이터를 가져오는 Azure Storage 계정의 대상 저장소 blob 이름입니다.
    * **Azure 구독 id**: Azure Storage 계정이 만들어진 AZURE 구독의 id입니다.
    * **Azure 가져오기 작업 이름**: azure 가져오기 서비스와 Azure Backup에서 azure로 전송 되는 데이터의 전송을 추적 하는 고유한 이름입니다.
  
   화면에 입력을 입력 하 고 **다음**을 클릭 합니다. 디스크를 준비 하는 데이 정보가 필요 하므로 제공 된 *준비 위치* 및 *Azure 가져오기 작업 이름을*저장 합니다.

2. 메시지가 표시 되 면 Azure 구독에 로그인 합니다. Azure Backup Azure Active Directory 응용 프로그램을 만들고 Azure 가져오기 서비스에 액세스 하는 데 필요한 권한을 제공할 수 있도록 로그인 해야 합니다.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/azurelogin.png)

3. 워크플로를 완료 하 고 Azure Backup 에이전트 콘솔에서 **지금**백업을 클릭 합니다.

    ![Biztonsági mentés](./media/backup-azure-backup-import-export/backupnow.png)

4. 마법사의 확인 페이지에서 **백업**을 클릭 합니다. 초기 백업은 설치의 일부로 준비 영역에 기록 됩니다.

   ![지금 백업할 준비가 되었는지 확인 합니다.](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    작업이 완료 된 후 준비 위치를 디스크 준비에 사용할 준비가 된 것입니다.

   ![Biztonsági mentés](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA 드라이브 준비 및 Azure로 배송

*Azureofflinebackupdiskprep* 유틸리티는 가장 가까운 Azure 데이터 센터로 전송 되는 SATA 드라이브를 준비 합니다. 이 유틸리티는 Azure Backup 에이전트 설치 디렉터리 (다음 경로)에서 사용할 수 있습니다.

    *\Microsoft Azure Recovery Services Agent\Utils\\*

1. 디렉터리로 이동 하 여 **Azureofflinebackupdiskprep** 디렉터리를 SATA 드라이브가 연결 된 다른 컴퓨터에 복사 합니다. SATA 드라이브가 연결 된 컴퓨터에서 다음을 확인 합니다.

   * Copy 컴퓨터는 **오프 라인 백업 시작** 워크플로에 제공 된 것과 동일한 네트워크 경로를 사용 하 여 오프 라인 시드 워크플로의 준비 위치에 액세스할 수 있습니다.
   * BitLocker가 복사 컴퓨터에서 사용 하도록 설정 되어 있습니다.
   * Azure PowerShell 3.7.0가 설치 되어 있습니다.
   * 최신 호환 브라우저 (Microsoft Edge 또는 Internet Explorer 11)가 설치 되어 있고 JavaScript가 사용 하도록 설정 되어 있습니다.
   * 복사 컴퓨터는 Azure Portal에 액세스할 수 있습니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 동일할 수 있습니다.

     > [!IMPORTANT]
     > 원본 컴퓨터가 가상 컴퓨터인 경우 복사 컴퓨터는 원본 컴퓨터와 다른 실제 서버 또는 클라이언트 컴퓨터 여야 합니다.

2. 현재 디렉터리로 *Azureofflinebackupdiskprep* 유틸리티 디렉터리를 사용 하 여 복사 컴퓨터에서 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행 합니다.

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Paraméter | Leírás |
    | --- | --- |
    | s:&lt;*준비 위치 경로*&gt; |**오프 라인 백업 시작** 워크플로에 입력 한 스테이징 위치에 대 한 경로를 제공 하는 데 사용 되는 필수 입력입니다. |
    | p:&lt;*파일의 경로*&gt; |**오프 라인 백업 시작** 워크플로에 입력 한 **Azure 게시 설정** 파일의 경로를 제공 하는 데 사용 되는 선택적 입력입니다. |

    명령을 실행 하면 유틸리티가 준비 해야 하는 드라이브에 해당 하는 Azure 가져오기 작업의 선택을 요청 합니다. 제공 된 준비 위치와 연결 된 가져오기 작업이 하나만 있는 경우 다음과 같은 화면이 표시 됩니다.

    ![Azure 디스크 준비 도구 입력](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Azure로 전송 하기 위해 준비 하려는 탑재 된 디스크의 후행 콜론 없이 드라이브 문자를 입력 합니다.
4. 메시지가 표시 되 면 드라이브의 형식에 대 한 확인을 제공 합니다.
5. Azure 구독에 로그인 하 라는 메시지가 표시 됩니다. Adja meg a hitelesítő adatait.

    ![Azure 디스크 준비 도구 입력](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    그런 다음 도구는 디스크를 준비 하 고 백업 데이터를 복사 하기 시작 합니다. 제공 된 디스크에 백업 데이터에 대 한 충분 한 공간이 없는 경우 도구에서 메시지가 표시 될 때 추가 디스크를 연결 해야 할 수 있습니다. <br/>

    도구를 성공적으로 실행 한 후 명령 프롬프트에서 다음 세 가지 정보를 제공 합니다.
   1. 제공한 하나 이상의 디스크를 Azure로 배송할 준비가 됩니다.
   2. 가져오기 작업이 생성 되었다는 확인 메시지가 표시 됩니다. 가져오기 작업은 사용자가 제공한 이름을 사용 합니다.
   3. 이 도구는 Azure 데이터 센터에 대 한 배송 주소를 표시 합니다.

      ![Azure 디스크 준비 완료](./media/backup-azure-backup-import-export/console2.png)<br/>

6. 명령 실행이 끝나면 배송 정보를 업데이트할 수 있습니다.

7. 도구가 제공 하는 주소로 디스크를 배송 하 고 나중에 참조할 수 있도록 추적 번호를 유지 합니다.

   > [!IMPORTANT]
   > 두 Azure 가져오기 작업은 동일한 추적 번호를 가질 수 없습니다. 단일 Azure 가져오기 작업에서 유틸리티에 의해 준비 된 드라이브가 단일 패키지로 함께 배송 되 고 패키지에 대 한 단일 고유 추적 번호가 있는지 확인 합니다. 별도의 Azure 가져오기 작업의 일부로 준비 된 드라이브를 단일 패키지로 결합 하지 마십시오.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure 가져오기 작업의 배송 세부 정보 업데이트

다음 절차에서는 Azure 가져오기 작업 전달 세부 정보를 업데이트 합니다. 이 정보에는 다음에 대 한 세부 정보가 포함 됩니다.

* Azure에 디스크를 배달 하는 회사의 이름
* 디스크의 배송 세부 정보를 반환 합니다.

1. Azure 구독에 로그인 합니다.
2. 주 메뉴에서 **모든 서비스** 를 클릭 하 고 모든 서비스 대화 상자에서 가져오기를 입력 합니다. **가져오기/내보내기 작업이**표시 되 면 클릭 합니다.
    배송 정보를 입력 ![](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    **가져오기/내보내기 작업** 메뉴가 열리고 선택한 구독의 모든 가져오기/내보내기 작업 목록이 표시 됩니다.

3. 구독이 여러 개인 경우 백업 데이터를 가져오는 데 사용 되는 구독을 선택 해야 합니다. 그런 다음 새로 만든 가져오기 작업을 선택 하 여 세부 정보를 엽니다.

    ![배송 정보 검토](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. 가져오기 작업의 설정 메뉴에서 **배송 정보 관리** 를 클릭 하 고 반송 세부 정보를 입력 합니다.

    ![배송 정보 저장](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. 배송 운송 업체의 추적 번호가 있으면 Azure 가져오기 작업 개요 페이지에서 배너를 클릭 하 고 다음 세부 정보를 입력 합니다.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy az Azure importálási feladat létrehozásától számított két héten belül frissítve legyenek a szolgáltató adatai és a nyomkövetési azonosító szám. 2 주 이내에이 정보를 확인 하지 못하면 작업이 삭제 되 고 드라이브가 처리 되지 않을 수 있습니다.

   ![배송 정보 저장](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![배송 정보 저장](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>드라이브를 처리 하는 시간

Azure 가져오기 작업을 처리 하는 데 걸리는 시간은 배송 시간, 작업 유형, 복사 되는 데이터의 유형 및 크기, 제공 된 디스크 크기와 같은 다양 한 요소에 따라 다릅니다. Azure Import/Export 서비스에는 SLA가 없지만 디스크를 받은 후에는 7 ~ 10 일 내에 Azure storage 계정에 대 한 백업 데이터 복사를 완료 하기 위해 서비스가 노력 하 고 있습니다.

### <a name="monitoring-azure-import-job-status"></a>Azure 가져오기 작업 상태 모니터링

**가져오기/내보내기 작업** 페이지로 이동 하 고 작업을 선택 하 여 Azure Portal에서 가져오기 작업의 상태를 모니터링할 수 있습니다. 가져오기 작업의 상태에 대 한 자세한 내용은 [저장소 가져오기 내보내기 서비스](../storage/common/storage-import-export-service.md) 문서를 참조 하세요.

### <a name="complete-the-workflow"></a>워크플로 완료

가져오기 작업이 성공적으로 완료 되 면 저장소 계정에서 초기 백업 데이터를 사용할 수 있습니다. 다음 예약 된 백업 시 Azure Backup는 아래와 같이 저장소 계정에서 Recovery Services 자격 증명 모음으로 데이터의 내용을 복사 합니다.

   ![Recovery Services 자격 증명 모음에 데이터 복사](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

다음 예약 된 백업 시 Azure Backup 증분 백업을 수행 합니다.

### <a name="cleaning-up-resources"></a>리소스 정리

초기 백업이 완료 되 면 Azure Storage 컨테이너 및 준비 위치의 백업 데이터로 가져온 데이터를 안전 하 게 삭제할 수 있습니다.

## <a name="next-steps"></a>Következő lépések

* Azure Import/Export 워크플로에 대 한 질문은 [Microsoft Azure Import/Export 서비스를 사용 하 여 Blob storage로 데이터 전송](../storage/common/storage-import-export-service.md)을 참조 하세요.

