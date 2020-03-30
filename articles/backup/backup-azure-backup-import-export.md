---
title: Azure 가져오기/내보내기 서비스를 사용 하 고 시드 오프라인 백업
description: Azure 가져오기/내보내기 서비스를 사용하여 Azure Backup을 사용하여 네트워크 로 데이터를 보내는 방법을 알아봅니다. 이 문서에서는 Azure 가져오기/내보내기 서비스를 사용하여 초기 백업 데이터의 오프라인 시드에 대해 설명합니다.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206761"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure 백업의 오프라인 백업 워크플로우

Azure Backup은 데이터를 Azure에 처음 전체 백업하는 동안 네트워크 및 스토리지 비용을 절약하는 여러 가지 기본 제공 효율성 향상 기능이 있습니다. 초기 "전체" 백업은 일반적으로 많은 양의 데이터를 전송하며 델타/증분만 전송하는 후속 백업에 비해 네트워크 대역폭을 더 많이 요구합니다. 오프라인 시드 프로세스를 통해 Azure Backup은 디스크를 사용하여 오프라인 백업 데이터를 Azure에 업로드할 수 있습니다.

Azure Backup 오프라인 시드 프로세스는 Azure [가져오기/내보내기 서비스와](../storage/common/storage-import-export-service.md)긴밀하게 통합됩니다. 이 서비스를 사용하여 디스크를 사용하여 초기 백업 데이터를 Azure로 전송할 수 있습니다. 대기 시간이 많은 네트워크와 낮은 대역폭 네트워크를 통해 전송해야 하는 초기 백업 데이터의 테라바이트(TB)가 있는 경우 오프라인 시드 워크플로를 사용하여 하나 이상의 하드 드라이브에서 Azure 데이터 센터에 초기 백업 복사본을 발송할 수 있습니다. 다음 이미지는 워크플로의 단계에 대한 개요를 제공합니다.

  ![오프라인 가져오기 워크플로 프로세스 개요](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

오프라인 백업 프로세스에는 다음 단계가 포함됩니다.

1. 네트워크를 통해 백업 데이터를 보내는 대신 준비 위치에 백업 데이터를 기록합니다.
1. *AzureOfflineBackupDiskPrep* 유틸리티를 사용하여 스테이징 위치에 있는 데이터를 하나 이상의 SATA 디스크에 씁니다.
1. 준비 작업의 일부로 *AzureOfflineBackupDiskPrep* 유틸리티는 Azure 가져오기 작업을 만듭니다. SATA 드라이브를 가장 가까운 Azure 데이터 센터로 보내고 가져오기 작업을 참조하여 활동을 연결합니다.
1. Azure 데이터 센터에서 디스크의 데이터는 Azure Storage 계정에 복사됩니다.
1. Azure Backup은 스토리지 계정의 백업 데이터를 Recovery Services 자격 증명 모음으로 복사하고 증분 백업이 예약됩니다.

## <a name="supported-configurations"></a>지원되는 구성

다음 Azure Backup 기능 또는 워크로드는 다음에 대한 오프라인 백업 사용을 지원합니다.

> [!div class="checklist"]
>
> * Microsoft Azure 복구 서비스(MARS) 에이전트를 사용하여 파일 및 폴더를 백업합니다.
> * 시스템 센터 데이터 보호 관리자(DPM)를 사용하여 모든 워크로드 및 파일을 백업합니다.
> * Microsoft Azure 백업 서버를 사용 하 고 모든 워크로드 및 파일백업.
 
   > [!NOTE]
   > Azure 백업 에이전트를 사용하여 수행되는 시스템 상태 백업에는 오프라인 백업이 지원되지 않습니다.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>사전 요구 사항

  > [!NOTE]
  > 다음 필수 구성 프로그램 및 워크플로는 최신 Azure 복구 [서비스 에이전트를](https://aka.ms/azurebackup_agent)사용하여 파일 및 폴더의 오프라인 백업에만 적용됩니다. 시스템 센터 DPM 또는 Azure 백업 서버를 사용하여 워크로드에 대한 오프라인 백업을 수행하려면 [DPM 및 Azure 백업 서버에 대한 오프라인 백업 워크플로를](backup-azure-backup-server-import-export-.md)참조하십시오.

오프라인 백업 워크플로를 시작하기 전에 다음 필수 구성 조건을 완료합니다.

* 복구 [서비스 자격 증명 모음을](backup-azure-recovery-services-vault-overview.md)만듭니다. 볼트를 만들려면 [복구 서비스 자격 증명 모음 만들기의](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)단계를 따릅니다.
* Azure [백업 에이전트의 최신 버전만](https://aka.ms/azurebackup_agent) 해당되는 경우 Windows 서버 또는 Windows 클라이언트에 설치되고 컴퓨터가 복구 서비스 자격 증명 모음에 등록되어 있는지 확인합니다.
* Azure PowerShell 3.7.0은 Azure 백업 에이전트를 실행하는 컴퓨터에서 필요합니다. 다운로드 및 [Azure PowerShell의 3.7.0 버전을 설치합니다.](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)
* Azure 백업 에이전트를 실행하는 컴퓨터에서 Microsoft Edge 또는 Internet Explorer 11이 설치되어 있고 자바스크립트가 활성화되어 있는지 확인합니다.
* 복구 서비스 자격 증명 모음과 동일한 구독에서 Azure 저장소 계정을 만듭니다.
* Azure Active Directory 애플리케이션을 만드는 데 [필요한 권한](../active-directory/develop/howto-create-service-principal-portal.md)이 있는지 확인합니다. 오프라인 백업 워크플로는 Azure 저장소 계정과 연결된 구독에서 Azure Active Directory 응용 프로그램을 만듭니다. 응용 프로그램의 목표는 오프라인 백업 워크플로에 필요한 Azure 가져오기/내보내기 서비스에 대한 안전하고 범위가 설정된 액세스를 Azure Backup에 제공하는 것입니다.
* Azure 저장소 계정이 포함된 구독으로 *Microsoft.ImportExport* 리소스 공급자를 등록합니다. 리소스 공급자 등록하려면:
    1. 기본 메뉴에서 **구독을 선택합니다.**
    1. 여러 구독을 구독하는 경우 오프라인 백업에 사용할 구독을 선택합니다. 구독을 하나만 사용하는 경우에는 구독이 나타납니다.
    1. 구독 메뉴에서 **리소스 공급자를** 선택하여 공급자 목록을 봅니다.
    1. 공급자 목록에서 *Microsoft.ImportExport*로 스크롤합니다. **상태가** **등록되지 않은**경우 **레지스터를**선택합니다.

        ![리소스 공급자 등록](./media/backup-azure-backup-import-export/registerimportexport.png)

* 네트워크 공유 또는 컴퓨터의 추가 드라이브에 있을 수 있는, 초기 복사본을 저장할 충분한 디스크 공간이 있는 내부 또는 외부의 스테이징 위치가 생성됩니다. 예를 들어 500GB 파일 서버를 백업하려면 준비 영역이 500GB 이상인지 확인합니다. (압축으로 인해 더 작은 양이 사용됩니다.)
* Azure로 디스크를 보낼 때 2.5인치 SSD 또는 2.5인치 또는 3.5인치 SATA II/III 내부 하드 드라이브만 사용하십시오. 최대 10TB의 하드 드라이브를 사용할 수 있습니다. [Azure Import/Export 서비스 설명서](../storage/common/storage-import-export-requirements.md#supported-hardware)에서 서비스가 지원하는 최신 드라이브를 집합을 확인하세요.
* SATA 드라이브는 준비 위치에서 SATA 드라이브로 백업 데이터의 복사가 수행되는 컴퓨터(*복사 컴퓨터*라고 함)에 연결되어야 합니다. 복사 컴퓨터에서 BitLocker를 사용하도록 설정되어 있는지 확인합니다.

## <a name="workflow"></a>워크플로

이 섹션에서는 데이터를 Azure 데이터 센터에 전달하고 Azure Storage에 업로드할 수 있도록 오프라인 백업 워크플로에 대해 설명합니다. 가져오기 서비스 또는 프로세스의 모든 측면에 대한 질문이 있는 경우 [Azure 가져오기/내보내기 서비스 개요 설명서를](../storage/common/storage-import-export-service.md)참조하십시오.

## <a name="initiate-offline-backup"></a>오프라인 백업 시작

1. 복구 서비스 에이전트에서 백업을 예약하면 이 페이지가 표시됩니다.

    ![가져오기 페이지](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. 내 디스크를 사용하여 전송 옵션을 **선택합니다.**

    > [!NOTE]
    > Azure 데이터 상자 옵션을 사용하여 초기 백업 데이터를 오프라인으로 전송합니다. 이 옵션을 사용하면 Azure 호환 디스크를 자체 로 조달하는 데 필요한 노력을 줄일 수 있습니다. 복구 서비스 에이전트가 백업 데이터를 직접 기록할 수 있는 Microsoft 독점적이고 안전하며 변조방지Azure 데이터 박스 장치를 제공합니다.

1. **다음을**선택하고 상자를 조심스럽게 채웁니다.

    ![디스크 세부 정보 입력](./media/backup-azure-backup-import-export/your-disk-details.png)

   입력하는 상자는 다음과 같습니다.

    * **스테이징 위치**: 초기 백업 복사본을 쓸 임시 스토리지 위치입니다. 스테이징 위치는 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 다른 경우 스테이징 위치의 전체 네트워크 경로를 지정합니다.
    * **Azure 리소스 관리자 저장소 계정**: 모든 Azure 구독에서 리소스 관리자 유형 저장소 계정(범용 v1 또는 범용 v2)의 이름입니다.
    * **Azure 저장소 컨테이너:** 복구 서비스 자격 증명 모음에 복사되기 전에 백업 데이터를 가져오는 Azure 저장소 계정의 대상 저장소 Blob의 이름입니다.
    * **Azure 구독 ID**: Azure 저장소 계정이 만들어지는 Azure 구독의 ID입니다.
    * **Azure 가져오기 작업 이름:** Azure 가져오기/내보내기 서비스 및 Azure Backup이 디스크에서 Azure로 전송되는 데이터 전송을 추적하는 고유 이름입니다.
  
   상자를 입력한 후 **다음을**선택합니다. **스테이징 위치와** **Azure 가져오기 작업 이름** 정보를 저장합니다. 디스크를 준비해야 합니다.

1. 메시지가 표시되면 Azure 구독에 로그인합니다. Azure Backup에서 Azure Active Directory 응용 프로그램을 만들 수 있도록 로그인해야 합니다. Azure 가져오기/내보내기 서비스에 액세스하는 데 필요한 권한을 입력합니다.

    ![Azure 구독 로그인 페이지](./media/backup-azure-backup-import-export/azure-login.png)

1. 워크플로를 완료합니다. Azure 백업 에이전트 콘솔에서 **지금 백업을 선택합니다.**

    ![지금 백업](./media/backup-azure-backup-import-export/backupnow.png)

1. 마법사의 **확인** 페이지에서 **백업을**선택합니다. 초기 백업은 설정의 일환으로 준비 영역에 기록됩니다.

   ![지금 백업할 준비가 되어 있는지 확인합니다.](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    작업이 완료되면 스테이징 위치를 디스크 준비에 사용할 수 있습니다.

   ![지금 마법사 백업 페이지](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA 드라이브 준비 및 Azure에 제공

*AzureOfflineBackupDiskPrep* 유틸리티는 가장 가까운 Azure 데이터 센터에 전송되는 SATA 드라이브를 준비합니다. 이 유틸리티는 다음 경로의 Azure 백업 에이전트 설치 디렉터리에서 사용할 수 있습니다.

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. 디렉터리로 이동하여 *AzureOfflineBackupDiskPrep* 디렉터리를 SATA 드라이브가 연결된 다른 컴퓨터에 복사합니다. 연결된 SATA 드라이브가 있는 컴퓨터에서 다음을 확인하십시오.

   * 복사 컴퓨터는 "오프라인 백업 시작" 섹션에서 워크플로에 제공된 것과 동일한 네트워크 경로를 사용하여 오프라인 시드 워크플로의 스테이징 위치에 액세스할 수 있습니다.
   * BitLocker가 복사 컴퓨터에서 활성화됩니다.
   * Azure PowerShell 3.7.0이 설치되었습니다.
   * 최신 호환 브라우저(Microsoft Edge 또는 Internet Explorer 11)가 설치되고 자바스크립트가 활성화됩니다.
   * 복사 컴퓨터는 Azure Portal에 액세스할 수 있습니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 같을 수 있습니다.

     > [!IMPORTANT]
     > 원본 컴퓨터가 가상 머신인 경우 복사 컴퓨터는 원본 컴퓨터와 다른 물리적 서버 또는 클라이언트 컴퓨터여야 합니다.

1. 현재 디렉터리로 *AzureOfflineBackupDiskPrep* 유틸리티 디렉토리를 사용 하 여 복사 컴퓨터에서 높은 명령 프롬프트를 엽니다. 다음 명령 실행:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | 매개 변수 | 설명 |
    | --- | --- |
    | s:&lt;*위치 경로 준비*&gt; |이 필수 입력은 "오프라인 백업 시작" 섹션에서 워크플로에 입력한 스테이징 위치에 대한 경로를 제공하는 데 사용됩니다. |
    | p:&lt;*게시 설정 파일 경로*&gt; |이 선택적 입력은 "오프라인 백업 시작" 섹션에서 워크플로에 입력한 Azure 게시 설정 파일에 대한 경로를 제공하는 데 사용됩니다. |

    명령을 실행하면 유틸리티는 준비해야 하는 드라이브에 해당하는 Azure 가져오기 작업의 선택을 요청합니다. 단일 가져오기 작업만 제공된 스테이징 위치와 연결된 경우 이와 같은 페이지가 표시됩니다.

    ![Azure 디스크 준비 도구 입력](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Azure에 전송하도록 준비하려는 탑재된 디스크에 대한 드라이브 문자를 후행 콜론 없이 입력합니다.
1. 메시지가 표시될 때 드라이브의 서식에 대한 확인을 제공합니다.
1. Azure 구독에 로그인하라는 메시지가 표시됩니다. 자격 증명을 입력합니다.

    ![Azure 구독 로그인](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    그런 다음 도구가 디스크를 준비하고 백업 데이터를 복사하기 시작합니다. 제공된 디스크에 백업 데이터에 대한 공간이 충분하지 않은 경우 도구에서 프롬프트를 받으면 추가 디스크를 연결해야 할 수 있습니다. <br/>

    도구가 성공적으로 실행되면 명령 프롬프트에 세 자기 정보가 제공됩니다.

   1. 제공한 디스크 하나 이상이 Azure에 발송되도록 준비됩니다.
   1. 가져오기 작업이 만들어졌다는 확인을 받습니다. 가져오기 작업에 사용자가 제공한 이름이 사용됩니다.
   1. 이 도구는 Azure 데이터 센터의 발송 주소를 표시합니다.

      ![Azure 디스크 준비 완료](./media/backup-azure-backup-import-export/console2.png)<br/>

1. 명령 실행이 끝나면 발송 정보를 업데이트할 수 있습니다.

1. 디스크를 도구가 제공한 주소로 발송합니다. 나중에 참조할 수 있도록 추적 번호를 유지합니다.

   > [!IMPORTANT]
   > 두 개의 Azure 가져오기 작업에 동일한 추적 번호가 있을 수 없습니다. 단일 Azure 가져오기 작업에서 유틸리티에서 준비한 드라이브가 단일 패키지로 함께 제공되고 패키지에 대한 고유 추적 번호가 하나 있는지 확인합니다. 단일 패키지에서 별도의 Azure 가져오기 작업의 일부로 준비된 드라이브를 결합하지 마십시오.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure 가져오기 작업에 대한 배송 세부 정보 업데이트

다음 절차는 Azure 가져오기 작업 전달 세부 정보를 업데이트합니다. 이러한 정보에는 다음 항목에 대한 세부 정보가 포함됩니다.

* Azure에 디스크를 배달하는 캐리어의 이름입니다.
* 디스크에 대한 배송 세부 정보를 반환합니다.

1. Azure 구독에 로그인합니다.
1. 주 메뉴에서 **모든 서비스를**선택합니다. 모든 **서비스** 대화 상자에서 **가져오기**를 입력합니다. **가져오기/내보내기 작업이**표시되면 해당 작업을 선택합니다.

    ![배송 정보 입력](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    **가져오기/내보내기 작업** 메뉴가 열리고 선택한 구독의 모든 가져오기/내보내기 작업 목록이 나타납니다.

1. 구독이 여러 개인 경우 백업 데이터를 가져오는 데 사용되는 구독을 선택합니다. 그런 다음 새로 생성된 가져오기 작업을 선택하여 세부 정보를 엽니다.

    ![배송 정보 검토](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. 가져오기 작업에 대한 **설정** 메뉴에서 **배송 정보 관리를**선택합니다. 반품 배송 세부 정보를 입력 합니다.

    ![배송 정보 저장](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. 배송사업자의 추적 번호가 있는 경우 Azure 가져오기 작업 개요 페이지에서 배너를 선택하고 다음 세부 정보를 입력합니다.

   > [!IMPORTANT]
   > Azure 가져오기 작업 생성 후 2주 이내에 운송 업체 정보 및 추적 번호가 업데이트되어야 합니다. 2주 이내에 이 정보를 확인하지 않으면 작업이 삭제되고 드라이브가 처리되지 않을 수 있습니다.

   ![추적 정보 업데이트 경고](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![이동통신사 정보 및 추적 번호](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>드라이브 처리 시간

Azure 가져오기 작업을 처리하는 데 걸리는 시간은 다양합니다. 프로세스 시간은 배송 시간, 작업 유형, 복사할 데이터의 유형 및 크기, 제공된 디스크 크기와 같은 요소를 기반으로 합니다. Azure 가져오기/내보내기 서비스에 SLA가 없습니다. 디스크를 받은 후 서비스는 7~10일 이내에 Azure 저장소 계정에 대한 백업 데이터 복사본을 완료하기 위해 노력합니다.

### <a name="monitor-azure-import-job-status"></a>Azure 가져오기 작업 상태 모니터링

Azure 포털에서 가져오기 작업의 상태를 모니터링할 수 있습니다. **가져오기/내보내기 작업** 페이지로 이동하여 작업을 선택합니다. 가져오기 작업의 상태에 대한 자세한 내용은 [Azure 가져오기/내보내기 서비스란 무엇입니까?](../storage/common/storage-import-export-service.md)

### <a name="finish-the-workflow"></a>워크플로 완료

가져오기 작업이 성공적으로 완료되면 스토리지 계정에서 초기 백업 데이터를 사용할 수 있습니다. 다음 예약된 백업 시 Azure Backup은 저장소 계정에서 복구 서비스 자격 증명 모음으로 데이터의 내용을 복사합니다.

   ![복구 서비스 자격 증명 모음에 데이터 복사](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

다음 예약된 백업 시 Azure Backup은 증분 백업을 수행합니다.

### <a name="clean-up-resources"></a>리소스 정리

초기 백업이 완료되면 Azure Storage 컨테이너로 가져온 데이터와 스테이징 위치의 백업 데이터를 안전하게 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure 가져오기/내보내기 서비스 워크플로에 대한 질문은 [Microsoft Azure 가져오기/내보내기 서비스 사용을 사용하여 데이터를 Blob 저장소로 전송합니다.](../storage/common/storage-import-export-service.md)
