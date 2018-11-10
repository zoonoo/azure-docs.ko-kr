---
title: Azure로 장애 조치(failover) 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 장애 조치(failover)하는 동안 발생하는 일반적인 문제를 해결하는 방법을 설명합니다.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 09/11/2018
ms.author: ponatara
ms.openlocfilehash: de0b3a51ae7c7cca91366b955c5fa74963d95d27
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211675"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>가상 머신을 Azure로 장애 조치 시 오류 문제 해결

가상 컴퓨터를 Azure로 장애 조치하는 동안 다음 오류 중 하나가 나타날 수 있습니다. 문제를 해결하려면 각 오류 조건에 대해 설명된 단계를 따르세요.

## <a name="failover-failed-with-error-id-28031"></a>장애 조치 실패(오류 ID 28031)

Site Recovery가 Azure에서 장애 조치된 가상 머신을 만들 수 없습니다. 다음 이유 중 하나로 인해 발생할 수 있습니다.

* 가상 머신을 만드는 데 사용할 수 있는 할당량이 충분하지 않습니다. [구독] -&gt; [사용량 + 할당량]으로 이동하여 사용할 수 있는 할당량을 확인할 수 있습니다. [새로운 지원 요청](http://aka.ms/getazuresupport)을 열어 할당량을 늘릴 수 있습니다.

* 동일한 가용성 집합에 다른 크기 제품군의 가상 머신을 장애 조치하려고 합니다. 동일한 가용성 집합의 모든 가상 머신에 대해 동일한 크기 제품군을 선택했는지 확인합니다. 가상 머신의 Compute 및 네트워크 설정으로 이동하여 크기를 변경한 후 장애 조치를 다시 시도합니다.

* 구독에는 가상 머신의 생성을 방지하는 정책이 있습니다. 가상 머신의 생성을 허용하도록 정책을 변경한 후 장애 조치를 다시 시도합니다.

## <a name="failover-failed-with-error-id-28092"></a>장애 조치 실패(오류 ID 28092)

Site Recovery가 장애 조치된 가상 머신에 대해 네트워크 인터페이스를 만들 수 없습니다. 구독에서 네트워크 인터페이스를 만드는 데 사용할 수 있는 충분한 할당량이 있는지 확인합니다. [구독] -> [사용량 + 할당량]으로 이동하여 사용할 수 있는 할당량을 확인할 수 있습니다. [새로운 지원 요청](http://aka.ms/getazuresupport)을 열어 할당량을 늘릴 수 있습니다. 할당량이 충분하고 일시적인 문제일 수 있다면 작업을 다시 시도합니다. 다시 시도 후에도 문제가 계속되면 이 문서의 끝에 의견을 남겨 주세요.  

## <a name="failover-failed-with-error-id-70038"></a>장애 조치 실패(오류 ID 70038)

Site Recovery가 Azure에서 장애 조치된 클래식 가상 머신을 만들 수 없습니다. 다음 때문에 발생할 수 있습니다.

* 만들 가상 머신에 필요한 가상 네트워크와 같은 리소스 중 하나가 존재하지 않습니다. 가상 머신의 Compute 및 네트워크 설정에 제공된 대로 가상 네트워크를 만들거나 이미 존재하는 가상 네트워크로 설정을 수정한 다음 장애 조치를 다시 시도합니다.

## <a name="unable-to-connectrdpssh---vm-connect-button-grayed-out"></a>연결할 수 없음/RDP/SSH - VM 연결 단추가 회색으로 표시됨

Azure에서 장애 조치(failover)된 VM의 **연결** 단추가 회색으로 표시되고 Express 경로 또는 사이트 간 VPN 연결을 통해 Azure에 연결되지 않은 경우에는 다음을 수행합니다.

1. **가상 머신** > **네트워킹**으로 차례로 이동하고, 필요한 네트워크 인터페이스의 이름을 클릭합니다.  ![네트워크 인터페이스](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. **IP 구성**으로 이동한 다음, 필요한 IP 구성의 이름 필드를 클릭합니다. ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. 공용 IP 주소를 사용하도록 설정하려면 **사용**을 클릭합니다. ![IP 사용](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. **필수 설정 구성** > **새로 만들기**를 차례로 클릭합니다. ![새로 만들기](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. 공용 주소의 이름을 입력하고, **SKU** 및 **할당**에 대한 기본 옵션을 선택한 다음, **확인**을 클릭합니다.
6. 이제 변경 내용을 저장하려면 **저장**을 클릭합니다.
7. 패널을 닫고, 가상 머신의 **개요** 섹션으로 이동하여 RDP를 연결합니다.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>연결할 수 없음/RDP/SSH - VM 연결 단추 사용 가능

Azure에서 장애 조치(failover)된 VM의 **연결** 단추를 사용할 수 있는 경우(회색으로 표시되지 않음), 가상 머신에서 **부트 진단**을 검사하고 [이 문서](../virtual-machines/windows/boot-diagnostics.md)에 나열된 오류를 확인합니다.

1. 가상 머신이 시작되지 않은 경우 이전의 복구 지점으로 장애 조치를 시도합니다.
2. 가상 머신 내 응용 프로그램이 가동되지 않는 경우 응용 프로그램 일치 복구 지점으로 장애 조치를 시도합니다.
3. 가상 머신이 도메인에 조인되어 있는 경우 도메인 컨트롤러가 정확하게 작동하는지 확인합니다. 이렇게 하려면 아래 제공된 단계를 따릅니다.

    a. 동일한 네트워크에 새 가상 머신을 만듭니다.

    b.  장애 조치(failover)된 가상 머신이 가동되어야 하는 동일한 도메인에 새 가상 머신이 가입할 수 있는지 확인합니다.

    다. 도메인 컨트롤러가 정확하게 작동하지 **않는** 경우 로컬 관리자 계정을 사용하여 장애 조치(failover)된 가상 머신에 로그인합니다.
4. 사용자 지정 DNS 서버를 사용하는 경우 연결할 수 있는지 확인합니다. 이렇게 하려면 아래 제공된 단계를 따릅니다.

    a. 동일한 네트워크에 새 가상 머신을 만듭니다.

    b. 가상 머신이 사용자 지정 DNS 서버를 사용하여 이름 확인을 수행할 수 있는지 확인합니다.

>[!Note]
>부트 진단 이외의 설정을 사용하도록 설정하려면 장애 조치 전에 Azure VM 에이전트를 가상 머신에 설치해야 합니다.

## <a name="unexpected-shutdown-message-event-id-6008"></a>예기치 않은 시스템 종료 메시지(이벤트 ID 6008)

장애 조치(failover) 후 Windows VM을 부팅할 때 복구된 VM에 예기치 않은 시스템 종료 메시지를 수신하는 경우 장애 조치(failover)에 사용된 복구 지점에서 VM 종료 상태가 캡처되지 않았음을 나타냅니다. 이 오류는 VM이 완전히 종료되지 않은 시점으로 복구할 때 발생합니다.

일반적으로 문제를 유발하지는 않으며, 계획되지 않은 장애 조치(failover)의 경우 무시할 수 있습니다. 계획된 장애 조치(failover)의 경우 장애 조치(failover) 전에 VM이 제대로 종료되었는지 확인하고 온-프레미스에서 보류 중인 복제 데이터가 Azure에 전송되는 데 충분한 시간을 제공합니다. 그런 다음, [장애 조치(failover) 화면](site-recovery-failover.md#run-a-failover)의 **최신** 옵션을 사용하여 Azure에서 보류 중인 데이터가 복구 지점으로 처리되어 VM 장애 조치(failover)에 사용되도록 합니다.

## <a name="retaining-drive-letter-after-failover"></a>장애 조치 후 드라이브 문자 유지
장애 조치(failover) 후 가상 머신에서 드라이브 문자를 유지하려면 온-프레미스 가상 머신에 대한 **SAN 정책**을 **OnlineAll**로 설정하면 됩니다. [자세히 알아보기](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="next-steps"></a>다음 단계
- [Windows VM에 대한 RDP 연결](../virtual-machines/windows/troubleshoot-rdp-connection.md) 문제 해결
- [Linux VM에 대한 SSH 연결](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md) 문제 해결

도움이 더 필요한 경우 [Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 쿼리를 게시하거나 이 문서의 끝에 의견을 남기세요. 도움을 드릴 수 있도록 커뮤니티를 운영 중입니다.
