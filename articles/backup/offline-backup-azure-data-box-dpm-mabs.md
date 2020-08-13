---
title: DPM 및 MABS에 대 한 Azure Data Box를 사용 하 여 오프 라인 백업
description: Azure Data Box를 사용 하 여 DPM 및 MABS에서 초기 백업 데이터를 오프 라인으로 초기값으로 지정할 수 있습니다.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 8b585dc46eb2bdd54e48950ca861f0edc8f0a7ed
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88187044"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>DPM 및 MABS (미리 보기)에 대 한 Azure Data Box를 사용 하 여 오프 라인 시드

> [!NOTE]
> 이 기능은 Data Protection Manager (DPM) 2019 U R 2 이상에 적용 됩니다.<br><br>
> 이 기능은 현재 MABS (Microsoft Azure Backup 서버)에 대해 미리 보기로 제공 됩니다. MABS를 사용 하 여 오프 라인 시드를 위해 Azure Data Box를 사용 하는 데 관심이 있는 경우에 문의 [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) 하세요.

이 문서에서는 Azure Data Box를 사용 하 여 DPM 및 MABS에서 Azure Recovery Services vault로 초기 백업 데이터를 오프 라인으로 초기값을 설정 하는 방법을 설명 합니다.

[Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) 를 사용 하 여 네트워크를 사용 하지 않고 Recovery Services 자격 증명 모음에 대 한 대량 초기 DPM/mabs 백업의 초기값을 설정할 수 있습니다. 이 프로세스는 대기 시간이 긴 네트워크를 통해 많은 양의 백업 데이터를 온라인으로 이동 하는 시간 및 네트워크 대역폭을 절약 합니다. 이 기능은 현재 미리 보기로 제공됩니다.

Azure Data Box을 기반으로 하는 오프 라인 백업은 [Azure Import/Export 서비스를 기반으로 하는 오프 라인 백업에](backup-azure-backup-server-import-export.md)비해 두 가지 이점을 제공 합니다.

- 사용자 고유의 Azure 호환 디스크 및 커넥터를 사용할 필요가 없습니다. Azure Data Box는 선택한 [DATA BOX SKU](https://azure.microsoft.com/services/databox/data/)와 연결 된 디스크를 제공 합니다.

- Azure Backup (MARS 에이전트)는 Azure Data Box의 지원 되는 Sku에 백업 데이터를 직접 쓸 수 있습니다. 이 기능을 통해 초기 백업 데이터에 대 한 준비 위치를 프로 비전 할 필요가 없습니다. 또한 디스크에 해당 데이터를 포맷 하 고 복사 하는 유틸리티가 필요 하지 않습니다.

## <a name="supported-platforms"></a>지원 플랫폼

지원 되는 플랫폼은 다음과 같습니다.

- Windows Server 2019 64 비트 (Standard, Datacenter, Essentials)
- Windows Server 2016 64 비트 (Standard, Datacenter, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>백업 데이터 크기 및 지원 되는 Data Box Sku

다음 Data Box Sku가 지원 됩니다.

| 서버당 백업 데이터 크기 (MARS로 압축 후) \* | 지원 되는 Azure Data Box SKU |
| --- | --- |
| \<= 7.2 TB | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB 및 <= 80 TB\*\* | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\*일반적인 압축 요금은 10-20% 마다 다릅니다. <br>
\*\*[SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)단일 데이터 원본에 대해 80 TB 이상의 초기 백업 데이터를 필요로 하는 경우에 연락 하세요.

> [!IMPORTANT]
> 단일 데이터 원본의 초기 백업 데이터는 단일 Azure Data Box 또는 Azure Data Box 디스크에 포함 되어야 하며, 동일한 또는 다른 Sku의 여러 장치 간에 공유할 수 없습니다. 그러나 Azure Data Box는 여러 데이터 원본의 초기 백업을 포함할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

DPM/MABS에서 실행 중인 MARS 에이전트는 [최신 버전](https://aka.ms/azurebackup_agent) (2.0.9171.0 이상)으로 업그레이드 해야 합니다.

다음을 확인합니다.

### <a name="azure-subscription-and-required-permissions"></a>Azure 구독 및 필요한 권한

- 유효한 Azure 구독.
- 오프 라인 백업 정책을 수행 하려는 사용자는 Azure 구독의 소유자 여야 합니다.
- 데이터를 시드 해야 하는 Data Box 작업 및 Recovery Services 자격 증명 모음은 동일한 구독에서 사용할 수 있어야 합니다.
    > [!NOTE]
    > 대상 저장소 계정과 Recovery Services 자격 증명 모음은 동일한 지역에 있는 것이 좋습니다. 그러나 반드시 이렇게 해야 하는 것은 아닙니다.

### <a name="order-and-receive-the-data-box-device"></a>Data Box 장치 순서 및 수신

오프 라인 백업을 트리거하기 전에 필요한 Data Box 장치가 *배달* 된 상태 인지 확인 합니다. 요구 사항에 가장 적합 한 SKU를 주문 하려면 [백업 데이터 크기 및 지원 되는 Data Box sku](#backup-data-size-and-supported-data-box-skus) 를 참조 하세요. [이 문서의](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) 단계에 따라 Data Box 장치를 주문 하 고 수신 합니다.

> [!IMPORTANT]
> **계정 종류**에 대해 *blobstorage* 를 선택 하지 마세요. DPM/MABS 서버에는 *Blobstorage* 를 선택할 때 지원 되지 않는 페이지 blob을 지 원하는 계정이 필요 합니다. Azure Data Box 작업에 대 한 대상 저장소 계정을 만들 때 **계정 종류** 로 **저장소 v2 (범용 v2)** 를 선택 합니다.

![Azure databox 설정](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Azure Data Box 장치 설치

Azure Data Box 장치를 받은 후에는 주문한 Azure Data Box SKU에 따라 아래 해당 섹션의 단계를 수행 하 여 DPM/MABS 서버의 Data Box 장치를 설정 하 고 준비 하 여 초기 백업 데이터를 식별 하 고 전송 합니다.

### <a name="setup-azure-data-box-disk"></a>디스크 Azure Data Box 설정

하나 이상의 Azure Data Box 디스크 (최대 8TB)를 주문한 경우 [여기](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up) 에 설명 된 단계에 따라 Data Box 디스크의 압축을 풀고 연결 하 고 잠금을 해제 합니다.

> [!NOTE]
> DPM/MABS 서버에 USB 포트가 없을 수 있습니다. 이러한 시나리오에서는 Azure Data Box 디스크를 다른 서버/클라이언트에 연결 하 고 장치의 루트를 네트워크 공유로 표시할 수 있습니다.

## <a name="setup-azure-data-box"></a>설치 Azure Data Box

Azure Data Box (최대 100 TB)를 주문한 경우 [여기](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) 에 설명 된 단계에 따라 Data Box를 설정 합니다.

### <a name="mount-your-azure-data-box-as-local-system"></a>Azure Data Box를 로컬 시스템으로 탑재

DPM/MABS 서버는 시스템 컨텍스트에서 작동 하므로 Azure Data Box 연결 된 탑재 경로에 동일한 수준의 권한이 제공 되어야 합니다. NFS 프로토콜을 사용 하 여 Data Box 장치를 로컬 시스템으로 탑재할 수 있도록 아래 단계를 수행 합니다.

1. DPM/MABS 서버에서 NFS 용 클라이언트 기능을 사용 하도록 설정 합니다.
대체 원본 지정: *WIM: D: \Source\install\install.wim: 4*
2. **PSExec** 를에서 [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) DPM/mabs 서버로 다운로드 합니다.
3. 관리자 권한 명령 프롬프트를 열고 *PSExec.exe* 를 현재 디렉터리로 포함 하는 디렉터리를 사용 하 여 다음 명령을 실행 합니다.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. 위의 명령 결과로 열리는 명령 창은 로컬 시스템 컨텍스트에 있습니다. 이 명령 창을 사용 하 여 Azure 페이지 Blob 공유를 Windows Server의 네트워크 드라이브로 탑재 하는 단계를 실행할 수 있습니다.
5. NFS를 통해 Data Box 장치에 DPM/MABS 서버를 연결 하 고 로컬 시스템 명령 프롬프트에서 다음 명령을 실행 하 여 Azure 페이지 Blob 공유를 탑재 하려면 [여기](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) 의 단계를 따르세요.

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. 탑재 되 면 서버에서 X:에 액세스할 수 있는지 확인 합니다. 가능 하면이 문서의 다음 섹션을 계속 진행 합니다.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Azure Data Box 장치에 초기 백업 데이터 전송

1. DPM/MABS 서버에서 [새 보호 그룹을 만드는](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019)단계를 수행 합니다. 기존 보호 그룹에 온라인 보호를 추가 하는 경우 기존 보호 그룹을 마우스 오른쪽 단추로 클릭 하 고 **온라인 보호 추가** 를 선택 하 고 **8 단계**에서 시작 합니다.
2. **그룹 구성원 선택** 페이지에서 백업 하려는 컴퓨터 및 원본을 지정 합니다.
3. **데이터 보호 방법 선택** 페이지에서 단기 및 장기 백업을 처리할 방법을 지정 합니다. 온라인 보호를 사용 하도록 선택 해야 **합니다.**

   ![새 보호 그룹 만들기](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. **단기 목표 선택** 페이지에서 디스크의 단기 저장소에 백업 하는 방법을 지정 합니다.
5. **디스크 할당 검토** 페이지에서 보호 그룹에 할당 된 저장소 풀 디스크 공간을 검토 합니다.
6. **복제본 만들기 방법 선택** 페이지에서 **네트워크를 통해 자동으로를 선택 합니다.**
7. **일관성 확인 옵션 선택** 페이지에서 일관성 확인을 자동화 하는 방법을 선택 합니다.
8. **온라인 보호 데이터 지정** 페이지에서 온라인 보호를 사용 하도록 설정할 구성원을 선택 합니다.

    ![온라인 보호 데이터 지정](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. **온라인 백업 일정 지정** 페이지에서 Azure에 대 한 증분 백업이 발생 하는 빈도를 지정 합니다.
10. **온라인 보존 정책 지정** 페이지에서 일별/주별/월별/연도별 백업에서 만들어진 복구 지점이 Azure에서 유지 되는 방식을 지정 합니다.
11. 마법사의 **온라인 복제 선택** 화면에서 **Microsoft 소유의 디스크를 사용 하 여 전송** 옵션을 선택 하 고 **다음**을 선택 합니다.

    ![초기 온라인 복제 선택](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > **Microsoft 소유의 디스크를 사용 하 여 전송** 을 선택 하는 옵션은이 기능이 미리 보기 상태 이므로 mabs v3에 사용할 수 없습니다. [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com)MABS v3에이 기능을 사용 하려면 microsoft에 문의 하세요.

12. Azure 구독에 대 한 소유자 액세스 권한이 있는 사용자 자격 증명을 사용 하 여 메시지가 표시 되 면 Azure에 로그인 합니다. 성공적으로 로그인 하면 다음과 같은 화면이 표시 됩니다.

    ![로그인 성공 후](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     그런 다음 DPM/MABS 서버는 구독에서 *배달* 됨 상태의 Data Box 작업을 인출 합니다.

     > [!NOTE]
     > 처음으로 로그인 하는 데 평소 보다 시간이 더 오래 걸립니다. Azure PowerShell 모듈은 백그라운드에서 설치 되며 Azure AD 응용 프로그램도 등록 됩니다.
     >
     >  - 설치 된 PowerShell 모듈은 다음과 같습니다.<br>
          -AzureRM *5.8.3*<br>
          -AzureRM *6.7.3*<br>
          -AzureRM *5.2.0*<br>
          -Azure Storage *4.6.1*<br>
     >  - Azure AD 응용 프로그램은 *AzureOfflineBackup_ \<object GUID of the user> *으로 등록 됩니다.

13. Data Box 디스크의 압축을 풀고 연결 하 고 잠금을 해제 한 올바른 데이터 상자 순서를 선택 합니다. **다음**을 선택합니다.

    ![Databox 선택](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. **DataBox 검색** 화면에서 Data Box 장치의 경로를 입력 하 고 **장치 검색**을 선택 합니다.

    ![네트워크 경로 입력](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Azure Data Box 디스크의 루트 디렉터리에 대 한 네트워크 경로를 제공 합니다. 이 디렉터리에는 아래와 같이 이름이 *Pageblob* 인 디렉터리가 포함 되어야 합니다.
    >
    > ![USB 드라이브](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > 예를 들어 디스크 경로가이 `\\mydomain\myserver\disk1\` 고 *Disk1* 에 *pageblob*이라는 디렉터리가 포함 되어 있으면 DPM/mabs 서버 마법사에서 제공 하는 경로는 `\\mydomain\myserver\disk1\` 입니다.
    > [Azure Data Box 100 TB 장치를 설치](https://docs.microsoft.com/azure/backup/offline-backup-azure-data-box#setup-azure-data-box)하는 경우 장치에 대 한 네트워크 경로로 다음을 제공 합니다 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` .

15. **다음**을 선택합니다. **요약** 페이지에서 설정을 검토 하 고 **그룹 만들기**를 선택 합니다.

    ![Databox 검색](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    다음 화면에서는 보호 그룹이 성공적으로 만들어졌는지 확인 합니다.

    ![보호 그룹이 만들어짐](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. 위의 화면에서 **닫기** 를 선택 합니다.

    이를 통해 데이터의 초기 복제가 DPM/MABS 디스크에 발생 합니다. 보호가 완료 되 면 **보호 페이지에서** 그룹 상태가 **확인** 으로 표시 됩니다.

17. Azure Data Box 장치에 대 한 오프 라인 백업 복사를 시작 하려면 **보호 그룹**을 마우스 오른쪽 단추로 클릭 한 다음 **복구 지점 만들기** 옵션을 선택 합니다. 그런 다음 **온라인 보호** 옵션을 선택합니다.

    ![복구 지점 만들기](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![복구 지점](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   DPM/MABS 서버는 사용자가 선택한 데이터를 Azure Data Box 장치에 백업 하기 시작 합니다. 이 작업은 데이터 크기 및 DPM/MABS 서버와 Azure Data Box Disk 간의 연결 속도에 따라 몇 시간에서 며칠까지 걸릴 수 있습니다.

   **모니터링** 창에서 작업의 상태를 모니터링할 수 있습니다. 데이터 백업이 완료 되 면 아래와 유사한 화면이 표시 됩니다.

   ![관리자 콘솔](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>백업 후 단계

Azure Data Box Disk에 대 한 데이터 백업이 성공적으로 완료 되 면 다음 단계를 수행 합니다.

- [이 문서의](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up) 단계에 따라 Azure에 Azure Data Box 디스크를 배송 합니다. Azure Data Box 100-TB 장치를 사용 하는 경우 [다음 단계](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) 에 따라 Azure에 Azure Data Box를 제공 합니다.
- Azure Portal에서 [Data Box 작업을 모니터링](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) 합니다. Azure Data Box 작업이 *완료*되 면 DPM/mabs 서버는 다음 예약 된 백업 시 저장소 계정에서 Recovery Services 자격 증명 모음으로 데이터를 자동으로 이동 합니다. 복구 지점이 성공적으로 만들어지면 백업 작업이 *완료* 된 것으로 표시 됩니다.

  > [!NOTE]
  > DPM/MABS 서버는 보호 그룹을 만드는 동안 예약 된 시간에 백업을 트리거합니다. 그러나 이러한 작업은 작업이 완료 될 때까지 *Azure Data Box 작업이 완료 되기를 기다리는* 플래그를 지정 합니다.

- DPM/MABS 서버에서 초기 백업에 해당 하는 복구 지점을 만든 후에는 Azure Data Box 작업과 연결 된 저장소 계정 (또는 특정 내용)을 삭제할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

DPM 서버의 Microsoft Azure Backup (MAB) 에이전트가 테 넌 트에서 Azure AD 응용 프로그램을 만듭니다. 이 응용 프로그램에는 오프 라인 시드 정책을 구성할 때 생성 및 업로드 되는 인증을 위한 인증서가 필요 합니다.

Azure AD 응용 프로그램에 인증서를 만들고 업로드 하는 데 Azure PowerShell를 사용 합니다.

### <a name="issue"></a>문제

오프 라인 백업을 구성할 때 Azure PowerShell cmdlet의 알려진 코드 오류로 인해 MAB 에이전트가 만든 동일한 Azure AD 응용 프로그램에 여러 인증서를 추가할 수 없습니다. 이렇게 하면 동일한 서버 또는 다른 서버에 대 한 오프 라인 시드 정책을 구성한 경우에 영향을 줍니다.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>문제가이 특정 근본 원인으로 인 한 것인지 확인 합니다.

위의 [문제로](#issue) 인해 실패가 발생 하는지 확인 하려면 다음 단계 중 하나를 수행 합니다.

#### <a name="step-1"></a>1단계

오프 라인 백업을 구성할 때 DPM/MABS 콘솔에 다음 오류 메시지가 표시 되는지 확인 합니다.

![Azure 복구 서비스 에이전트](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>2단계

1. 설치 경로에서 **임시** 폴더를 엽니다. 기본 임시 폴더 경로는 *C:\Program Files\Microsoft Azure Recovery Services agent\temp*입니다. *C를* 검색 하 고 파일을 엽니다.
2. *Cis Icurr* 파일에서 마지막 줄로 스크롤하고 "고객 계정에서 Azure AD 응용 프로그램 자격 증명을 만들 수 없습니다."로 인해 오류가 발생 했는지 확인 합니다. 예외: KeyId를 사용 하 여 기존 자격 증명으로 업데이트할 \<some guid> 수 없습니다. "

### <a name="workaround"></a>해결 방법

이 문제를 해결 하려면 다음 단계를 수행 하 고 정책 구성을 다시 시도 하십시오.

1. 가져오기 내보내기 작업을 만들 구독에 대 한 관리자 액세스 권한이 있는 다른 계정을 사용 하 여 DPM/MABS 서버 UI에 표시 되는 Azure 로그인 페이지에 로그인 합니다.
2. 다른 서버에 오프 라인 시드가 구성 되어 있지 않고 다른 서버가 응용 프로그램에 종속 되어 있지 않은 경우에는 `AzureOfflineBackup_<Azure User Id>` **Azure Portal > Azure Active Directory > 앱 등록**에서이 응용 프로그램을 삭제 합니다.

   > [!NOTE]
   > 응용 프로그램에 `AzureOfflineBackup_<Azure User Id>` 다른 오프 라인 시드가 구성 되어 있지 않고 다른 서버가이 응용 프로그램에 종속 되어 있지 않은지 확인 합니다. 공개 키 섹션 아래의 **설정 > 키** 로 이동 하 여 다른 **공개 키** 를 추가 하지 않아야 합니다. 참조는 다음 스크린샷을 참조 하세요.
   >
   > ![공개 키](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>3단계

오프 라인 백업을 구성 하려는 DPM/MABS 서버에서 다음 작업을 수행 합니다.

1. **컴퓨터 인증서 응용 프로그램 관리**  >  **개인** 탭을 열고 이름이 인 인증서를 찾습니다 `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
2. 위의 인증서를 선택 하 고, **모든 작업** 을 마우스 오른쪽 단추로 클릭 하 고 개인 키를 사용 하지 않고를 .cer 형식으로 **내보냅니다** .
3. **지점 2**에서 언급 한 Azure Offline Backup 응용 프로그램으로 이동 합니다. **설정**  >  **키**  >  **업로드 공개 키** 에서 위의 단계에서 내보낸 인증서를 업로드 합니다.

   ![공개 키 업로드](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. 서버에서 **실행** 창에 **regedit** 를 입력 하 여 레지스트리를 엽니다.
5. 레지스트리 *Computer\HKEY \_ LOCAL \_ MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*로 이동 합니다. **Cloudbackupprovider** 를 마우스 오른쪽 단추로 클릭 하 고 이름이 인 새 문자열 값을 추가 `AzureADAppCertThumbprint_<Azure User Id>` 합니다.

    >[!NOTE]
    > Azure 사용자 ID를 가져오려면 다음 작업 중 하나를 수행 합니다.
    >
    >- Azure에 연결 된 PowerShell에서 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` 명령을 실행 합니다.
    > - `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup`이름이 *currentuserid*인 레지스트리 경로로 이동 합니다.

6. 위의 단계에서 추가 된 문자열을 마우스 오른쪽 단추로 클릭 하 고 **수정**을 선택 합니다. 값에서 **지점 2** 에서 내보낸 인증서의 지문을 입력 하 고 **확인**을 선택 합니다.
7. 지문 값을 가져오려면 인증서를 두 번 클릭 한 다음 **세부 정보** 를 선택 하 고 지문 필드가 표시 될 때까지 아래로 스크롤합니다. **손 도장 (Thumbprint** )을 선택 하 고 값을 복사 합니다.

   ![인증서](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>다음 단계

- [자체 디스크를 사용 하 여 오프 라인 시드 (Azure Import/Export 서비스 사용)](backup-azure-backup-server-import-export.md)
