---
title: DPM 및 Azure Backup Server를 위한 오프라인 백업
description: Azure Backup을 사용하면 Azure 가져오기/내보내기 서비스를 사용하여 네트워크 에서 데이터를 보낼 수 있습니다. 이 문서에서는 DPM 및 Azure 백업 서버에 대한 오프라인 백업 워크플로를 설명합니다.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197075"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>DPM 및 Azure 백업 서버에 대한 오프라인 백업 워크플로우

Azure Backup은 데이터를 Azure에 처음 전체 백업하는 동안 네트워크 및 스토리지 비용을 절약하는 여러 가지 기본 제공 효율성 향상 기능이 있습니다. 초기 "전체" 백업은 일반적으로 많은 양의 데이터를 전송하며 델타/증분만 전송하는 후속 백업에 비해 네트워크 대역폭을 더 많이 요구합니다. Azure Backup은 초기 백업을 압축합니다. 오프라인 시드의 프로세스를 통해 Azure Backup은 디스크를 사용하여 오프라인으로 압축된 초기 백업 데이터를 Azure에 업로드할 수 있습니다.

Azure Backup의 오프라인 시드 프로세스는 Azure [가져오기/내보내기 서비스와](../storage/common/storage-import-export-service.md)긴밀하게 통합됩니다. 이 서비스를 사용하여 디스크를 사용하여 Azure로 데이터를 전송할 수 있습니다. 대기 시간이 많은 네트워크와 낮은 대역폭 네트워크를 통해 전송해야 하는 초기 백업 데이터의 테라바이트(TB)가 있는 경우 오프라인 시드 워크플로를 사용하여 하나 이상의 하드 드라이브에서 초기 백업 복사본을 Azure 데이터 센터로 전송할 수 있습니다. 이 문서에서는 시스템 센터 데이터 보호 관리자(DPM) 및 Microsoft Azure 백업 서버(MABS)에 대한 이 워크플로를 완료하는 개요 및 추가 단계를 제공합니다.

> [!NOTE]
> Microsoft Azure 복구 서비스(MARS) 에이전트에 대한 오프라인 백업 프로세스는 DPM 및 MABS와 다릅니다. MARS 에이전트와 오프라인 백업 사용에 대한 자세한 내용은 [Azure Backup에서 오프라인 백업 워크플로를](backup-azure-backup-import-export.md)참조하십시오. Azure 백업 에이전트를 사용하여 수행되는 시스템 상태 백업에는 오프라인 백업이 지원되지 않습니다.
>

## <a name="overview"></a>개요

Azure Backup 및 Azure 가져오기/내보내기 서비스의 오프라인 시드 기능을 사용하면 디스크를 사용하여 데이터를 Azure에 오프라인으로 오프라인으로 업로드하는 것이 간단합니다. 오프라인 백업 프로세스는 다음과 같은 단계를 포함합니다.

> [!div class="checklist"]
>
> * 백업 데이터는 네트워크를 통해 전송되는 대신 스테이징 위치에 기록됩니다.
> * 그런 다음 스테이징 위치의 데이터는 *AzureOfflineBackupDiskPrep* 유틸리티를 사용하여 하나 이상의 SATA 디스크에 기록됩니다.
> * Azure 가져오기 작업은 유틸리티에 의해 자동으로 만들어집니다.
> * 그러면 SATA 드라이브가 가장 가까운 Azure 데이터 센터로 전송됩니다.
> * Azure에 백업 데이터 업로드를 완료한 후 Azure Backup이 백업 데이터를 백업 자격 증명 모음에 복사하고 증분 백업이 예약됩니다.

## <a name="supported-configurations"></a>지원되는 구성

온-프레미스에서 Microsoft 클라우드로 데이터를 백업하는 Azure Backup의 모든 배포 모델에 대해 오프라인 백업이 지원됩니다. 이러한 모델에는 다음이 포함됩니다.

> [!div class="checklist"]
>
> * MARS 에이전트 또는 Azure 백업 에이전트를 사용 하 여 파일 및 폴더의 백업입니다.
> * DPM을 가진 모든 워크로드 및 파일의 백업.
> * MABS를 통해 모든 워크로드 및 파일 백업.

## <a name="prerequisites"></a>사전 요구 사항

오프라인 백업 워크플로를 시작하기 전에 다음 필수 구성 조건이 충족되었는지 확인합니다.

* [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)이 만들어졌습니다. 하나를 만들려면 [복구 서비스 자격 증명 모음 만들기의](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) 단계를 따릅니다.
* Azure 백업 에이전트 또는 MABS 또는 DPM은 해당되는 경우 Windows 서버 또는 Windows 클라이언트에 설치되었으며 컴퓨터가 복구 서비스 자격 증명 모음에 등록됩니다. [최신 버전의 Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525)만 사용되는지 확인합니다.
* 데이터를 백업하려는 컴퓨터에서 [Azure 게시 설정 파일을 다운로드합니다.](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) 게시 설정 파일을 다운로드하는 구독은 복구 서비스 자격 증명 모음이 포함된 구독과 다를 수 있습니다. 구독이 주권 Azure 클라우드에 있는 경우 다음 링크를 적절히 사용하여 Azure 게시 설정 파일을 다운로드합니다.

    | 소버린 클라우드 지역 | Azure 게시 설정 파일 링크 |
    | --- | --- |
    | 미국 | [링크](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 중국 | [링크](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* 리소스 관리자 배포 모델이 있는 Azure 저장소 계정이 게시 설정 파일을 다운로드한 구독에서 만들어졌습니다.

  ![리소스 관리자 개발을 사용하여 저장소 계정 만들기](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* 네트워크 공유 또는 컴퓨터의 추가 드라이브에 있을 수 있는, 초기 복사본을 저장할 충분한 디스크 공간이 있는 내부 또는 외부의 스테이징 위치가 생성됩니다. 예를 들어 500GB 파일 서버를 백업하려면 준비 영역이 500GB 이상인지 확인합니다. (압축으로 인해 더 작은 양이 사용됩니다.)
* Azure로 전송되는 디스크의 경우 2.5인치 SSD 또는 2.5인치 또는 3.5인치 SATA II/III 내부 하드 드라이브만 사용해야 합니다. 최대 10TB의 하드 드라이브를 사용할 수 있습니다. [Azure Import/Export 서비스 설명서](../storage/common/storage-import-export-requirements.md#supported-hardware)에서 서비스가 지원하는 최신 드라이브를 집합을 확인하세요.
* SATA 드라이브는 준비 위치에서 SATA 드라이브로 백업 데이터의 복사가 수행되는 컴퓨터(*복사 컴퓨터*라고 함)에 연결되어야 합니다. 복사 컴퓨터에서 BitLocker를 사용하도록 설정되어 있는지 확인합니다.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>오프라인 백업 프로세스에 대한 서버 준비

>[!NOTE]
> MARS 에이전트 설치시 나열된 유틸리티(예: *AzureOfflineBackupCertGen.exe)를*찾을 수 없는 경우 AskAzureBackupTeam@microsoft.com 해당 유틸리티에 액세스하려면 서면으로 작성합니다.

* 서버에서 상승된 명령 프롬프트를 열고 다음 명령을 실행합니다.

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    도구가 존재하지 않는 경우 Azure 오프라인 백업 활성 디렉터리 응용 프로그램을 만듭니다.

    응용 프로그램이 이미 있는 경우 이 실행 프로그램은 테넌트의 응용 프로그램에 인증서를 수동으로 업로드하도록 요청합니다. [이 섹션의](#manually-upload-an-offline-backup-certificate) 단계에 따라 인증서를 응용 프로그램에 수동으로 업로드합니다.

* *AzureOfflineBackup.exe* 도구는 *오프라인 응용 프로그램Params.xml* 파일을 생성합니다. 이 파일을 MABS 또는 DPM을 사용하여 서버에 복사합니다.
* DPM 인스턴스 또는 Azure 백업 서버에 [최신 MARS 에이전트를](https://aka.ms/azurebackup_agent) 설치합니다.
* Azure에 서버를 등록합니다.
* 다음 명령 실행:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* 이전 명령은 파일을 `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`만듭니다.

## <a name="manually-upload-an-offline-backup-certificate"></a>오프라인 백업 인증서를 수동으로 업로드

다음 단계에 따라 오프라인 백업을 위해 이전에 만든 Azure Active Directory 응용 프로그램에 오프라인 백업 인증서를 수동으로 업로드합니다.

1. Azure Portal에 로그인합니다.
1. Azure **Active Directory** > **앱 등록으로 이동합니다.**
1. 소유 **응용 프로그램** 탭에서 표시 이름 형식이 `AzureOfflineBackup _<Azure User Id`있는 응용 프로그램을 찾습니다.

    ![소유 응용 프로그램 탭에서 응용 프로그램 찾기](./media/backup-azure-backup-import-export/owned-applications.png)

1. 애플리케이션을 선택합니다. 왼쪽 창에서 **관리** 아래에서 **인증서 & 비밀로 이동합니다.**
1. 기존 인증서 또는 공개 키를 확인합니다. 없는 경우 응용 프로그램의 **개요** 페이지에서 **삭제** 단추를 선택하여 응용 프로그램을 안전하게 삭제할 수 있습니다. 그런 다음 단계를 다시 시도하여 [오프라인 백업 프로세스에 대한 서버를 준비하고](#prepare-the-server-for-the-offline-backup-process) 다음 단계를 건너뛸 수 있습니다. 그렇지 않으면 오프라인 백업을 구성하려는 DPM 인스턴스 또는 Azure Backup 서버에서 다음 단계를 계속 수행합니다.
1. 컴퓨터 **인증서 관리 응용 프로그램** > **개인** 탭을 선택합니다. `CB_AzureADCertforOfflineSeeding_<ResourceId>`
1. 인증서를 선택하고 모든 **작업을**마우스 오른쪽 단추로 클릭한 다음 .cer 형식으로 개인 키 없이 **내보내기를**선택합니다.
1. Azure 포털의 Azure 오프라인 백업 응용 프로그램으로 이동합니다.
1. 인증서 **&** > 비밀 > **업로드 인증서**관리를**선택합니다.** 이전 단계에서 내보낸 인증서를 업로드합니다.

    ![인증서 업로드](./media/backup-azure-backup-import-export/upload-certificate.png)

1. 서버에서 실행 창에 **regedit를** 입력하여 레지스트리를 엽니다.
1. 레지스트리 *항목으로 이동컴퓨터\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure 백업\Config\CloudBackupProvider*.
1. 오른쪽 단추로 클릭 **CloudBackupProvider**및 이름으로 `AzureADAppCertThumbprint_<Azure User Id>`새 문자열 값을 추가 합니다.

    >[!NOTE]
    > Azure 사용자 ID를 찾으려면 다음 단계 중 하나를 수행합니다.
    >
    >* Azure 연결 PowerShell에서 명령을 `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` 실행합니다.
    >* 레지스트리 경로로 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`이동합니다.

1. 이전 단계에서 추가된 문자열을 마우스 오른쪽 단추로 클릭하고 **에서 수정을**선택합니다. 값에서 7단계에서 내보낸 인증서의 지문을 제공합니다. 그런 다음 **확인을**선택합니다.
1. 지문 값을 얻으려면 인증서를 두 번 클릭합니다. 세부 **정보** 탭을 선택하고 지문 필드가 표시될 때까지 아래로 스크롤합니다. 지문을 선택하고 값을 **복사합니다.**

    ![지문 필드에서 복사 값](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. [워크플로](#workflow) 섹션을 계속하여 오프라인 백업 프로세스를 진행합니다.

## <a name="workflow"></a>워크플로

이 섹션의 정보는 데이터를 Azure 데이터 센터에 전달하고 Azure Storage에 업로드할 수 있도록 오프라인 백업 워크플로를 완료하는 데 도움이 됩니다. 가져오기 서비스 또는 프로세스의 모든 측면에 대한 질문이 있는 경우 앞에서 참조한 [가져오기 서비스 개요 설명서를](../storage/common/storage-import-export-service.md) 참조하십시오.

### <a name="initiate-offline-backup"></a>오프라인 백업 시작

1. 백업을 예약하면 Windows 서버, Windows 클라이언트 또는 DPM에 다음 페이지가 표시됩니다.

    ![가져오기 페이지](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    DPM의 해당 페이지는 다음과 같습니다. <br/>
    
    ![DPM 및 Azure 백업 서버 가져오기 페이지](./media/backup-azure-backup-import-export/dpmoffline.png)

    입력하는 상자는 다음과 같습니다.

   * **스테이징 위치**: 초기 백업 복사본을 쓸 임시 스토리지 위치입니다. 스테이징 위치는 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 다른 경우 스테이징 위치의 전체 네트워크 경로를 지정합니다.
   * **Azure 게시 설정**: 게시 설정 파일에 대한 로컬 경로입니다.
   * **Azure 가져오기 작업 이름:** Azure 가져오기/내보내기 서비스 및 Azure Backup이 디스크에서 Azure로 전송되는 데이터 전송을 추적하는 고유 이름입니다.
   * **Azure 구독 ID**: Azure 게시 설정 파일을 다운로드한 위치의 구독에 대한 Azure 구독 ID입니다.
   * **Azure 저장소 계정**: Azure 게시 설정 파일과 연결된 Azure 구독의 저장소 계정 이름입니다.
   * **Azure Storage 컨테이너**: 백업 데이터를 가져올 Azure 스토리지 계정의 대상 스토리지 Blob 이름입니다.

   준비 **위치** 및 제공한 **Azure 가져오기 작업 이름** 정보를 저장합니다. 디스크를 준비해야 합니다.

1. 워크플로를 완료합니다. 오프라인 백업 복사본을 시작하려면 Azure 백업 에이전트 관리 콘솔에서 **지금 백업을** 선택합니다. 초기 백업은 이 단계의 일환으로 준비 영역에 기록됩니다.

    ![지금 Backup](./media/backup-azure-backup-import-export/backupnow.png)

    DPM 또는 Azure 백업 서버에서 해당 워크플로를 완료하려면 **보호 그룹을**마우스 오른쪽 단추로 클릭합니다. 복구 **지점 만들기** 옵션을 선택합니다. 그런 다음 **온라인 보호** 옵션을 선택합니다.

    ![DPM 및 MABS 지금 백업](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    작업이 완료되면 스테이징 위치를 디스크 준비에 사용할 수 있습니다.

    ![Backup 진행](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA 드라이브 준비 및 Azure에 제공

*AzureOfflineBackupDiskPrep* 유틸리티는 가장 가까운 Azure 데이터 센터로 전송되는 SATA 드라이브를 준비하는 데 사용됩니다. 이 유틸리티는 다음 경로의 복구 서비스 에이전트 의 설치 디렉터리에서 사용할 수 있습니다.

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. 디렉터리로 이동하고 준비할 SATA 드라이브가 연결될 복사 컴퓨터에 *AzureOfflineBackupDiskPrep* 디렉터리를 복사합니다. 다음 사항을 확인합니다.

   * 복사 컴퓨터는 "오프라인 백업 시작" 섹션에서 워크플로에 제공된 것과 동일한 네트워크 경로를 사용하여 오프라인 시드 워크플로의 스테이징 위치에 액세스할 수 있습니다.
   * BitLocker가 복사 컴퓨터에서 활성화됩니다.
   * 복사 컴퓨터는 Azure Portal에 액세스할 수 있습니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 같을 수 있습니다.

     > [!IMPORTANT]
     > 원본 컴퓨터가 가상 컴퓨터인 경우 다른 물리적 서버 또는 클라이언트 컴퓨터를 복사 컴퓨터로 사용해야 합니다.

1. 현재 디렉터리로 *AzureOfflineBackupDiskPrep* 유틸리티 디렉토리를 사용 하 여 복사 컴퓨터에서 높은 명령 프롬프트를 엽니다. 다음 명령 실행:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | 매개 변수 | 설명 |
    | --- | --- |
    | s:&lt;*위치 경로 준비*&gt; |이 필수 입력은 "오프라인 백업 시작" 섹션에서 워크플로에 입력한 스테이징 위치에 대한 경로를 제공하는 데 사용됩니다. |
    | p:&lt;*게시 설정 파일 경로*&gt; |이 선택적 입력은 "오프라인 백업 시작" 섹션에서 워크플로에 입력한 Azure 게시 설정 파일에 대한 경로를 제공하는 데 사용됩니다. |

    > [!NOTE]
    > 복사 컴퓨터 및 원본 컴퓨터가 서로 다른 경우 &lt;AzurePublishSettingFile에 대한 경로&gt; 값은 필수입니다.
    >
    >

    명령을 실행하면 유틸리티는 준비해야 하는 드라이브에 해당하는 Azure 가져오기 작업의 선택을 요청합니다. 단일 가져오기 작업만 제공된 스테이징 위치와 연결된 경우 이와 같은 페이지가 표시됩니다.

    ![Azure 디스크 준비 도구 입력](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Azure에 전송하도록 준비하려는 탑재된 디스크에 대한 드라이브 문자를 후행 콜론 없이 입력합니다. 메시지가 표시되면 드라이브의 서식지정을 확인합니다.

    그런 다음 도구가 디스크를 준비하고 백업 데이터를 복사하기 시작합니다. 제공된 디스크에 백업 데이터에 대한 공간이 충분하지 않은 경우 도구에서 프롬프트를 받으면 추가 디스크를 연결해야 할 수 있습니다. <br/>

    도구가 성공적으로 완료되면 제공한 하나 이상의 디스크가 Azure로 배송될 준비가 됩니다. "오프라인 백업 시작" 섹션에서 워크플로 중에 제공한 이름의 가져오기 작업도 Azure에서 만들어집니다. 마지막으로 도구는 디스크를 배송해야 하는 Azure 데이터 센터에 대한 배송 주소를 표시합니다.

    ![Azure 디스크 준비 완료](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. 명령 실행이 끝나면 배송 정보를 업데이트하는 옵션도 표시됩니다.

    ![배송 정보 옵션 업데이트](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. 세부 정보를 바로 입력할 수 있습니다. 이 도구는 일련의 입력을 포함하는 프로세스를 안내합니다. 추적 번호 나 배송과 관련된 기타 세부 정보와 같은 정보가 없는 경우 세션을 종료할 수 있습니다. 배송 세부 정보를 나중에 업데이트하는 단계는 이 문서에서 제공됩니다.

1. 디스크를 도구가 제공한 주소로 발송합니다. 나중에 참조할 수 있도록 추적 번호를 유지합니다.

   > [!IMPORTANT]
   > 두 개의 Azure 가져오기 작업에 동일한 추적 번호가 있을 수 없습니다. 단일 Azure 가져오기 작업에서 유틸리티에서 준비한 드라이브가 단일 패키지로 함께 제공되고 패키지에 대한 고유 추적 번호가 하나 있는지 확인합니다. 단일 패키지에서 다른 Azure 가져오기 작업의 일부로 준비된 드라이브를 결합하지 마십시오.

1. 추적 번호 정보가 있으면 가져오기 작업 완료를 기다리는 원본 컴퓨터로 이동합니다. *AzureOfflineBackupDiskPrep* 유틸리티 디렉터리를 현재 디렉터리로 사용 하 고 상승 된 명령 프롬프트에서 다음 명령을 실행 합니다.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   선택적으로 복사 컴퓨터와 같은 다른 컴퓨터에서 *AzureOfflineBackupDiskPrep* 유틸리티 디렉터리를 현재 디렉터리로 실행할 수 있습니다.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | 매개 변수 | 설명 |
    | --- | --- |
    | u: | 이 필수 입력은 Azure 가져오기 작업에 대한 배송 세부 정보를 업데이트하는 데 사용됩니다. |
    | s:&lt;*위치 경로 준비*&gt; | 이 필수 입력은 원본 컴퓨터에서 명령이 실행되지 않을 때 사용됩니다. "오프라인 백업 시작" 섹션에서 워크플로에 입력한 스테이징 위치에 대한 경로를 제공하는 데 사용됩니다. |
    | p:&lt;*게시 설정 파일 경로*&gt; | 이 필수 입력은 원본 컴퓨터에서 명령이 실행되지 않을 때 사용됩니다. "오프라인 백업 시작" 섹션에서 워크플로에 입력한 Azure 게시 설정 파일에 대한 경로를 제공하는 데 사용됩니다. |

    이 유틸리티는 원본 컴퓨터가 대기하고 있는 가져오기 작업또는 다른 컴퓨터에서 명령을 실행할 때 스테이징 위치와 연결된 가져오기 작업을 자동으로 감지합니다. 그런 다음 일련의 입력을 통해 배송 정보를 업데이트하는 옵션을 제공합니다.

    ![배송 정보 입력](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. 모든 입력이 제공된 후 세부 정보를 주의 깊게 검토하고 **yes를**입력하여 제공한 배송 정보를 커밋합니다.

    ![배송 정보 검토](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. 배송 정보가 성공적으로 업데이트되면 유틸리티는 입력한 배송 세부 정보가 저장되는 로컬 위치를 제공합니다.

    ![배송 정보 저장](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > *AzureOfflineBackupDiskPrep* 유틸리티를 사용하여 배송 정보를 제공한 후 2주 이내에 드라이브가 Azure 데이터 센터에 도달해야 합니다. 이렇게 하지 않으면 드라이브가 처리되지 않을 수 있습니다. 

이전 단계를 완료하면 Azure 데이터 센터가 드라이브를 수신하고 추가로 프로세스하여 드라이브에서 만든 클래식 형식 Azure 저장소 계정으로 백업 데이터를 전송할 준비가 된 것입니다.

### <a name="time-to-process-the-drives"></a>드라이브 처리 시간

Azure 가져오기 작업을 처리하는 데 걸리는 시간은 다양합니다. 프로세스 시간은 배송 시간, 작업 유형, 복사할 데이터의 유형 및 크기 및 제공된 디스크 크기와 같은 요인에 따라 달라집니다. Azure 가져오기/내보내기 서비스에 SLA가 없습니다. 디스크를 받은 후 서비스는 7~10일 이내에 Azure 저장소 계정에 대한 백업 데이터 복사본을 완료하려고 노력합니다. 다음 섹션에서는 Azure 가져오기 작업의 상태를 모니터링하는 방법에 대해 설명합니다.

### <a name="monitor-azure-import-job-status"></a>Azure 가져오기 작업 상태 모니터링

드라이브가 전송 중이거나 Azure 데이터 센터에서 저장소 계정으로 복사되는 동안 원본 컴퓨터의 Azure 백업 에이전트 또는 DPM 또는 MABS 콘솔에는 예약된 백업에 대한 다음 작업 상태가 표시됩니다.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

가져오기 작업 상태를 확인하려면 다음을 수행합니다.

1. 원본 컴퓨터에서 상승된 명령 프롬프트를 열고 다음 명령을 실행합니다.

     `AzureOfflineBackupDiskPrep.exe u:`

1. 출력에는 가져오기 작업의 현재 상태가 표시됩니다.

    ![가져오기 작업 상태 확인](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Azure 가져오기 작업의 다양한 상태에 대한 자세한 내용은 [Azure 가져오기/내보내기 작업의 상태 보기를](../storage/common/storage-import-export-view-drive-status.md)참조하십시오.

### <a name="finish-the-workflow"></a>워크플로 완료

가져오기 작업이 완료된 후 사용자의 스토리지 계정에서 초기 백업 데이터를 사용할 수 있습니다. 다음 예약된 백업 시 Azure Backup은 저장소 계정에서 복구 서비스 자격 증명 모음으로 데이터의 내용을 복사합니다.

   ![복구 서비스 자격 증명 모음에 데이터 복사](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

다음 예약된 백업 시 Azure Backup은 초기 백업 복사본을 통해 증분 백업을 수행합니다.

## <a name="next-steps"></a>다음 단계

* Azure 가져오기/내보내기 서비스 워크플로에 대한 질문은 [Microsoft Azure 가져오기/내보내기 서비스 사용을 사용하여 데이터를 Blob 저장소로 전송합니다.](../storage/common/storage-import-export-service.md)
