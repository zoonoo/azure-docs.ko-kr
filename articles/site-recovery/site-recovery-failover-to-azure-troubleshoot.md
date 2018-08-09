---
title: Azure로 장애 조치(failover) 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure로 장애 조치(failover) 시 일반적인 오류 문제를 해결하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: b7b5dcd88b6e4e09dd9beb21e83ef405df148115
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443387"
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

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>가상 머신의 연결 단추가 회색으로 표시되어 장애 조치된 가상 머신에 RDP/SSH를 연결할 수 없음

[연결] 단추가 회색으로 표시되고 ExpressRoute 또는 사이트 간 VPN 연결을 통해 Azure에 연결되지 않은 경우 다음을 수행합니다.

1. **가상 머신** > **네트워킹**으로 차례로 이동하고, 필요한 네트워크 인터페이스의 이름을 클릭합니다.  ![네트워크 인터페이스](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
1. **IP 구성**으로 이동한 다음, 필요한 IP 구성의 이름 필드를 클릭합니다. ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
1. 공용 IP 주소를 사용하도록 설정하려면 **사용**을 클릭합니다. ![IP 사용](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
1. **필수 설정 구성** > **새로 만들기**를 차례로 클릭합니다. ![새로 만들기](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
1. 공용 주소의 이름을 입력하고, **SKU** 및 **할당**에 대한 기본 옵션을 선택한 다음, **확인**을 클릭합니다.
1. 이제 변경 내용을 저장하려면 **저장**을 클릭합니다.
1. 패널을 닫고, 가상 머신의 **개요** 섹션으로 이동하여 RDP를 연결합니다.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>가상 머신에서 연결 단추를 사용할 수 있지만(회색으로 표시되지 않음) 장애 조치된 가상 머신에 RDP/SSH를 연결할 수 없음

Virtual Machine에서 **부트 진단**을 확인하고, 이 문서에 나열된 오류를 확인합니다.

1. 가상 머신이 시작되지 않은 경우 이전의 복구 지점으로 장애 조치를 시도합니다.
1. 가상 머신 내 응용 프로그램이 가동되지 않는 경우 응용 프로그램 일치 복구 지점으로 장애 조치를 시도합니다.
1. 가상 머신이 도메인에 조인되어 있는 경우 도메인 컨트롤러가 정확하게 작동하는지 확인합니다. 이렇게 하려면 아래에 제공된 단계를 따릅니다.
    a. 동일한 네트워크에 새 가상 머신을 만듭니다.

    나.  장애 조치된 가상 머신이 가동되어야 하는 동일한 도메인에 조인할 수 있는지 확인합니다.

    다. 도메인 컨트롤러가 정확하게 **작동하지 않는 경우** 로컬 관리자 계정을 사용하여 장애 조치된 가상 머신에 로그인합니다.
1. 사용자 지정 DNS 서버를 사용하는 경우 연결할 수 있는지 확인합니다. 이렇게 하려면 아래에 제공된 단계를 따릅니다.
    a. 동일한 네트워크에 새 가상 머신을 만듭니다. b. 가상 머신에서 사용자 지정 DNS 서버를 사용하여 이름 확인을 수행할 수 있는지 확인합니다.

>[!Note]
>부트 진단 이외의 설정을 사용하도록 설정하려면 장애 조치 전에 Azure VM 에이전트를 가상 머신에 설치해야 합니다.

## <a name="next-steps"></a>다음 단계

도움이 더 필요한 경우 [Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 쿼리를 게시하거나 이 문서의 끝에 의견을 남기세요. 도움을 드릴 수 있도록 커뮤니티를 운영 중입니다.
