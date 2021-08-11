---
title: DPM(Data Protection Manager) 및 MABS(Microsoft Azure Backup Server) 이전 버전에 대한 오프라인 백업
description: Azure Backup을 사용하여 Azure Import/Export 서비스에서 네트워크를 통해 데이터를 내보낼 수 있습니다. 이 문서에서는 이전 버전의 DPM 및 Azure Backup Server에 대한 오프라인 백업 워크플로에 대해 설명합니다.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0405ab66b7714f00349419e94bb064267ca711a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702188"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>DPM 및 Azure Backup Server(이전 버전)에 대한 오프라인 백업 워크플로

>[!IMPORTANT]
>이러한 단계는 DPM 2019 RTM 및 이전 버전, MABS v3 RTM 및 이전 버전에 적용됩니다.

Azure Backup은 데이터를 Azure에 처음 전체 백업하는 동안 네트워크 및 스토리지 비용을 절약하는 여러 가지 기본 제공 효율성 향상 기능이 있습니다. 초기 전체 백업은 일반적으로 많은 양의 데이터를 전송하고 델타/증분만 전송하는 후속 백업과 비교할 때 더 많은 네트워크 대역폭이 필요합니다. Azure Backup은 초기 백업을 압축합니다. 오프라인 시드 프로세스를 통해 Azure Backup은 디스크를 사용하여 압축된 초기 백업 데이터를 Azure에 오프라인으로 업로드할 수 있습니다.

Azure Backup 오프라인 시드 프로세스는 [Azure Import/Export 서비스](../import-export/storage-import-export-service.md)와 긴밀하게 통합되어 있습니다. 디스크를 사용하여 Azure로 데이터를 전송하는 데 이 서비스를 사용할 수 있습니다. 긴 대기 시간 및 낮은 대역폭 네트워크를 통해 전송해야 하는 테라바이트(TB) 분량의 초기 백업 데이터가 있다면, 오프라인 시드 워크플로를 사용하여 하나 이상의 하드 드라이브에 있는 초기 백업 복사본을 Azure 데이터 센터로 보낼 수 있습니다. 이 문서는 System Center DPM(Data Protection Manager) 및 MABS(Microsoft Azure Backup Server)에 대한 워크플로를 완료하는 개요 및 세부 단계를 제공합니다.

> [!NOTE]
> Microsoft Azure Recovery Services(MARS) 에이전트에 대한 오프라인 백업 프로세스는 DPM 및 MABS과 다릅니다. MARS 에이전트를 사용하여 오프라인 백업을 사용하는 방법에 대한 자세한 내용은 [Azure Backup 오프라인 백업 워크플로](backup-azure-backup-import-export.md)를 참조하세요. 오프라인 백업은 Azure Backup 에이전트를 사용하여 수행된 시스템 상태 백업에 대해서는 지원되지 않습니다.
>

## <a name="overview"></a>개요

Azure Backup의 오프라인 시드 기능 및 Azure Import/Export 서비스를 사용하면 디스크를 사용하여 오프라인으로 간단하게 Azure에 데이터를 업로드할 수 있습니다. 오프라인 백업 프로세스는 다음과 같은 단계를 포함합니다.

> [!div class="checklist"]
>
> * 백업 데이터가 네트워크를 통해 전송되는 대신 스테이징 위치에 기록됩니다.
> * 그런 다음, 스테이징 위치의 데이터는 *AzureOfflineBackupDiskPrep* 유틸리티를 사용하여 하나 이상의 SATA 디스크에 기록됩니다.
> * Azure 가져오기 작업은 유틸리티에 의해 자동으로 생성됩니다.
> * SATA 드라이브는 가장 가까운 Azure 데이터 센터로 전송됩니다.
> * Azure에 백업 데이터를 업로드하는 작업이 완료되면 Azure Backup에서 백업 데이터를 백업 자격 증명 모음에 복사하고 증분 백업이 예약됩니다.

## <a name="supported-configurations"></a>지원되는 구성

오프라인 백업은 온-프레미스에서 Microsoft 클라우드로 데이터를 백업하는 Azure Backup의 모든 배포 모델에 대해 지원됩니다. 해당 모델은 다음과 같습니다.

> [!div class="checklist"]
>
> * MARS 에이전트 또는 Azure Backup에이전트를 사용한 파일 및 폴더 백업.
> * DPM을 사용한 모든 워크로드 및 파일의 백업.
> * MABS를 사용한 모든 워크로드 및 파일의 백업.

>[!NOTE]
>Azure CSP 구독은 DPM 2019 RTM 및 이전 버전, MABS v3 RTM 및 이전 버전에 대한 오프라인 시드에 사용하도록 지원되지 않습니다. 네트워크를 통한 온라인 백업은 계속 지원됩니다.

## <a name="prerequisites"></a>사전 요구 사항

오프라인 백업 워크플로를 시작하기 전에 다음 필수 구성 요소가 충족되는지 확인합니다.

* [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)이 생성되었습니다. 생성하려면 [Recovery Services 자격 증명 모음 생성하기](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)의 단계를 따르세요
* Azure Backup 에이전트, MABS, DPM이 해당되는 Windows Server 또는 Windows 클라이언트에 설치되었고 컴퓨터가 Recovery Services 자격 증명 모음으로 등록됩니다. [최신 버전의 Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525)만 사용되는지 확인합니다.
* 데이터를 백업하려는 컴퓨터에 [Azure 게시 설정 파일을 다운로드](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)합니다. 게시 설정 파일을 다운로드하는 구독은 Recovery Services 자격 증명 모음을 포함하는 구독과 다를 수 있습니다. 구독이 소버린 Azure 클라우드에 있는 경우, 다음 링크를 적절하게 사용하여 Azure 게시 설정 파일을 다운로드합니다.

    | 소버린 클라우드 지역 | Azure 게시 설정 파일 링크 |
    | --- | --- |
    | 미국 | [링크](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 중국 | [링크](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* 게시 설정 파일을 다운로드한 구독에서 리소스 관리자 배포 모델을 사용하는 Azure storage 계정이 생성되었습니다. 스토리지 계정에서 대상으로 사용될 새 blob 컨테이너를 생성합니다.

  ![리소스 관리자 개발을 사용하여 스토리지 계정 생성하기](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* 초기 복사본을 저장하기 위한 충분한 디스크 공간의 컴퓨터 내부 또는 외부에 네트워크 공유 또는 추가 드라이브일 수 있는 준비 위치가 생성됩니다. 예를 들어, 500GB 파일 서버를 백업하려는 경우 준비 영역이 500GB인지 확인합니다. (압축으로 인해 더 적은 양이 사용됩니다.)
* Azure로 전송되는 디스크의 경우, 2.5인치 SSD, 2.5인치 또는 3.5인치 SATA II/III 내장 하드 드라이브가 사용되는지 확인합니다. 최대 10TB의 하드 드라이브를 사용할 수 있습니다. 서비스에서 지원하는 최신 드라이브 집합의 경우 [Azure Import/Export 서비스 설명서](../import-export/storage-import-export-requirements.md#supported-hardware)를 확인하세요.
* SATA 드라이브는 준비 위치에서 SATA 드라이브로 백업 데이터의 복사가 수행되는 컴퓨터(*복사 컴퓨터* 라고 함)에 연결되어야 합니다. BitLocker가 복사 컴퓨터에서 사용하도록 설정되어 있는지 확인합니다.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>오프라인 백업 프로세스를 위한 서버 준비

>[!NOTE]
> MARS 에이전트를 설치 시, *AzureOfflineBackupCertGen.exe* 와 같은 등록 유틸리티를 찾을 수 없는 경우에, AskAzureBackupTeam@microsoft.com에 작성하여 액세스 권한을 얻습니다.

* 서버에서 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행합니다.

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    이 도구는 Azure Offline Backup Active Directory 응용 프로그램을 생성합니다(없는 경우).

    응용 프로그램이 이미 있는 경우, 이 실행 파일은 테넌트에서 응용 프로그램에 인증서를 수동으로 업로드 하라는 메시지를 표시합니다. [이 섹션](#manually-upload-an-offline-backup-certificate)의 단계에 따라 인증서를 응용 프로그램에 수동으로 업로드합니다.

* *AzureOfflineBackupCertGen.exe* 도구는 *OfflineApplicationParams.xml* 파일을 생성합니다. MABS 또는 DPM을 사용하여 이 파일을 서버에 복사합니다.
* DPM 인스턴스 또는 Azure Backup 서버에 [최신 MARS 에이전트](https://aka.ms/azurebackup_agent)를 설치합니다.
* Azure에 서버를 등록합니다.
* 다음 명령 실행:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* 이전 명령이 `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` 파일을 생성합니다.

## <a name="manually-upload-an-offline-backup-certificate"></a>오프라인 백업 인증서 수동 업로드

오프라인 백업을 위해 이전에 생성한 Azure Active Directory 응용 프로그램에 오프라인 백업 인증서를 수동으로 업로드하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인합니다.
1. **Azure Active Directory** > **앱 등록** 으로 이동합니다.
1. **소유한 응용 프로그램** 탭에서 표시 이름 형식이 `AzureOfflineBackup _<Azure User Id`인 응용 프로그램을 찾습니다.

    ![소유한 응용 프로그램 탭에 응용 프로그램 배치](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. 애플리케이션을 선택합니다. 왼쪽 창의 **관리** 에서 **인증서 및 비밀** 을 선택합니다.
1. 기존 인증서 또는 공개 키를 확인합니다. 없는 경우 응용 프로그램의 **개요** 페이지에서 **삭제** 단추를 선택하여 응용 프로그램을 안전하게 삭제할 수 있습니다. 그런 다음 [오프라인 백업 프로세스를 위한 서버 준비](#prepare-the-server-for-the-offline-backup-process) 단계를 다시 시도하고 다음 단계를 건너뛸 수 있습니다. 그렇지 않으면, 오프라인 백업을 구성하려는 DPM 인스턴스 또는 Azure Backup 서버에서 이러한 단계를 계속 수행합니다.
1. **시작** 에서 **실행** 까지 *Certlm.msc* 를 입력합니다. **인증서 - 로컬 컴퓨터** 창에서 **인증서 – 로컬 컴퓨터** > **개인** 탭을 선택합니다. 이름이 `CB_AzureADCertforOfflineSeeding_<ResourceId>`인 인증서를 찾습니다.
1. 해당 인증서를 선택하고 **모든 작업** 을 마우스 오른쪽 클릭하여 개인 키 없이 .cer 형식으로 **내보내기** 를 선택합니다.
1. Azure Portal의 Azure 오프라인 백업 응용 프로그램으로 이동합니다.
1. **관리** > **인증서 및 비밀** > **인증서 업로드** 를 선택합니다. 이전 단계에서 내보낸 인증서를 업로드합니다.

    ![인증서 업로드](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. 서버에서 실행 창에 **regedit** 를 입력하여 레지스트리를 엽니다.
1. 레지스트리 항목 *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* 로 이동합니다.
1. **CloudBackupProvider** 를 마우스 오른쪽 단추로 클릭하고 이름이 `AzureADAppCertThumbprint_<Azure User Id>`인 새 문자열 값을 추가합니다.

    >[!NOTE]
    > Azure 사용자 ID를 찾으려면 다음 단계 중 하나를 수행합니다.
    >
    >* Azure에 연결된 PowerShell에서 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` 명령을 실행합니다.
    >* 레지스트리 경로 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`입니다.

1. 이전 단계에서 추가한 문자열을 마우스 오른쪽 단추로 클릭하고 **수정** 을 선택합니다. 7단계에서 내보낸 인증서 지문을 값에 입력합니다. 그런 다음, **확인** 을 선택합니다.
1. 지문 값을 가져오려면 인증서를 두 번 클릭합니다. **세부 정보** 탭을 선택하고 지문 필드가 보일 때까지 아래로 스크롤합니다. **지문** 을 선택하고 값을 복사합니다.

    ![지문 필드에서 값 복사](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. [워크플로](#workflow) 섹션으로 계속 진행하여 오프라인 백업 프로세스를 진행합니다.

## <a name="workflow"></a>워크플로

이 섹션에 제공된 정보는 데이터를 Azure 데이터 센터에 전송하고 Azure Storage에 업로드할 수 있도록 오프라인 백업 워크플로를 완료하는 데 도움을 줍니다. 가져오기 서비스 또는 프로세스의 모든 측면에 대한 질문이 있으면 앞에서 언급한 [가져오기 서비스 개요](../import-export/storage-import-export-service.md)를 참조하세요.

### <a name="initiate-offline-backup"></a>오프라인 백업 시작

1. 백업을 예약하면 Windows Server, Windows 클라이언트, DPM에 다음 페이지가 표시됩니다.

    ![가져오기 페이지](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    DPM의 해당 페이지는 다음과 같습니다. <br/>

    ![DPM 및 Azure Backup Server 가져오기 페이지](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    입력할 입력란은 다음과 같습니다.

   * **준비 위치**: 초기 백업 복사본을 쓸 임시 스토리지 위치입니다. 스테이징 위치는 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 서로 다르면 스테이징 위치의 전체 네트워크 경로를 지정합니다.
   * **Azure 게시 설정**: 게시 설정 파일에 대한 로컬 경로입니다.
   * **Azure 가져오기 작업 이름**: Azure Import/Export 서비스 및 Azure Backup이 디스크에서 Azure에 보내지는 데이터의 전송을 추적하는 고유한 이름입니다.
   * **Azure 구독 ID**: Azure 게시 설정 파일을 다운로드한 구독에 대한 Azure 구독 ID입니다.
   * **Azure Storage 계정**: Azure 게시 설정 파일과 연결된 Azure 구독의 스토리지 계정 이름입니다.
   * **Azure Storage 컨테이너**: 백업 데이터를 가져올 Azure 스토리지 계정의 대상 스토리지 Blob 이름입니다.

   제공된 **스테이징 위치** 및 **Azure 가져오기 작업 이름** 정보를 저장합니다. 이는 디스크를 준비하는 데 필요합니다.

1. 워크플로를 완료합니다. 오프라인 백업 복사를 시작하려면 Azure Backup 에이전트 관리 콘솔에서 **지금 백업** 을 선택합니다. 초기 백업은 이 단계의 일환으로 준비 영역에 기록됩니다.

    ![지금 백업](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    DPM 또는 Azure Backup Server에서 해당 워크플로를 완료하려면 **보호 그룹** 을 마우스 오른쪽 클릭합니다. **복구 지점 생성하기** 옵션을 선택합니다. 그런 다음 **온라인 보호** 옵션을 선택합니다.

    ![DPM 및 MABS 백업](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    작업이 완료되면 준비 위치를 디스크 준비에 사용할 수 있습니다.

    ![Backup 진행](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA 드라이브 준비 및 Azure로 배송

*AzureOfflineBackupDiskPrep* 유틸리티는 가장 가까운 Azure 데이터 센터에 전송되는 SATA 드라이브를 준비하는 데 사용됩니다. 이 유틸리티는 다음 경로에서 Recovery Services 에이전트의 설치 디렉터리에서 사용할 수 있습니다.

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. 디렉터리로 이동하고 준비할 SATA 드라이브가 연결될 복사 컴퓨터에 *AzureOfflineBackupDiskPrep* 디렉터리를 복사합니다. 다음 사항을 확인합니다.

   * 복사 컴퓨터는 "오프라인 백업 시작" 섹션의 워크플로에 제공된 것과 같은 네트워크 경로를 사용하여 오프라인 시드 워크플로에 대해 제공된 스테이징 위치에 액세스할 수 있습니다.
   * 복사 컴퓨터에서 BitLocker가 사용 설정되어 있습니다.
   * 복사 컴퓨터는 Azure Portal에 액세스할 수 있습니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 동일할 수 있습니다.

     > [!IMPORTANT]
     > 원본 컴퓨터가 가상 머신인 경우 복사 컴퓨터로 다른 실제 서버 또는 클라이언트 컴퓨터를 사용해야 합니다.

1. *AzureOfflineBackupDiskPrep* 유틸리티 디렉터리를 현재 디렉터리로 하여 관리자 권한 명령 프롬프트를 복사 컴퓨터에서 엽니다. 다음 명령 실행:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | 매개 변수 | 설명 |
    | --- | --- |
    | s:&lt;*Staging Location Path*&gt; |이 필수 입력은 "오프라인 백업 시작" 섹션의 워크플로에 입력한 스테이징 위치에 대한 경로를 제공하는 데 사용됩니다. |
    | p:&lt;*Path to PublishSettingsFile*&gt; |이 선택적 입력은 "오프라인 백업 시작" 섹션의 워크플로에 입력한 Azure 게시 설정 파일의 경로를 제공하는 데 사용됩니다. |

    > [!NOTE]
    > 복사 컴퓨터 및 원본 컴퓨터가 서로 다른 경우 &lt;AzurePublishSettingFile에 대한 경로&gt; 값은 필수입니다.
    >
    >

    명령을 실행할 때 유틸리티에서 준비해야 할 드라이브에 해당하는 Azure 가져오기 작업의 선택을 요청합니다. 제공된 스테이징 위치와 연결된 가져오기 작업이 하나만 있는 경우 다음과 같은 페이지가 나타납니다.

    ![Azure 디스크 준비 도구 입력](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Azure로 전송하기 위해 준비하려는 탑재된 디스크에 대해 후행 콜론 없이 드라이브 문자를 입력합니다. 메시지가 표시될 때, 드라이브 포맷에 대한 확인을 제공합니다.

    그런 다음, 도구가 디스크를 준비하고 백업 데이터 복사를 시작합니다. 제공된 디스크에 백업 데이터에 대한 충분한 공간이 없는 경우, 도구에서 메시지를 표시할 때 추가 디스크에 연결해야 할 수 있습니다. <br/>

    도구가 작업을 마치면 하나 이상의 디스크가 Azure에 배송되도록 준비됩니다. "오프라인 백업 시작" 섹션의 워크플로 중에 제공한 이름의 가져오기 작업이 Azure에도 생성됩니다. 마지막으로 도구는 디스크를 배송해야 하는 Azure 데이터 센터에 대한 배송 주소를 표시합니다.

    ![Azure 디스크 준비 완료](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. 명령 실행 마지막에 아래와 같이 배송 정보를 업데이트하는 옵션이 표시됩니다.

    ![배송 정보 업데이트 옵션](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. 세부 정보를 바로 입력할 수 있습니다. 이 도구는 일련의 입력이 필요한 프로세스를 안내합니다. 추적 번호와 같은 정보 또는 배송과 관련된 세부 정보가 없는 경우, 세션을 종료할 수 있습니다. 배송 세부 정보를 나중에 업데이트하는 단계는 이 문서에서 제공됩니다.

1. 디스크를 도구가 제공한 주소로 배송합니다. 나중에 참조할 수 있도록 추적 번호를 유지합니다.

   > [!IMPORTANT]
   > 두 개의 Azure 가져오기 작업은 동일한 추적 번호를 가질 수 없습니다. 단일 Azure 가져오기 작업 아래의 유틸리티에서 준비한 드라이브가 단일 패키지로 함께 배송되고, 패키지에 대한 고유한 단일 추적 번호가 있는지 확인합니다. 준비된 드라이브를 단일 패키지에서 다른 Azure 가져오기 작업의 일부와 결합하지 않습니다.

1. 추적 번호 정보가 있으면 가져오기 작업이 완료될 때까지 대기 중인 원본 컴퓨터로 이동합니다. *AzureOfflineBackupDiskPrep* 유틸리티 디렉터리를 현재 디렉터리로 하여 관리자 권한 명령 프롬프트에서 다음 명령을 실행합니다.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   *AzureOfflineBackupDiskPrep* 유틸리티 디렉터리를 현재 디렉터리로 하여 복사 컴퓨터와 같은 다른 컴퓨터에서 다음 명령을 필요에 따라 실행할 수 있습니다.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | 매개 변수 | Description |
    | --- | --- |
    | u: | 이 필수 입력 사항은 Azure 가져오기 작업에 대한 배송 세부 정보를 업데이트하는 데 사용됩니다. |
    | s:&lt;*Staging Location Path*&gt; | 이 필수 입력 사항은 명령이 원본 컴퓨터에서 실행되지 않을 때 사용됩니다. 이는 "오프라인 백업 시작" 섹션에서 워크플로에 입력한 스테이징 위치의 경로를 제공하는 데 사용됩니다. |
    | p:&lt;*Path to PublishSettingsFile*&gt; | 이 필수 입력 사항은 명령이 원본 컴퓨터에서 실행되지 않을 때 사용됩니다. 이는 "오프라인 백업 시작" 섹션에서 워크플로에 입력한 Azure 게시 설정 파일의 경로를 제공하는 데 사용됩니다. |

    유틸리티는 원본 컴퓨터가 기다리는 가져오기 작업 또는 명령이 다른 컴퓨터에서 실행되는 경우 스테이징 위치와 관련된 가져오기 작업을 자동으로 검색합니다. 그런 다음, 일련의 입력을 통해 배송 정보를 업데이트하는 옵션을 제공합니다.

    ![배송 정보 입력](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. 모두 입력한 후, 세부 정보를 주의 깊게 검토하고, **예** 를 입력하여 제공한 배송 정보를 커밋합니다.

    ![배송 정보 검토](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. 배송 정보가 성공적으로 업데이트되면 유틸리티는 입력한 배송 정보가 저장되는 로컬 위치를 제공합니다.

    ![배송 정보 저장](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > *AzureOfflineBackupDiskPrep* 유틸리티를 사용하여 배송 정보를 제공한 후 2주 이내에 드라이브가 Azure 데이터 센터에 도착하는지 확인합니다. 이렇게 하지 않으면 드라이브가 처리되지 않을 수 있습니다.

이전 단계를 완료하면 Azure 데이터 센터는 드라이브를 받고 백업 데이터를 드라이브에서 사용자가 생성한 기본 형식 Azure Storage 계정에 전송하도록 처리할 준비가 됩니다.

### <a name="time-to-process-the-drives"></a>드라이브를 처리하는 시간

Azure 가져오기 작업을 처리하는 데 걸리는 시간은 모두 다릅니다. 프로세스 시간은 배송 시간, 작업 유형, 복사되는 데이터 유형 및 크기, 제공된 디스크 크기와 같은 다양한 요소에 따라 달라집니다. Azure Import/Export 서비스에는 SLA가 없습니다. 이 서비스는 디스크가 수신되고 7~10일 내에 Azure Storage 계정으로 백업 데이터 복사를 완료하려고 합니다. 다음 섹션은 Azure 가져오기 작업의 상태를 모니터링하는 방법에 대해 설명합니다.

### <a name="monitor-azure-import-job-status"></a>Azure 가져오기 작업 상태 모니터링

드라이브가 전송 중이거나 Azure 데이터 센터에서 스토리지 계정에 복사되는 동안, 원본 컴퓨터의 Azure Backup 에이전트, DPM, MABS 콘솔은 예약된 백업에 대해 다음과 같은 작업 상태를 표시합니다.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

가져오기 작업 상태를 확인하려면 다음을 수행합니다.

1. 원본 컴퓨터에서 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행합니다.

     `AzureOfflineBackupDiskPrep.exe u:`

1. 출력에는 가져오기 작업의 현재 상태가 표시됩니다.

    ![가져오기 작업 상태 확인](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Azure 가져오기 작업의 다양한 상태에 대한 자세한 내용은 [Azure Import/Export 작업 상태 보기](../import-export/storage-import-export-view-drive-status.md)를 참조하세요.

### <a name="finish-the-workflow"></a>워크플로 완료

가져오기 작업이 완료되면 스토리지 계정에서 초기 백업 데이터를 사용할 수 있습니다. 다음 예약 백업 시 Azure Backup은 데이터의 내용을 스토리지 계정에서 Recovery Services 자격 증명 모음으로 복사합니다.

   ![Recovery Services 자격 증명 모음으로 데이터 복사](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

다음 예약된 백업 시 Azure Backup은 초기 백업 복사본을 통해 증분 백업을 수행합니다.

## <a name="next-steps"></a>다음 단계

* Azure 가져오기/내보내기 서비스 워크플로에 대한 질문은 [Microsoft Azure Import/Export 서비스를 사용하여 Blob Storage에 데이터 전송](../import-export/storage-import-export-service.md)을 참조하세요.
