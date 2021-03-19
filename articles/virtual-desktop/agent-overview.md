---
title: Windows 가상 데스크톱 에이전트 시작
description: Windows 가상 데스크톱 에이전트 및 업데이트 프로세스에 대 한 개요입니다.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: ecc4a5a17186eddd4223715462b14399bdf702df
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601893"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Windows 가상 데스크톱 에이전트 시작

Windows 가상 데스크톱 서비스 프레임 워크에는 원격 데스크톱 클라이언트, 서비스 및 가상 컴퓨터의 세 가지 주요 구성 요소가 있습니다. 이러한 가상 머신은 Windows 가상 데스크톱 에이전트 및 에이전트 부팅 시스템이 설치 된 고객 구독에 있습니다. 에이전트는 서비스와 가상 컴퓨터 간의 중간 communicator 역할을 하며 연결을 사용 하도록 설정 합니다. 따라서 에이전트 설치, 업데이트 또는 구성에 문제가 발생 하는 경우 가상 컴퓨터에서 서비스에 연결할 수 없습니다. 에이전트 부팅 로더는 에이전트를 로드 하는 실행 파일입니다. 

이 문서에서는 에이전트 설치 및 업데이트 프로세스에 대 한 간략 한 개요를 제공 합니다.

>[!NOTE]
>이 설명서는 FSLogix 에이전트 또는 원격 데스크톱 클라이언트 에이전트에는 적합 하지 않습니다.


## <a name="initial-installation-process"></a>초기 설치 프로세스

Windows 가상 데스크톱 에이전트는 처음에 두 가지 방법 중 하나로 설치 됩니다. Azure Portal 및 Azure Marketplace에서 Vm (가상 머신)을 프로 비전 하는 경우 에이전트 및 에이전트 부팅 시스템이 자동으로 설치 됩니다. PowerShell을 사용 하 여 Vm을 프로 비전 하는 경우 PowerShell을 사용 하 여 [Windows 가상 데스크톱 호스트 풀을 만들](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)때 에이전트 및 에이전트 부팅 파일 .msi 파일을 수동으로 다운로드 해야 합니다. 에이전트가 설치 되 면 Windows 가상 데스크톱 side-by-side stack 및 Geneva Monitoring agent도 동시에 설치 됩니다. Side-by-side 스택 구성 요소는 사용자가 역방향 서버 간 연결을 안전 하 게 설정 하는 데 필요 합니다. Geneva Monitoring agent는 에이전트의 상태를 모니터링 합니다. 이러한 세 구성 요소는 모두 제대로 작동 하려면 종단 간 사용자 연결이 필요 합니다.

>[!IMPORTANT]
>Windows 가상 데스크톱 에이전트, side-by-side 스택 및 Geneva Monitoring agent를 성공적으로 설치 하려면 [필요한 url 목록](safe-url-list.md#virtual-machines)에 나열 된 모든 url을 차단 해제 해야 합니다. Windows 가상 데스크톱 서비스를 사용 하려면 이러한 Url의 차단을 해제 해야 합니다.

## <a name="agent-update-process"></a>에이전트 업데이트 프로세스

Windows 가상 데스크톱 서비스는 업데이트를 사용할 수 있게 될 때마다 에이전트를 업데이트 합니다. 에이전트 업데이트에는 새로운 기능이 나 이전 문제에 대 한 수정 사항이 포함 될 수 있습니다. Windows 가상 데스크톱 에이전트의 초기 버전이 설치 되 면 에이전트는 Windows 가상 데스크톱 서비스를 정기적으로 쿼리하여 최신 버전의 에이전트, 스택 또는 모니터링 구성 요소를 사용할 수 있는지 확인 합니다. 최신 버전의 구성 요소를 이미 배포한 경우에는 업데이트 된 구성 요소가 자동으로 설치 됩니다.

새 버전의 에이전트는 일정 한 간격으로 모든 Azure 구독에 weeklong 기간에 배포 됩니다. 이러한 업데이트 기간을 "항공편" 이라고 합니다. 비행이 발생 하면 호스트 풀의 Vm이 서로 다른 시간에 에이전트 업데이트를 수신 하는 것을 볼 수 있습니다. 모든 구독의 모든 VM 에이전트는 배포 기간이 끝날 때 업데이트 됩니다. Windows 가상 데스크톱 플 라이팅 시스템은 에이전트 업데이트의 안정성 및 품질을 보장 하 여 서비스의 안정성을 향상 시킵니다.


>[!NOTE]
>- Geneva Monitoring agent가 최신 버전으로 업데이트 되 면 새 모니터링 에이전트에 대 한 새 작업을 만들기 전에 이전 GenevaTask 작업을 찾아서 사용 하지 않도록 설정 합니다. 최신 버전의 모니터링 에이전트가 문제를 해결 하기 위해 이전 버전으로 되돌려야 하는 경우 이전 버전의 모니터링 에이전트가 삭제 되지 않습니다. 최신 버전에 문제가 있는 경우 모니터링 데이터를 계속 해 서 배달 하려면 이전 모니터링 에이전트를 다시 사용 하도록 설정 해야 합니다. 업데이트 전에 마지막으로 설치한 모니터의 모든 버전은 VM에서 삭제 됩니다.
>- VM은 한 번에 세 버전의 병렬 스택을 유지 합니다. 업데이트에 문제가 발생 하는 경우 빠른 복구를 수행할 수 있습니다. 스택이 업데이트 될 때마다 가장 오래 된 버전의 스택이 VM에서 제거 됩니다.

이 업데이트 설치는 일반적으로 새 VM에서 2-3 분 동안 지속 되며 VM이 연결을 손실 하거나 종료 하지 않아야 합니다. 이 업데이트 프로세스는 Windows 가상 데스크톱 (클래식) 및 Azure Resource Manager를 사용 하는 최신 버전의 Windows 가상 데스크톱에 적용 됩니다.

## <a name="next-steps"></a>다음 단계

이제 Windows 가상 데스크톱 에이전트에 대해 더 잘 이해 했으므로 다음을 수행 하는 데 도움이 될 수 있는 몇 가지 리소스가 있습니다.

- 에이전트 또는 연결 관련 문제가 발생 하는 경우 [Windows 가상 데스크톱 에이전트 문제 해결 가이드](troubleshoot-agent.md)를 확인 하세요.
