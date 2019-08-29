---
layout: LandingPage
description: 가상 머신 배포 문제를 해결하는 방법을 알아봅니다.
title: Azure Virtual Machines 설명서 문제 해결 | Microsoft Docs
services: virtual-machines
author: genlin
manager: gwallace
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: ed1873fa8e74b2af5734411dc80aeb9089b61a7e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080448"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Azure 가상 머신 문제 해결

## <a name="tools-for-troubleshooting"></a>문제 해결 도구

- [직렬 콘솔](serial-console-windows.md)
- [부팅 진단](boot-diagnostics.md)
- [Windows VM: 문제 해결을 위해 OS 디스크를 다른 VM에 연결](troubleshoot-recovery-disks-portal-windows.md)
- [Linux VM: 문제 해결을 위해 OS 디스크를 다른 VM에 연결](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>VM에 연결할 수 없음

### <a name="windows"></a>Windows

**일반 솔루션**

- [RDP 다시 설정](reset-rdp.md)
- [RDP 문제 해결](troubleshoot-rdp-connection.md)
- [자세한 RDP 문제 해결](detailed-troubleshoot-rdp.md)

**RDP 오류**

- [라이선스 서버 없음](troubleshoot-rdp-no-license-server.md)
- [내부](Troubleshoot-rdp-internal-error.md)
- [인증 오류](troubleshoot-authentication-error-rdp-vm.md)
- [특정 오류 해결](troubleshoot-specific-rdp-errors.md)

**VM 부팅 오류**

* [BitLocker 부팅 오류](troubleshoot-bitlocker-boot-error.md) 
* [Windows 부팅 중에 “파일 시스템 확인” 표시](troubleshoot-check-disk-boot-error.md)
* [블루 스크린 오류](troubleshoot-common-blue-screen-error.md)
* [VM 시작이 “Windows 준비 중”에서 중단되는 경우](troubleshoot-vm-boot-configure-update.md)
* [블루 스크린의 “중요 서비스 실패” 오류](troubleshoot-critical-service-failed-boot-error.md)
* [다시 부팅 루프 문제](troubleshoot-reboot-loop.md)
* [VM 시작이 Windows 업데이트 단계에서 중단되는 경우](troubleshoot-stuck-updating-boot-error.md)
* [안전모드로 VM 부팅](troubleshoot-rdp-safe-mode.md)

**기타**
- [Windows VM 오프라인에 대한 VM 암호 다시 설정](reset-local-password-without-agent.md)
- [잘못된 구성 후 NIC 다시 설정](reset-network-interface.md)

### <a name="linux"></a>Linux

- [SSH 문제 해결](troubleshoot-ssh-connection.md)
- [자세한 SSH 문제 해결](detailed-troubleshoot-ssh-connection.md)
- [일반적인 오류 메시지](error-messages.md)
- [Linux VM 오프라인에 대한 VM 암호 다시 설정](reset-password.md)

## <a name="vm-deployment-issues"></a>VM 배포 문제

- [할당 오류](allocation-failure.md)
- VM 재배포
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- 배포 문제 해결
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [디바이스 이름이 변경됨](troubleshoot-device-names-problems.md)
- [Windows VM 에이전트 오프라인 설치](install-vm-agent-offline.md)
- [VM 다시 시작 또는 크기 조정](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>VM 성능 문제
- [VM 관련 성능 문제](performance-diagnostics.md)
- Windows
    - [PerfInsights를 사용하는 방법](how-to-use-perfinsights.md)
    - [성능 진단 확장](performance-diagnostics-vm-extension.md)
- Linux
    - [PerfInsights를 사용하는 방법](how-to-use-perfinsights-linux.md)