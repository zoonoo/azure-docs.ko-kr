---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구에 대한 복제 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구 중에 일반적인 복제 문제를 해결하는 방법을 제공합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: c53dc81da9469c0628adbd3751dc818997fa4d05
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063681"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM 및 실제 서버에 대한 복제 문제 해결

Azure Site Recovery를 사용하여 VMware 가상 머신 또는 물리적 서버를 보호하는 경우 특정 오류 메시지가 나타날 수 있습니다. 이 문서에서는 [Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 VMware VM 및 실제 서버를 Azure에 복제하는 경우 발생할 수 있는 몇 가지 일반적인 문제를 설명합니다.

## <a name="initial-replication-issues"></a>초기 복제 문제

종종 원본 서버와 프로세스 서버 간 또는 프로세스 서버와 Azure 간의 연결 문제로 인해 초기 복제가 실패할 수 있습니다. 대부분의 경우 다음 섹션의 단계를 완료하여 이러한 문제를 해결할 수 있습니다.

### <a name="check-the-source-machine"></a>원본 컴퓨터 확인

다음 목록은 원본 컴퓨터를 확인하는 방법입니다.

*  원본 서버의 명령줄에서 다음 명령을 실행하여 Telnet으로 HTTPS 포트(기본 HTTPS 포트: 9443)를 통해 프로세스 서버를 ping합니다. 이 명령은 네트워크 연결 문제와 방화벽 포트 차단 문제가 있는지 확인합니다.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Telnet을 사용하여 연결을 테스트합니다. `ping`은 사용하지 않도록 합니다. 텔넷을 설치하지 않은 경우 [텔넷 클라이언트 설치](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)에 나열된 단계를 완료합니다.

   프로세스 서버에 연결할 수 없는 경우 프로세스 서버에서 인바운드 포트 9443을 허용합니다. 예를 들어, 네트워크에 경계 네트워크 또는 스크린된 서브넷이 있는 경우 프로세스 서버에서 인바운드 포트 9443을 허용해야 할 수 있습니다. 그런 다음, 문제가 여전히 발생하는지 확인합니다.

*  **InMage Scout VX Agent – Sentinel/OutpostStart** 서비스의 상태를 확인합니다. 서비스가 실행되고 있지 않으면 서비스를 시작하고 문제가 여전히 발생하는지 확인합니다.   

### <a name="check-the-process-server"></a>프로세스 서버 확인

다음 목록은 프로세스 서버를 확인하는 방법입니다.

*  **프로세스 서버가 데이터를 Azure로 적극적으로 밀어넣는지 확인합니다**.

   1. 프로세스 서버에서 작업 관리자를 엽니다(Ctrl+Shift+Esc 누르기).
   2. **성능** 탭을 선택하고 **리소스 모니터 열기** 링크를 선택합니다. 
   3. **리소스 모니터** 페이지에서 **네트워크** 탭을 선택합니다. **네트워크 활동이 있는 프로세스**에서 **cbengine.exe**가 현재 대용량 데이터를 전송하고 있는지 확인합니다.

        ![네트워크 활동이 있는 프로세스 아래에 볼륨을 표시하는 스크린샷](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   cbengine.exe가 대용량 데이터를 전송하고 있지 않으면 다음 섹션의 단계를 완료합니다.

*  **프로세스 서버가 Azure Blob 스토리지에 연결할 수 있는지 여부를 확인합니다**.

   **cbengine.exe**를 선택합니다. **TCP 연결** 아래에서 프로세스 서버로부터 Azure Blob 스토리지 URL로의 연결이 있는지 확인합니다.

   ![cbengine.exe와 Azure Blob 스토리지 URL 간의 연결을 보여 주는 스크린샷](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   프로세스 서버로부터 Azure Blob 스토리지 URL로의 연결이 없으면 제어판에서 **서비스**를 선택합니다. 다음 서비스가 실행 중인지 여부를 확인합니다.

   *  cxprocessserver
   *  InMage Scout VX Agent – Sentinel/Outpost
   *  Microsoft Azure Recovery Services 에이전트
   *  Microsoft Azure Site Recovery 서비스
   *  tmansvc

   실행되지 않고 있는 모든 서비스를 시작하거나 다시 시작합니다. 문제가 여전히 발생하는지 여부를 확인합니다.

*  **프로세스 서버가 포트 443을 사용하여 Azure 공용 IP 주소에 연결할 수 있는지 여부를 확인합니다**.

   %programfiles%\Microsoft Azure Recovery Services Agent\Temp에서 최신 CBEngineCurr.errlog 파일을 엽니다. 파일에서 **443** 또는 문자열 **connection attempt failed**를 검색합니다.

   ![Temp 폴더의 로그 오류를 표시하는 스크린샷](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   문제가 표시되면 프로세스 서버의 명령줄에서 Telnet을 사용하여 Azure 공용 IP 주소(이전 이미지에서는 IP 주소가 마스킹됨)를 ping합니다. 포트 443을 사용하여 CBEngineCurr.currLog 파일에서 Azure 공용 IP 주소를 찾을 수 있습니다.

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   연결할 수 없으면 다음 단계에 설명된 대로 액세스 문제가 방화벽 또는 프록시 설정으로 인한 것인지 확인합니다.

*  **프로세스 서버의 IP 주소 기반 방화벽이 액세스를 차단하는지 여부를 확인합니다**.

   서버에서 IP 주소 기반 방화벽 규칙을 사용하는 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)의 전체 목록을 다운로드합니다. IP 주소 범위를 방화벽 구성에 추가하여 방화벽이 Azure(및 기본 HTTPS 포트 443)와의 통신을 허용하도록 합니다. 구독하는 Azure 지역과 Azure 미국 서부 지역에 해당하는 IP 주소 범위를 허용합니다(액세스 제어 및 ID 관리에 사용됨).

*  **프로세스 서버의 URL 기반 방화벽이 액세스를 차단하는지 여부를 확인합니다**.

   서버에서 URL 기반 방화벽 규칙을 사용하는 경우 다음 표에 나열된 URL을 방화벽 구성에 추가합니다.

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **프로세스 서버의 프록시 설정이 액세스를 차단할지 여부를 확인합니다**.

   프록시 서버를 사용하는 경우 DNS 서버에서 프록시 서버 이름을 확인하는지 확인합니다. 구성 서버를 설정할 때 제공한 값을 확인하려면 레지스트리 키 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**로 이동합니다.

   다음으로, Azure Site Recovery에서 데이터를 보내는 데 동일한 설정을 사용하는지 확인합니다. 
      
   1. **Microsoft Azure Backup**을 검색합니다. 
   2. **Microsoft Azure Backup**을 열고 **작업** > **속성 변경**을 선택합니다. 
   3. **프록시 구성** 탭에 프록시 주소가 표시되어야 합니다. 프록시 주소는 레지스트리 설정에 표시된 프록시 주소와 동일해야 합니다. 그렇지 않은 경우 동일한 주소로 변경하세요.

*  **프로세스 서버에서 스로틀 대역폭이 제한되는지 여부를 확인합니다**.

   대역폭을 늘리고 문제가 여전히 발생하는지 확인합니다.

## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>원본 컴퓨터가 Azure Portal에 나열되지 않음

Site Recovery를 사용하여 복제를 사용하도록 설정할 원본 컴퓨터를 선택하려고 하면 다음과 같은 이유 중 하나로 컴퓨터를 계속 사용하지 못할 수 있습니다.

*  vCenter에 인스턴스 UUID가 같은 두 개의 가상 머신이 있는 경우 구성 서버에서 첫 번째로 검색된 가상 머신이 Azure Portal에 표시됩니다. 이 문제를 해결하려면 두 개의 가상 머신에 동일한 인스턴스 UUID가 없는지 확인합니다.
*  OVF 템플릿 또는 통합 설정을 사용하여 구성 서버를 설정하는 동안 올바른 vCenter 자격 증명을 추가했는지 확인합니다. 설치 중에 추가한 자격 증명을 확인하려면 [자동 검색에 대한 자격 증명 수정](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)을 참조하세요.
*  vCenter에 액세스하기 위해 제공된 권한에 필요한 사용 권한이 없으면 가상 머신을 검색하지 못할 수 있습니다. [자동 검색용 계정 준비](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)에 설명된 사용 권한을 vCenter 사용자 계정에 추가해야 합니다.
*  Site Recovery를 통해 가상 머신을 이미 보호하는 경우 포털에서 보호하기 위해 가상 머신을 선택할 수 없습니다. 포털에서 찾고 있는 가상 머신이 다른 사용자 또는 다른 구독을 통해 아직 보호되고 있지 않은지 확인합니다.

## <a name="protected-virtual-machines-arent-available-in-the-portal"></a>보호된 가상 머신은 포털에서 사용할 수 없습니다.

Site Recovery에서 복제된 가상 머신은 시스템에 중복된 항목이 있으면 Azure Portal에서 사용할 수 없습니다. 오래된 항목을 삭제하고 이 문제를 해결하는 방법에 대한 자세한 내용은 [Azure Site Recovery VMware-Azure: 중복되거나 오래된 항목을 정리하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 활발히 유지되는 커뮤니티가 있으며 엔지니어 중 하나가 도움을 줄 수 있습니다.
