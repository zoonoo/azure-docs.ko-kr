---
title: "VMware에서 Azure로의 Azure Site Recovery 문제 해결 | Microsoft Docs"
description: "Azure 가상 컴퓨터를 복제할 때 오류 문제 해결"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1e2452ccc6d3f1859a39e1ee09cdde32f53767ba
ms.contentlocale: ko-kr
ms.lasthandoff: 08/28/2017

---
# <a name="troubleshooting-mobility-service-push-install-issues"></a>모바일 서비스 강제 설치 문제 해결

이 문서에서는 보호를 사용하기 위해 원본 서버에 모바일 서비스를 설치하려고 할 때 발생하는 일반적인 문제에 대해 자세히 설명합니다.

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(오류 코드 95107) 보호를 사용하도록 설정할 수 없습니다.
**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95107 </br>***메시지 -*** 원본 컴퓨터에 모바일 서비스를 강제 설치하지 못했습니다(오류 코드: ***EP0858***). <br> 모바일 서비스를 설치하기 위해 제공된 자격 증명이 잘못되었거나 사용자 계정에 권한이 부족 | 원본 컴퓨터에 모바일 서비스를 설치하기 위해 제공된 자격 증명이 잘못됨 | 구성 서버에서 원본 컴퓨터에 대해 제공된 사용자 자격 증명이 올바른지 확인하세요. <br> 사용자 자격 증명을 추가/편집하려면 구성 서버 > Cspsconfigtool 아이콘 > 계정 관리로 이동합니다. </br> 또한 강제 설치를 성공적으로 완료하려면 아래 필수 조건을 확인해야 합니다.

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(오류 코드 95015) 보호를 사용하도록 설정할 수 없습니다.
**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95105 </br>***메시지 -*** 원본 컴퓨터에 모바일 서비스를 강제 설치하지 못했습니다(오류 코드: ***EP0856***). <br> 원본 컴퓨터에서 [파일 및 프린터 공유]가 허용되지 않거나 프로세스 서버와 원본 컴퓨터 간에 네트워크 연결 문제가 있음| 파일 및 인쇄 공유 사용 안 함 | Windows 방화벽에서 원본 컴퓨터의 [파일 및 프린터 공유]를 허용하고 원본 컴퓨터로 이동하여 > [Windows 방화벽 설정] 아래에서 > [방화벽을 통해 앱 또는 기능 허용] > [모든 프로필에 대해 파일 및 프린터 공유]를 선택합니다. </br> 또한 강제 설치를 성공적으로 완료하려면 아래 필수 조건을 확인해야 합니다.

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(오류 코드 95117) 보호를 사용하도록 설정할 수 없습니다.
**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95117 </br>***메시지 -*** 원본 컴퓨터에 모바일 서비스를 강제 설치하지 못했습니다(오류 코드: ***EP0865***). <br> 원본 컴퓨터가 실행 중이 아니거나 프로세스 서버와 원본 컴퓨터 간에 네트워크 연결 문제가 있음 | 프로세스 서버와 원본 서버 간의 네트워크 연결 | 프로세스 서버와 원본 서버 간의 연결을 확인합니다. </br> 또한 강제 설치를 성공적으로 완료하려면 아래 필수 조건을 확인해야 합니다.

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(오류 코드 95103) 보호를 사용하도록 설정할 수 없습니다.
**오류 코드** | **가능한 원인** | **오류 관련 권장 사항**
--- | --- | ---
95103 </br>***메시지 -*** 원본 컴퓨터에 모바일 서비스를 강제 설치하지 못했습니다(오류 코드: ***EP0854***). <br> 원본 컴퓨터에서 “WMI(Windows Management Instrumentation)”가 허용되지 않거나 프로세스 서버와 원본 컴퓨터 간에 네트워크 연결 문제가 있음| Windows 방화벽에서 WMI(Windows Management Instrumentation) 차단됨 | Windows 방화벽에서 WMI(Windows Management Instrumentation)를 허용합니다. [Windows 방화벽 설정] 아래에서 > [방화벽을 통해 앱 또는 기능 허용] > [모든 프로필에 대해 WMI 선택]을 선택합니다. </br> 또한 강제 설치를 성공적으로 완료하려면 아래 필수 조건을 확인해야 합니다.

## <a name="check-push-install-logs-for-errors"></a>강제 설치 로그에서 오류 확인

구성/프로세스 서버에서 <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\에 있는 'PushinstallService' 파일로 이동하여 문제 원인을 파악하고 아래 문제 해결 단계에 따라 문제를 해결합니다.</br>
![pushiinstalllogs](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-install-pre-requisites-for-windows"></a>Windows에 대한 강제 설치 필수 조건
### <a name="ensure-file-and-printer-sharing-is-enabled"></a>[파일 및 프린터 공유]가 사용하도록 설정되었는지 확인
Windows 방화벽에서 원본 컴퓨터의 [파일 및 프린터 공유]와 [WMI(Windows Management Instrumentation)] 허용 </br>
#### <a name="if-source-machine-is-domain-joined-br"></a>원본 컴퓨터가 도메인에 가입된 경우 </br>
GPMC(그룹 정책 관리 콘솔)를 사용하여 방화벽 설정을 구성합니다.
1. 관리자로 Active Directory 도메인 컴퓨터에 로그인하고 그룹 정책 관리 콘솔(GPMC.MSC, [시작] > [실행]에서 실행)을 엽니다.</br>
3. GPMC가 설치되지 않은 경우 링크에 따라 [GPMC를 설치](https://technet.microsoft.com/library/cc725932.aspx)합니다. </br>
4. GPMC 콘솔 트리에서 포리스트 및 도메인의 그룹 정책 개체를 두 번 클릭하고 [기본 도메인 정책]으로 이동합니다. </br>
![gpmc1](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
5. 마우스 오른쪽 단추로 [기본 도메인 정책] > [편집]을 클릭하면 > 새 [그룹 정책 관리 편집기] 창이 열립니다. </br>
![gpmc2](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
6. 그룹 정책 관리 편집기에서 [컴퓨터 구성] > [정책] > [관리 템플릿] > [네트워크] > [네트워크 연결] > [Windows 방화벽]으로 이동합니다. </br>
![gpmc3](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
7. 도메인 프로필 및 표준 프로필에 대해 다음 설정을 사용 </br>
a)  예외 [Windows 방화벽: 인바운드 파일 및 프린터 공유 예외 허용]을 두 번 클릭합니다. [사용]을 선택하고 [확인]을 클릭합니다. </br>
a)  예외 [Windows 방화벽: 인바운드 원격 관리 예외 허용]을 두 번 클릭합니다. [사용]을 선택하고 [확인]을 클릭합니다. </br>
![gpmc4](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

###### <a name="if-source-machine-is-not-domain-joined-and-part-of-workgroup-br"></a>원본 컴퓨터가 도메인에 가입되지 않고 작업 그룹의 일부가 아닌 경우 </br>
원격 컴퓨터에서 방화벽 설정 구성(작업 그룹의 경우):
1. 원본 컴퓨터로 이동합니다.</br>
2. [Windows 방화벽 설정] 아래에서 > [방화벽을 통해 앱 또는 기능 허용] > [모든 프로필에 대해 파일 및 프린터 공유]를 선택합니다. </br>
3. [Windows 방화벽 설정] 아래에서 > [방화벽을 통해 앱 또는 기능 허용] > [모든 프로필에 대해 WMI 선택]을 선택합니다. </br>

#### <a name="disable-remote-user-account-control-uac"></a>원격 UAC(사용자 계정 컨트롤)를 사용하지 않도록 설정
모바일 서비스를 푸시하기 위해 레지스트리 키를 사용하여 UAC를 사용하지 않도록 설정합니다.
1. [시작] > [실행] > regedit 입력 > ENTER 키 클릭
2. 다음 레지스트리 하위 키를 찾아 클릭합니다. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. LocalAccountTokenFilterPolicy 레지스트리 항목이 존재하지 않는 경우 다음 단계를 따르세요.
4. [편집] 메뉴에서 > [새로 만들기] > DWORD 값을 클릭합니다.
5. LocalAccountTokenFilterPolicy를 입력하고 ENTER 키를 누릅니다.
6. LocalAccountTokenFilterPolicy를 마우스 오른쪽 단추로 클릭하고 [수정]을 클릭합니다.
7. 값 데이터 상자에 1을 입력한 다음 [확인]을 클릭합니다.
8. 레지스트리 편집기를 종료합니다.


## <a name="push-install-pre-requisites-for-linux"></a>Linux에 대한 강제 설치 필수 조건:

1. 원본 Linux 서버에 루트 사용자를 만듭니다. (푸시 설치 및 업데이트의 경우에만 이 계정을 사용합니다.)</br>
2. 원본 Linux 서버의 /etc/hosts 파일에 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소에 매핑하는 항목이 있는지 확인합니다. </br>
3. 최신 openssh, openssh-server 및 openssl 패키지가 원본 Linux 서버에 설치되어 있는지 확인합니다. </br>
Ssh 포트 22가 활성화되어 실행 중인지 확인합니다. </br>
4. 원본 서버에 에이전트의 부실 항목이 있는지 확인하고 오래된 에이전트를 제거하고 서버를 다시 부팅한 후 에이전트를 다시 설치합니다. </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>sshd_config 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정합니다.
1. 원본 서버에서 루트로 로그인합니다. </br>
2. /etc/ssh/sshd_config 파일에서</br> PasswordAuthentication으로 시작하는 줄을 찾습니다. </br>
3. d.   줄의 주석 처리를 제거하고 값을 “아니요”에서 “예”로 변경합니다. </br>
![Linux1](./media/site-recovery-protection-common-errors/linux1.png)
4. “Subsystem”으로 시작하는 줄을 찾아서 주석 처리를 제거합니다. </br>
![Linux2](./media/site-recovery-protection-common-errors/linux2.png)
5. 변경 내용을 저장하고 sshd 서비스를 다시 시작합니다. </br>

## <a name="push-installation-checks-on-configurationprocess-server"></a>구성/프로세스 서버에서 강제 설치를 확인합니다.
#### <a name="validate-credentials-for-discovery-and-installation"></a>검색 및 설치에 대한 자격 증명 유효성 검사

1. 구성 서버에서 Cspsconfigtool을 시작합니다.</br>
![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. 보호에 사용된 계정에 원본 컴퓨터에 대한 관리자 권한이 있는지 확인합니다. </br>

#### <a name="check-connectivity-between-process-server-and-source-server"></a>프로세스 서버와 원본 서버 간의 연결 확인
1. 프로세스 서버에서 인터넷 연결을 확인합니다.
2. wbemtest.exe를 사용하여 WMI 연결을 확인합니다. </br>
프로세스 서버에서 [시작] > [실행] > [wbemtest.exe]을 클릭하면 > 표시된 것처럼 Windows Management Instrumentation 테스터 창이 열립니다.</br>
   ![WMItestConnect1](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
[연결]을 클릭하고 > 네임스페이스 입력 사용자 이름 및 암호에 원본 서버 IP를 입력합니다(원본 컴퓨터가 도메인에 가입된 경우 사용자 이름과 도메인 이름을 “domainName\username”으로 입력합니다. 원본 컴퓨터가 작업 그룹에 있는 경우 사용자 이름만 제공합니다). 인증 수준을 패킷 개인 정보로 선택합니다. </br>
![WMItestConnect2](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
   [연결]을 클릭합니다. 이제 제공된 데이터를 사용한 WMI 연결에 성공하며 아래와 같이 Windows Management Instrumentation 테스트 창이 표시됩니다. </br>
   ![WMItestConnect3](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
</br>
   WMI 연결이 실패한 경우 오류 메시지가 팝업됩니다. 아래 스크린샷은 Windows 방화벽 허용 앱에서 WMI/원격 관리가 활성화되지 않은 경우 실패한 시도를 보여 줍니다. </br>
   ![WMItestConnect4](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
</br>

3. WMI 상태 및 연결을 확인합니다.</br>
구성/프로세스 서버에서, </br>
[시작] > [실행] > [wmimgmt.msc] > [작업] > [추가 작업] > [connect to another computer(source machine)(다른 컴퓨터(원본 컴퓨터)에 연결)]을 클릭합니다. </br>
보호에 사용된 계정의 자격 증명을 입력하고 연결이 제대로 되는지 확인합니다. </br>

#### <a name="verify-network-shared-folders-of-source-machine-is-accessible-from-process-server-ps-remotely-using-specified-credentials"></a>지정된 자격 증명을 사용하여 원본 컴퓨터의 네트워크 공유 폴더를 PS(프로세스 서버)에서 원격으로 액세스할 수 있는지 확인합니다.
  1. PS(프로세스 서버) 컴퓨터에 로그인하고 파일 탐색기를 열고 > 주소 표시줄에 > "\\\source-machine-ip\C$"를 입력하고 > Enter 키를 클릭합니다. </br>
  ![Fileshare1](./media/site-recovery-protection-common-errors/fileshare1.png) </br>
  2. 파일 탐색기에서 자격 증명을 요청합니다. 사용자 이름과 암호를 입력하고 > [확인]을 입력합니다.</br>
   원본 컴퓨터가 도메인에 가입된 경우 사용자 이름과 도메인 이름을 “domainName\username”으로 입력합니다.</br>
   원본 컴퓨터가 작업 그룹에 있는 경우 “username”만 제공합니다. </br>
  ![Fileshare2](./media/site-recovery-protection-common-errors/fileshare2.png) </br>
  3. 연결에 성공한 경우 PS(프로세스 서버)에서 원본 컴퓨터의 폴더를 원격으로 볼 수 있습니다. </br>
  ![Fileshare3](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> 연결에 실패한 경우 모든 필수 조건을 충족하는지 확인하세요.
>

“Windows Management Instrumentation”을 열지 않으려는 경우 원본 컴퓨터에 모바일 서비스를 수동으로 설치할 수도 있습니다.</br> [GUI를 통해 모바일 서비스 수동 설치](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) </br>
[구성 관리자를 통한 설치 지침](site-recovery-install-mobility-service-using-sccm.md) </br>

## <a name="next-steps"></a>다음 단계
- [VMware 가상 컴퓨터의 복제 설정](vmware-walkthrough-enable-replication.md)

