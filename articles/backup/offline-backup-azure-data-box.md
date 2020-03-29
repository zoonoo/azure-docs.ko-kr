---
title: Azure 데이터 상자를 사용하여 오프라인 백업
description: Azure 데이터 상자를 사용하여 MARS 에이전트에서 복구 서비스 자격 증명 모음으로 오프라인으로 대규모 초기 백업 데이터를 시드하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672961"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure 데이터 상자를 사용 하 여 Azure 백업 오프라인 백업

[Azure 데이터 상자를](https://docs.microsoft.com/azure/databox/data-box-overview) 사용하여 네트워크를 사용하지 않고 오프라인으로 대규모 초기 MARS(MARS) 백업을 복구 서비스 자격 증명 모음에 시드할 수 있습니다. 이 프로세스는 대기 시간이 많은 네트워크를 통해 대량의 백업 데이터를 온라인으로 이동하는 데 사용되는 시간과 네트워크 대역폭을 절약합니다. 이 향상된 기능은 현재 미리 보기 상태입니다. Azure 데이터 상자를 기반으로 하는 오프라인 백업은 [Azure 가져오기/내보내기 서비스를 기반으로 하는 오프라인 백업에](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)비해 두 가지 뚜렷한 이점을 제공합니다.

* Azure 호환 디스크 와 커넥터를 직접 조달할 필요가 없습니다. Azure 데이터 상자는 선택한 데이터 [상자 SKU와](https://azure.microsoft.com/services/databox/data/)연결된 디스크를 제공합니다.
* Azure 백업(MARS 에이전트)은 지원되는 Azure 데이터 상자의 SCO에 백업 데이터를 직접 쓸 수 있습니다. 이 기능을 사용하면 초기 백업 데이터에 대한 스테이징 위치를 프로비전할 필요가 없습니다. 또한 해당 데이터를 디스크에 포맷하고 복사하는 유틸리티가 필요하지 않습니다.

## <a name="azure-data-box-with-the-mars-agent"></a>MARS 에이전트가 있는 Azure 데이터 상자

이 문서에서는 Azure 데이터 상자를 사용하여 MARS 에이전트에서 복구 서비스 자격 증명 모음으로 오프라인으로 큰 초기 백업 데이터를 시드하는 방법을 설명합니다.

## <a name="supported-platforms"></a>지원 플랫폼

Azure 데이터 상자를 사용하여 MARS 에이전트의 데이터를 시드하는 프로세스는 다음 Windows SCO에서 지원됩니다.

| **Os**                                 | **Sku**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **워크스테이션**                        |                                                              |
| Windows 10 64비트                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64비트                    | Enterprise, Pro                                             |
| Windows 8 64비트                      | Enterprise, Pro                                             |
| Windows 7 64비트                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **서버**                             |                                                              |
| Windows Server 2019 64비트            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64비트            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64비트         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64비트            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64비트    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64비트 | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64비트    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64비트     | Standard, Enterprise, Datacenter, Foundation                |
| 윈도우 서버 2008 SP2 64 비트        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>백업 데이터 크기 및 지원되는 데이터 박스 SUS

| 서버당 백업 데이터 크기(MARS에 의한 압축 후)* | 지원되는 Azure 데이터 상자 SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7.2 TB                                                    | [Azure 데이터 상자 디스크](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7.2 TB 및 <=80 TB**                                      | [Azure 데이터 상자(100TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

*일반적인 압축률은 10%에서 20% 사이입니다. <br>
**단일 MARS 서버에 대해 80TB 이상의 초기 백업 데이터가 있을 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)것으로 예상되는 경우 에 문의하십시오.

>[!IMPORTANT]
>단일 서버의 초기 백업 데이터는 단일 Azure 데이터 박스 인스턴스 또는 Azure Data Box 디스크 내에 포함되어야 하며 동일하거나 다른 SCO의 여러 장치 간에 공유할 수 없습니다. 그러나 Azure 데이터 상자 장치에는 여러 서버의 초기 백업이 포함될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="azure-subscription-and-required-permissions"></a>Azure 구독 및 필수 권한

* 이 프로세스에는 Azure 구독이 필요합니다.
* 이 프로세스에서는 오프라인 백업 정책을 수행하도록 지정된 사용자가 Azure 구독의 소유자임을 요구합니다.
* 데이터 상자 작업 및 복구 서비스 자격 증명 모음(데이터를 시드해야 함)은 동일한 구독에 있어야 합니다.
* Azure 데이터 상자 작업 및 복구 서비스 자격 증명 모음과 연결된 대상 저장소 계정이 동일한 지역에 있는 것이 좋습니다. 그러나, 이것은 필요 하지 않습니다.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 받기

*이 프로세스의 가장 중요한 전제 조건입니다.* Azure PowerShell 버전 3.7.0을 설치하기 전에 다음 검사를 수행합니다.

#### <a name="step-1-check-the-powershell-version"></a>1단계: PowerShell 버전 확인

1. Windows PowerShell을 열고 다음 명령을 실행합니다.

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  출력에 3.7.0보다 높은 버전이 표시되는 경우 "2단계"를 수행합니다. 그렇지 않으면 "3단계"로 건너뜁니다.

#### <a name="step-2-uninstall-the-powershell-version"></a>2단계: PowerShell 버전 제거

현재 버전의 PowerShell을 제거합니다.

1. PowerShell에서 다음 명령을 실행하여 종속 모듈을 제거합니다.

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. 모든 종속 모듈을 성공적으로 삭제하려면 다음 명령을 실행합니다.

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>3단계: PowerShell 버전 설치 3.7.0

AzureRM 모듈이 없는지 확인한 후 다음 방법 중 하나를 사용하여 버전 3.7.0을 설치합니다.

* GitHub에서 [이 링크를](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)사용합니다.

또는

* PowerShell 창에서 다음 명령을 실행합니다.

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell은 msi 파일을 사용하여 설치되었을 수도 있습니다. 제거하려면 제어판에서 **프로그램 제거** 옵션을 사용하여 제거합니다.

### <a name="order-and-receive-the-data-box-device"></a>데이터 박스 장치 주문 및 수신

MARS 및 Azure 데이터 상자를 사용하는 오프라인 백업 프로세스에서는 MARS 에이전트를 사용하여 오프라인 백업을 트리거하기 전에 데이터 상자 장치가 배달된 상태로 있어야 합니다. 요구 사항에 가장 적합한 SKU를 주문하려면 [백업 데이터 크기 및 지원되는 데이터 상자 SKU를](#backup-data-size-and-supported-data-box-skus)참조하십시오. [자습서: Azure 데이터 상자 디스크를 주문하여](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) 데이터 상자 장치를 주문하고 받을 수 있는 자습서의 단계를 따릅니다.

> [!IMPORTANT]
> **계정 종류에**대해 *BlobStorage를* 선택하지 마십시오. MARS 에이전트에는 페이지 Blob을 지원하는 계정이 필요하며, *BlobStorage를* 선택할 때 지원되지 않습니다. Azure Data Box 작업에 대한 대상 저장소 계정을 만들 때 **저장소 V2(범용 v2)를** **계정 종류로** 선택합니다.

![인스턴스 세부정보에서 계정 종류 선택](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>MARS 에이전트 설치 및 설정

1. MARS 에이전트의 이전 설치를 제거해야 합니다.
1. [이 웹 사이트에서](https://aka.ms/azurebackup_agent)최신 MARS 에이전트를 다운로드하십시오.
1. *MARSAgentInstaller.exe를*실행하고 백업을 저장할 복구 서비스 자격 증명 모음에 [에이전트를 설치하고 등록하는](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent) *단계만* 수행합니다.

   > [!NOTE]
   > 복구 서비스 자격 증명 모음은 Azure 데이터 상자 작업과 동일한 구독에 있어야 합니다.

   에이전트가 복구 서비스 자격 증명 모음에 등록된 후 다음 섹션의 단계를 따릅니다.

## <a name="set-up-azure-data-box-devices"></a>Azure 데이터 상자 장치 설정

주문한 Azure 데이터 상자 SKU에 따라 다음의 해당 섹션에서 다루는 단계를 수행합니다. 이 단계는 MARS 에이전트가 초기 백업 데이터를 식별하고 전송할 수 있도록 데이터 박스 장치를 설정하고 준비하는 방법을 보여 준다.

### <a name="set-up-azure-data-box-disks"></a>Azure 데이터 상자 디스크 설정

하나 이상의 Azure 데이터 박스 디스크(각각 최대 8TB)를 주문한 경우 여기에 언급된 단계를 따라 [데이터 상자 디스크의 압축을 풀고 연결하고 잠금을 해제합니다.](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)

>[!NOTE]
>MARS 에이전트가 있는 서버에 USB 포트가 없을 수 있습니다. 이 경우 Azure Data Box 디스크를 다른 서버 또는 클라이언트에 연결하고 장치의 루트를 네트워크 공유로 노출할 수 있습니다.

### <a name="set-up-azure-data-box"></a>Azure 데이터 상자 설정

Azure 데이터 상자 인스턴스(최대 100TB)를 주문한 경우 다음 단계를 [수행하여 데이터 상자 인스턴스를 설정합니다.](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Azure 데이터 상자 인스턴스를 로컬 시스템으로 마운트

MARS 에이전트는 로컬 시스템 컨텍스트에서 작동하므로 Azure 데이터 상자 인스턴스가 연결된 마운트 경로에 동일한 수준의 권한이 제공되어야 합니다. 

NFS 프로토콜을 사용하여 데이터 박스 장치를 로컬 시스템으로 탑재할 수 있도록 하려면 다음을 수행하십시오.

1. MARS 에이전트가 설치된 Windows 서버에서 NFS 기능에 대한 클라이언트를 사용하도록 설정합니다. 대체 소스 *WIM:D:\소스\Install.wim:4를*지정합니다.
1. MARS 에이전트가 <https://download.sysinternals.com/files/PSTools.zip> 설치된 서버로 PSExec을 다운로드합니다.
1. 상승된 명령 프롬프트를 열고 *PSExec.exe를* 현재 디렉토리로 포함하는 디렉터리로 다음 명령을 실행합니다.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   이전 명령의 결과로 열리는 명령 창은 로컬 시스템 컨텍스트에 있습니다. 이 명령 창을 사용하여 Azure 페이지 Blob 공유를 Windows 서버의 네트워크 드라이브로 탑재하는 단계를 실행합니다.
1. 데이터 상자에 [연결의](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) 단계를 수행하여 서버를 MARS 에이전트와 NFS를 통해 데이터 박스 장치에 연결합니다. 로컬 시스템 명령 프롬프트에서 다음 명령을 실행하여 Azure 페이지 Blob 공유를 탑재합니다.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   공유가 탑재된 후 서버에서 X에 액세스할 수 있는지 확인합니다. 할 수 있는 경우 이 문서의 다음 섹션을 계속 하십시오.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>초기 백업 데이터를 Azure 데이터 박스 장치로 전송

1. 서버에서 **Microsoft Azure 백업** 응용 프로그램을 엽니다.
1. **작업** 창에서 백업 **예약**을 선택합니다.

    ![백업 예약 선택](./media/offline-backup-azure-data-box/schedule-backup.png)

1. **백업 예약 마법사의**단계를 따릅니다.

1. 항목 추가 단추를 선택하여 **항목을 추가합니다.** 주문하고 받은 Azure 데이터 [상자 SKU에서 지원하는 크기 제한](#backup-data-size-and-supported-data-box-skus) 내에서 항목의 총 크기를 유지합니다.

    ![백업에 항목 추가](./media/offline-backup-azure-data-box/add-items.png)

1. 파일 및 폴더 및 **시스템 상태에**대한 적절한 백업 일정 및 보존 정책을 **선택합니다.** 시스템 상태는 Windows 서버에만 적용되며 Windows 클라이언트에는 적용되지 않습니다.
1. 마법사의 **초기 백업 유형(파일 및 폴더) 선택** 페이지에서 **Microsoft Azure 데이터 상자 디스크를 사용하여 전송** 옵션을 선택하고 다음 을 **선택합니다.**

    ![초기 백업 유형 선택](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Azure 구독에서 소유자 액세스 권한이 있는 사용자 자격 증명을 사용 하 여 메시지가 표시 될 때 Azure에 로그인 합니다. 성공하면 이 페이지와 유사한 페이지가 표시됩니다.

    ![리소스 만들기 및 필수 사용 권한 적용](./media/offline-backup-azure-data-box/creating-resources.png)

   그런 다음 MARS 에이전트는 배달된 상태에 있는 구독에서 데이터 상자 작업을 가져옵니다.

    ![구독 ID에 대한 데이터 상자 작업 가져오기](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. 데이터 박스 디스크의 압축을 풀고, 연결하고, 잠금 해제한 올바른 데이터 상자 순서를 선택합니다. **다음**을 선택합니다.

    ![데이터 상자 주문 선택](./media/offline-backup-azure-data-box/select-databox-order.png)

1. 데이터 상자 장치 검색 페이지에서 **장치 검색을** **선택합니다.** 이 작업을 수행하면 MARS 에이전트가 로컬로 연결된 Azure 데이터 상자 디스크를 검색하고 검색합니다.

    ![데이터 박스 장치 감지](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Azure Data Box 인스턴스를 네트워크 공유로 연결한 경우(USB 포트를 사용할 수 없거나 100TB 데이터 박스 장치를 주문하고 탑재했기 때문에) 처음에는 검색에 실패합니다. 데이터 상자 장치에 대한 네트워크 경로를 입력할 수 있는 옵션이 제공됩니다.

    ![네트워크 경로 입력](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Azure 데이터 상자 디스크의 루트 디렉터리에 대한 네트워크 경로를 제공합니다. 이 디렉터리에는 *PageBlob*이름으로 된 디렉터리가 포함되어야 합니다.
    >
    >![Azure 데이터 상자 디스크의 루트 디렉토리](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >예를 들어 `\\mydomain\myserver\disk1\` 디스크의 경로가 있고 *disk1에* *PageBlob이라는*디렉터리가 포함되어 있는 경우 MARS `\\mydomain\myserver\disk1\`에이전트 마법사 페이지에 입력하는 경로는 .
    >
    >Azure [데이터 상자 100TB 장치를 설정하는](#set-up-azure-data-box-devices) `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` 경우 장치에 대한 네트워크 경로로 입력합니다.

1. **다음**을 선택하고 다음 페이지에서 **완료를** 선택하여 Azure 데이터 상자를 사용하여 오프라인 백업구성으로 백업 및 보존 정책을 저장합니다.

   다음 페이지에서 정책이 성공적으로 저장됨을 확인합니다.

    ![정책이 성공적으로 저장됨](./media/offline-backup-azure-data-box/policy-saved.png)

1. 이전 페이지에서 **닫기(close)를** 선택합니다.

1. MARS 에이전트 콘솔의 **작업** 창에서 **지금 백업을** 선택합니다. 마법사 페이지에서 **백업을** 선택합니다.

    ![지금 백업 마법사](./media/offline-backup-azure-data-box/backup-now.png)

MARS 에이전트가 선택한 데이터를 Azure 데이터 상자 장치에 백업하기 시작합니다. 이 프로세스는 몇 시간에서 며칠까지 걸릴 수 있습니다. 시간은 MARS 에이전트와 Azure 데이터 상자 디스크를 사용하여 서버 간의 파일 수와 연결 속도에 따라 달라집니다.

데이터 백업이 완료되면 MARS 에이전트에 이 페이지와 유사한 페이지가 표시됩니다.

![표시된 백업 진행률](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>백업 후 단계

이 섹션에서는 Azure 데이터 상자 디스크에 대한 데이터 백업이 성공한 후 수행해야 하는 단계를 설명합니다.

* 이 문서의 단계에 따라 [Azure 데이터 상자 디스크를 Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)로 제공합니다. Azure 데이터 상자 100TB 장치를 사용한 경우 다음 단계에 따라 Azure 데이터 상자 장치를 Azure 로 [제공합니다.](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)

* Azure [포털에서 데이터 상자 작업을 모니터링합니다.](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) Azure 데이터 상자 작업이 완료되면 MARS 에이전트는 다음 예약된 백업 시 저장소 계정에서 복구 서비스 자격 증명 모음으로 데이터를 자동으로 이동합니다. 그런 다음 복구 지점이 성공적으로 생성되면 백업 작업을 *작업 완료로* 표시합니다.

    >[!NOTE]
    >MARS 에이전트는 정책 생성 중에 예약된 시간에 백업을 트리거합니다. 이러한 작업은 작업이 완료될 때까지 "Azure 데이터 상자 작업이 완료되기를 기다리는 중"이라고 표시됩니다.

* MARS 에이전트가 초기 백업에 해당하는 복구 지점을 성공적으로 생성한 후 저장소 계정 또는 Azure 데이터 상자 작업과 연결된 특정 내용을 삭제할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

Microsoft Azure 백업(MAB) 에이전트는 테넌트에서 사용자용 Azure Active Directory(Azure AD) 응용 프로그램을 만듭니다. 이 응용 프로그램에는 오프라인 시드 정책을 구성할 때 생성되고 업로드되는 인증에 대한 인증서가 필요합니다. Azure PowerShell을 사용하여 Azure AD 응용 프로그램에 인증서를 만들고 업로드합니다.

### <a name="problem"></a>문제

오프라인 백업을 구성할 때 Azure PowerShell cmdlet의 버그로 인해 문제가 발생할 수 있습니다. MAB 에이전트에서 만든 동일한 Azure AD 응용 프로그램에 여러 인증서를 추가하지 못할 수 있습니다. 이 문제는 동일하거나 다른 서버에 대해 오프라인 시드 정책을 구성한 경우에 영향을 미칩니다.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>이 특정 근본 원인으로 인해 문제가 발생했는지 확인합니다.

문제가 앞에서 설명한 것과 동일한지 확인하려면 다음 단계 중 하나를 수행하십시오.

#### <a name="step-1"></a>1단계

오프라인 백업을 구성할 때 MAB 콘솔에 다음과 같은 오류 메시지가 나타나는지 확인합니다.

![현재 Azure 계정에 대해 오프라인 백업 정책을 만들 수 없습니다.](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>2단계

1. 설치 경로에서 **임시** 폴더를 엽니다. 기본 임시 폴더 경로는 *C:\프로그램 파일\Microsoft Azure 복구 서비스 에이전트\Temp*입니다. *CBUICurr* 파일을 찾아 파일을 엽니다.

1. *CBUICurr* 파일에서 마지막 줄로 스크롤하여 문제가 이 오류 메시지의 파일과 동일한지 `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`확인합니다.

### <a name="workaround"></a>해결 방법

이 문제를 해결 하려면 다음 단계를 수행 하 고 정책 구성을 다시 시도 합니다.

#### <a name="step-1"></a>1단계

가져오기 또는 내보내기 작업이 생성된 구독에서 관리자 액세스 권한이 있는 다른 계정을 사용하여 MAB UI에 나타나는 PowerShell에 로그인합니다.

#### <a name="step-2"></a>2단계

다른 서버가 오프라인 시드를 구성하지 않고 다른 서버가 `AzureOfflineBackup_<Azure User Id>` 응용 프로그램에 종속되지 않는 경우 이 응용 프로그램을 삭제합니다. **Azure 포털** > **Azure Active Directory** > 앱**등록을 선택합니다.**

>[!NOTE]
> `AzureOfflineBackup_<Azure User Id>` 응용 프로그램에 구성된 다른 오프라인 시드가 없는지, 다른 서버가 이 응용 프로그램에 종속되어 있지 않은지 확인합니다. 공개 키 섹션 아래의 **설정** > **키로** **이동합니다.** 다른 공개 키가 추가되어서는 안 됩니다. 참조할 수 있는 다음 스크린샷을 참조하십시오.
>
>![공개 키](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>3단계

오프라인 백업을 구성하려는 서버에서 다음 작업을 수행합니다.

1. **컴퓨터 인증서 관리 응용 프로그램** > **개인** 탭으로 이동하여 `CB_AzureADCertforOfflineSeeding_<ResourceId>`이름으로 인증서를 찾습니다.

2. 인증서를 선택하고 모든 **작업을**마우스 오른쪽 단추로 클릭하고 .cer 형식의 개인 키 없이 **내보내기를** 선택합니다.

3. 2단계에서 언급한 Azure 오프라인 백업 응용 프로그램으로 이동합니다. **설정** > **키** > **업로드 공개 키를**선택합니다. 이전 단계에서 내보낸 인증서를 업로드합니다.

    ![공개 키 업로드](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 서버에서 실행 창에 **regedit를** 입력하여 레지스트리를 엽니다.

5. 레지스트리 *컴퓨터\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure 백업\Config\CloudBackupProvider로 이동합니다.* 오른쪽 단추로 클릭 **CloudBackupProvider**및 이름으로 `AzureADAppCertThumbprint_<Azure User Id>`새 문자열 값을 추가 합니다.

    >[!NOTE]
    > Azure 사용자 ID를 얻으려면 다음 작업 중 하나를 수행합니다.
    >
    >* Azure 연결 PowerShell에서 명령을 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` 실행합니다.
    > * 레지스트리 경로로 이동 *컴퓨터\HKEY_LOCAL_MACHINE\SOFTWARE\마이크로소프트\윈도우 Azure 백업\DbgSettings\온라인 백업* 이름으로 이름 *CurrentUserId*.

6. 이전 단계에서 추가된 문자열을 마우스 오른쪽 단추로 클릭하고 **에서 수정을**선택합니다. 값에서 2단계에서 내보낸 인증서의 지문을 제공합니다. **확인**을 선택합니다.

7. 지문 값을 얻으려면 인증서를 두 번 클릭합니다. 세부 **정보** 탭을 선택하고 지문 필드가 표시될 때까지 아래로 스크롤합니다. 지문을 선택하고 값을 **복사합니다.**

    ![인증서의 지문 필드](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>질문

당신이 직면 한 문제에 대한 질문이나 설명은, 문의 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).
