---
title: "클래식 배포 모델을 사용하여 Windows 서버 또는 클라이언트 백업 | Microsoft Docs"
description: "백업 자격 증명 모음을 만들고, 자격 증명을 다운로드하고, 백업 에이전트를 설치하고, 파일 및 폴더의 초기 백업을 완료하여 Windows 서버 또는 클라이언트를 Azure에 백업합니다."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "백업 자격 증명 모음, Windows 서버 백업, Windows 백업"
ms.assetid: 3b543bfd-8978-4f11-816a-0498fe14a8ba
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ddb1eabf88c48228f312d2fb0ac7f6685e448685


---
# <a name="back-up-a-windows-server-or-client-to-azure-using-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 Windows 서버 또는 클라이언트 백업
> [!div class="op_single_selector"]
> * [클래식 포털](backup-configure-vault-classic.md)
> * [Azure 포털](backup-configure-vault.md)
>
>

이 문서에서는 환경을 준비하고 Windows Server(또는 클라이언트)를 Azure에 백업하기 위해 따라야 할 절차를 다룹니다. 또한 백업 솔루션 배포 시 고려 사항에 대해서도 설명합니다. Azure 백업을 처음으로 시도하려는 경우 이 문서를 보면 해당 프로세스에 대한 간단한 지침을 볼 수 있습니다.

![자격 증명 모음 만들기](./media/backup-configure-vault-classic/initial-backup-process.png)

> [!IMPORTANT]
> Azure는 리소스를 만들고 작업하기 위한 두 가지 배포 모델로 리소스 관리자와 클래식을 제공합니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.
>
>

## <a name="before-you-start"></a>시작하기 전에
서버 또는 클라이언트를 Azure에 백업하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/free/) 을 만들 수 있습니다.

## <a name="step-1-create-a-backup-vault"></a>1단계: 백업 자격 증명 모음 만들기
서버 또는 클라이언트의 파일과 폴더를 백업하려면 데이터를 저장하려는 지역에 백업 자격 증명 모음을 만들어야 합니다.

### <a name="to-create-a-backup-vault"></a>백업 자격 증명 모음을 만들려면
1. [클래식 포털](https://manage.windowsazure.com/)에 로그인 합니다.
2. **새로 만들기** > **Data Services** > **Recovery Services** > **Backup 자격 증명 모음**을 클릭하고 **빨리 만들기**를 선택합니다.
3. **이름** 매개 변수에 백업 자격 증명 모음에 대한 이름을 입력합니다. 이름을 2~50자 사이로 입력합니다. 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다. 이 이름은 각 구독에 대해 고유해야 합니다.
4. **지역** 매개 변수에서 백업 자격 증명 모음의 지역을 선택합니다. 선택에 따라 백업 데이터가 전송되는 지역이 결정됩니다. 사용자의 위치에 가까운 지역을 선택하면 Azure에 백업할 때 네트워크 대기 시간을 줄일 수 있습니다.
5. **자격 증명 모음 만들기**를 클릭합니다.

    ![백업 자격 증명 모음 만들기](./media/backup-configure-vault-classic/demo-vault-name.png)

    백업 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다. 상태를 확인하려면 클래식 포털 화면의 아래쪽에서 알림을 모니터링합니다.

    백업 자격 증명 모음을 만든 후 자격 증명 모음이 성공적으로 생성되었다는 메시지가 나타납니다. **복구 서비스** 리소스 목록에 **활성**으로 나타납니다.

    ![자격 증명 모음 상태 만들기](./media/backup-configure-vault-classic/recovery-services-select-vault.png)
6. 여기에 설명된 단계에 따라 저장소 중복 옵션을 선택합니다.

   > [!IMPORTANT]
   > 저장소 중복 옵션을 지정하기에 가장 좋은 시기는 자격 증명 모음을 만든 후 자격 증명 모음에 컴퓨터를 등록하기 바로 직전입니다. 항목이 자격 증명 모음에 등록되고 나면 저장소 중복 옵션 잠기고 수정할 수 없습니다.
   >
   >

    기본 백업 저장소 끝점으로 Azure를 사용 중인 경우(예: Windows Server에서 Azure로 백업하는 경우) [지역 중복 저장소](../storage/storage-redundancy.md#geo-redundant-storage) 옵션(기본값)을 선택하는 것이 좋습니다.

    3차 백업 저장소 끝점으로 Azure를 사용 중인 경우(예: 온-프레미스에 로컬 백업 복사본을 보관하기 위해 System Center Data Protection Manager를 사용하는 경우 및 장기 보존 요구를 해결하기 위해 Azure를 사용하는 경우) [로컬 중복 저장소](../storage/storage-redundancy.md#locally-redundant-storage)를 선택하는 것이 좋습니다. 이렇게 하면 Azure에 데이터를 저장하는 비용을 크게 줄일 수 있지만 3차 복사본에 허용될 수 있는 데이터 영속성 수준이 낮아집니다.

    **저장소 중복 옵션을 선택하려면:**

    a. 방금 만든 자격 증명 모음을 클릭합니다.

    b. 빠른 시작 페이지에서 **구성**을 선택합니다.

    ![자격 증명 모음 상태 구성](./media/backup-configure-vault-classic/configure-vault.png)

    c. 적절한 저장소 중복 옵션을 선택합니다.

    **지역 중복**이 기본 옵션이기 때문에 **로컬 중복**을 선택하는 경우 **저장**을 클릭해야 합니다.

    d. 왼쪽 탐색 창에서 **복구 서비스** 를 클릭하여 복구 서비스의 리소스 목록으로 돌아갑니다.

## <a name="step-2-download-the-vault-credential-file"></a>2단계: 자격 증명 모음 자격 증명 파일 다운로드
온-프레미스 컴퓨터는 백업 자격 증명 모음을 인증해야 Azure에 데이터를 백업할 수 있습니다. 인증은 *보관 자격 증명*을 통해 수행됩니다. 보관 자격 증명 파일은 클래식 포털에서 보안 채널을 통해 다운로드됩니다. 인증서 개인 키는 포털 또는 서비스에 유지되지 않습니다.

[보관 자격 증명을 사용하여 백업 서비스 인증](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file)에 대한 자세한 내용을 알아보세요.

### <a name="to-download-the-vault-credential-file-to-a-local-machine"></a>로컬 컴퓨터에 자격 증명 모음 자격 증명 파일을 다운로드하려면
1. 왼쪽 탐색 창에서 **복구 서비스**를 클릭한 다음 생성된 백업 자격 증명 모음을 선택합니다.

    ![IR 완료](./media/backup-configure-vault-classic/rs-left-nav.png)
2. 빠른 시작 페이지에서 **자격 증명 모음 자격 증명 다운로드**를 클릭합니다.

   클래식 포털에서는 자격 증명 모음 이름과 현재 날짜를 조합하여 보관 자격 증명을 생성합니다. 자격 증명 모음 자격 증명 파일은 등록 워크플로 중에만 사용되며 48시간 후에 만료됩니다.

   자격 증명 모음 자격 증명 파일은 포털에서 다운로드할 수 있습니다.
3. **저장** 을 클릭하여 보관 자격 증명 파일을 로컬 계정의 다운로드 폴더에 다운로드합니다. **저장** 메뉴에서 **다른 이름으로 저장**을 선택하여 보관 자격 증명 파일에 대한 위치를 지정할 수 있습니다.

   > [!NOTE]
   > 자격 증명 모음 자격 증명 파일이 컴퓨터에서 액세스할 수 있는 위치에 저장되었는지 확인합니다. 파일 공유 또는 서버 메시지 블록에 저장되는 경우 액세스 권한이 있는지 확인합니다.
   >
   >

## <a name="step-3-download-install-and-register-the-backup-agent"></a>3단계: 백업 에이전트 다운로드, 설치 및 등록
백업 자격 증명 모음을 만들고 자격 증명 모음 자격 증명 파일을 다운로드한 후 각 Windows 컴퓨터에 에이전트를 설치해야 합니다.

### <a name="to-download-install-and-register-the-agent"></a>에이전트를 다운로드, 설치 및 등록하려면
1. **복구 서비스**를 클릭한 다음 서버에 등록하려는 백업 저장소를 선택합니다.
2. 빠른 시작 페이지에서 **Windows Server 또는 System Center Data Protection Manager 또는 Windows 클라이언트용 에이전트**를 클릭합니다. 그런 다음 **Save**를 클릭합니다.

    ![에이전트 저장](./media/backup-configure-vault-classic/agent.png)
3. MARSagentinstaller.exe 파일 다운로드가 완료되면 **실행**을 클릭하거나 저장된 위치에서 **MARSAgentInstaller.exe**를 두 번 클릭합니다.
4. 에이전트에 필요한 설치 폴더 및 캐시 폴더를 선택한 다음 **다음**을 클릭합니다. 지정된 캐시 위치에 백업 데이터의 5% 이상에 해당하는 여유 공간이 있어야 합니다.
5. 기본 프록시 설정을 통해 인터넷에 계속해서 연결할 수 있습니다.             프록시 서버를 사용하여 인터넷에 연결하는 경우 프록시 구성 페이지에서 **사용자 지정 프록시 설정 사용** 확인란을 선택한 다음 프록시 서버 세부 정보를 입력합니다. 인증된 프록시를 사용하는 경우 사용자 이름 및 암호 세부 정보를 입력한 다음 **다음**을 클릭합니다.
6. **설치** 를 클릭하여 에이전트 설치를 시작합니다. 백업 에이전트가 .NET Framework 4.5 및 Windows PowerShell(아직 설치되지 않은 경우)을 설치하여 설치를 완료합니다.
7. 에이전트가 설치되면 **등록 진행** 을 클릭하여 워크플로를 계속합니다.
8. 자격 증명 모음 식별 페이지에서 이전에 다운로드한 자격 증명 모음 자격 증명 파일로 이동하여 선택합니다.

    자격 증명 모음 자격 증명 파일은 48시간 동안만 유효합니다(그 이후에는 포털에서 다운로드). 이 페이지에서 오류가 발생할 경우(예: "제공한 자격 증명 모음 자격 증명 파일이 만료되었습니다.") 포털에 로그인하고 자격 증명 모음 자격 증명 파일을 다시 다운로드합니다.

    설치 응용 프로그램에서 액세스할 수 있는 위치에 있는 자격 증명 모음 자격 증명 파일인지 확인합니다. 액세스 관련 오류가 발생할 경우 동일한 컴퓨터의 임시 위치에 자격 증명 모음 자격 증명 파일을 복사하고 작업을 다시 시도합니다.

    자격 증명 모음 자격 증명 오류(예: "잘못된 자격 증명 모음 자격 증명을 제공했습니다.")가 발생한 경우 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. 포털에서 새 자격 증명 모음 자격 증명 파일을 다운로드한 후에 작업을 다시 시도합니다. 이 오류는 사용자가 **보관 자격 증명 다운로드** 옵션을 빠르게 연속적으로 여러 번 클릭할 경우에 발생할 수도 있습니다. 이 경우 마지막 자격 증명 모음 자격 증명 파일만 유효합니다.
9. 암호화 설정 페이지에서 암호를 생성하거나 암호를 제공합니다(최소 16자). 암호를 안전한 위치에 저장해야 합니다.
10. **마침**을 클릭합니다. 서버 등록 마법사는 백업에 서버를 등록합니다.

    > [!WARNING]
    > 암호를 분실하거나 잊어버린 경우 Microsoft에서 백업 데이터의 복구를 도와드릴 수 없습니다. 사용자는 암호화 암호를 소유하며 Microsoft는 사용자가 사용하는 암호를 전혀 볼 수 없습니다. 이 파일은 복구 작업에 필요하므로 안전한 위치에 저장합니다.
    >
    >
11. 암호화 키가 설정되면, **Microsoft Azure 복구 서비스 에이전트 시작** 확인란은 선택된 상태로 둔 다음 **닫기**를 클릭합니다.

## <a name="step-4-complete-the-initial-backup"></a>4단계: 초기 백업을 완료합니다.
초기 백업에는 두 가지 주요 작업이 포함됩니다.

* 백업 일정 만들기
* 처음으로 파일 및 폴더 백업

백업 정책이 초기 백업을 완료한 후에 데이터를 복구하는 경우 사용할 수 있는 백업 지점을 만듭니다. 백업 정책은 정의하는 일정에 따라 이를 수행합니다.

### <a name="to-schedule-the-backup"></a>백업을 예약하려면
1. Microsoft Azure 백업 에이전트를 엽니다. (서버 등록 마법사를 닫을 때 **Microsoft Azure 복구 서비스 에이전트 시작** 확인란을 선택된 상태로 둔 경우 자동으로 열립니다.) **Microsoft Azure 백업**에 대한 컴퓨터를 검색하여 찾을 수 있습니다.

    ![Azure 백업 에이전트 시작](./media/backup-configure-vault-classic/snap-in-search.png)
2. 백업 에이전트에서 **백업 예약**을 클릭합니다.

    ![Windows Server 백업 예약](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. 백업 예약 마법사의 시작 페이지에서 **다음**을 클릭합니다.
4. 백업할 항목 선택 페이지에서 **항목 추가**를 클릭합니다.
5. 백업할 파일 및 폴더를 선택한 다음 **확인**을 클릭합니다.
6. **Next**를 클릭합니다.
7. **백업 일정 지정** 페이지에서 **백업 일정**을 지정하고 **다음**을 클릭합니다.

    매일(하루에 최대 속도로 3회) 또는 매주 백업을 예약할 수 있습니다.

    ![Windows Server 백업에 대한 항목](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

   > [!NOTE]
   > 백업 일정을 지정하는 방법은 [Azure 백업을 사용하여 테이프 인프라 대체](backup-azure-backup-cloud-as-tape.md)문서를 참조하세요.
   >
   >
8. **보존 정책 선택** 페이지에서 백업 복사본의 **보존 정책**을 선택합니다.

    보존 정책은 백업의 저장 기간을 나타냅니다. 모든 백업 지점에 대한 "일반 정책"을 지정하는 대신 백업이 발생하는 시기에 따라 다른 보존 정책을 지정할 수 있습니다. 매일, 매주, 매월 및 매년 보존 정책을 요구 사항에 맞게 수정할 수 있습니다.
9. 초기 백업 유형 선택 페이지에서 초기 백업 유형을 선택합니다. **네트워크를 통해 자동으로** 옵션이 선택된 상태로 두고 **다음**을 클릭합니다.

    네트워크를 통해 자동으로 백업하거나 오프라인으로 백업할 수 있습니다. 이 문서의 나머지 부분은 자동 백업 프로세스를 설명합니다. 오프라인 백업을 선호하는 경우 [Azure 백업의 오프라인 백업 워크플로](backup-azure-backup-import-export.md) 문서에서 자세한 내용을 참조하세요.
10. 확인 페이지에서 정보를 검토한 다음 **마침**을 클릭합니다.
11. 마법사가 백업 일정 생성을 완료하면 **닫기**를 클릭합니다.

### <a name="enable-network-throttling-optional"></a>네트워크 제한 사용(선택 사항)
백업 에이전트는 네트워크 제한 기능을 제공합니다. 제한 기능은 데이터 전송 중에 사용되는 네트워크 대역폭의 양을 제어합니다. 근무 시간에 데이터를 백업해야 하는데 백업 프로세스가 다른 인터넷 트래픽을 방해하지 말아야 할 때 유용한 기능입니다. 제한은 백업 및 복원 작업에 적용됩니다.

**네트워크 제한을 사용하려면**

1. 백업 에이전트에서 **속성 변경**을 클릭합니다.

    ![속성 변경](./media/backup-configure-vault-classic/change-properties.png)
2. **제한** 탭에서 **백업 작업에 인터넷 대역폭 사용 제한 사용** 확인란을 선택합니다.

    ![네트워크 제한](./media/backup-configure-vault-classic/throttling-dialog.png)
3. 제한을 사용하도록 설정했으면 **근무 시간** 및 **휴무 시간** 중에 백업 데이터 전송에 허용되는 대역폭을 지정합니다.

    대역폭 값은 512Kbps(초당 킬로비트)에서 시작하여 최대 1023MBps(초당 메가바이트)까지 증가할 수 있습니다. 또한 **근무 시간**의 시작 및 완료 시간과 근무일로 간주되는 요일을 지정할 수도 있습니다. 지정된 작업 시간 이외의 시간은 비 작업 시간으로 간주됩니다.
4. **확인**을 클릭합니다.

### <a name="to-back-up-now"></a>지금 백업하려면
1. 백업 에이전트에서 **지금 백업** 을 클릭하여 네트워크를 통한 초기 시드 작업을 완료합니다.

    ![지금 Windows Server 백업](./media/backup-configure-vault-classic/backup-now.png)
2. 확인 페이지에서 컴퓨터를 백업하는 데 지금 백업 마법사가 사용할 설정을 검토합니다. 그런 다음 **백업**을 클릭합니다.
3. **닫기** 를 클릭하여 마법사를 닫습니다. 백업 프로세스가 완료되기 전에 이를 수행한 경우 마법사는 백그라운드에서 계속해서 실행할 수 있습니다.

초기 백업 작업이 완료되면 백업 콘솔에 **작업 완료** 상태가 표시됩니다.

![IR 완료](./media/backup-configure-vault-classic/ircomplete.png)

## <a name="next-steps"></a>다음 단계
* [무료 Azure 계정](https://azure.microsoft.com/free/)을 등록합니다.

VM 또는 다른 워크로드를 백업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [IaaS VM 백업](backup-azure-vms-prepare.md)
* [Microsoft Azure 백업 서버를 통해 Azure에 워크로드 백업](backup-azure-microsoft-azure-backup.md)
* [DPM을 통해 Azure에 워크로드 백업](backup-azure-dpm-introduction.md)



<!--HONumber=Nov16_HO3-->


