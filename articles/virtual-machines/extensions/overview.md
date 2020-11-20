---
title: Azure 가상 머신 확장 및 기능
description: Azure VM 확장에 대 한 자세한 정보
services: virtual-machines
author: amjads1
ms.service: virtual-machines
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/03/2020
ms.author: amjads
ms.openlocfilehash: b1dd26fce2e0a761ceed211933cb79ce518905e2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965887"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure 가상 머신 확장 및 기능
확장은 Azure Vm에서 배포 후 구성 및 자동화를 제공 하는 작은 응용 프로그램입니다. Azure 플랫폼은 VM 구성, 모니터링, 보안 및 유틸리티 응용 프로그램을 다루는 많은 확장을 호스팅합니다. 게시자는 응용 프로그램을 사용 하 여 확장으로 래핑하고 설치를 간소화 합니다. 필수 매개 변수만 제공 하기만 하면 됩니다. 

## <a name="how-can-i-find-what-extensions-are-available"></a>사용할 수 있는 확장을 찾으려면 어떻게 할까요?
왼쪽 메뉴에서 **확장** 을 선택 하 여 VM을 선택 하 여 사용 가능한 확장을 볼 수 있습니다. 확장의 전체 목록을 꺼내려면 [Linux 용 Vm 확장 검색](features-linux.md) 및 [Windows 용 vm 확장 검색](features-windows.md)을 참조 하세요.

## <a name="how-can-i-install-an-extension"></a>확장을 설치하려면 어떻게 해야 할까요?
Azure CLI, PowerShell, 리소스 관리자 템플릿 및 Azure Portal를 사용 하 여 Azure VM 확장을 관리할 수 있습니다. 확장을 시도 하려면 Azure Portal으로 이동 하 여 사용자 지정 스크립트 확장을 선택한 다음 명령 또는 스크립트를 전달 하 여 확장을 실행 합니다.

자세한 내용은 [Windows 사용자 지정 스크립트 확장](custom-script-windows.md) 및 [Linux 사용자 지정 스크립트 확장](custom-script-linux.md)을 참조 하세요.

## <a name="how-do-i-manage-extension-application-lifecycle"></a>확장 애플리케이션 수명 주기를 관리하려면 어떻게 해야 할까요?
VM에 직접 연결 하 여 확장을 설치 하거나 삭제할 필요가 없습니다. Azure 확장 수명 주기는 VM 외부에서 관리 되며 Azure 플랫폼에 통합 됩니다.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>확장에 대해 고려해야 할 다른 사항은 무엇인가요?
일부 개별 VM 확장 애플리케이션에는 엔드포인트 액세스와 같은 자체 환경 필수 구성 요소가 있을 수 있습니다. 각 확장에는 지원 되는 운영 체제를 포함 하 여 모든 필수 구성 요소를 설명 하는 문서가 있습니다.

## <a name="troubleshoot-extensions"></a>확장 문제 해결

각 확장에 대 한 문제 해결 정보는 확장에 대 한 개요의 **문제 해결 및 지원** 섹션에서 찾을 수 있습니다. 다음은 사용할 수 있는 문제 해결 정보 목록입니다.

| 네임스페이스 | 문제 해결 |
|-----------|-----------------|
| dependencyagent. dependencyagentlinux | [Linux에 대 한 Azure Monitor 종속성](agent-dependency-linux.md#troubleshoot-and-support) |
| dependencyagent. dependencyagentwindows | [Windows에 대 한 Azure Monitor 종속성](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft. azure. azurediskencryptionforlinux | [Linux에 대 한 Azure Disk Encryption](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft. azure. azurediskencryption | [Windows용 Azure Disk Encryption](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft. customscriptextension | [Windows 용 사용자 지정 스크립트](custom-script-windows.md#troubleshoot-and-support) |
| microsoft ostcextensions. customscriptforlinux | [Linux 용 필요한 상태 구성](dsc-linux.md#troubleshoot-and-support) |
| microsoft. powershell dsc | [Windows에 필요한 상태 구성](dsc-windows.md#troubleshoot-and-support) |
| microsoft. hpccompute | [Linux용 NVIDIA GPU 드라이버 확장](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft. hpccompute | [Windows 용 NVIDIA GPU 드라이버 확장](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| iaasantimalware. | [Windows 용 맬웨어 방지 확장](iaas-antimalware-windows.md#troubleshoot-and-support) |
| enterprisecloud를 모니터링 합니다. | [Linux에 대 한 Azure Monitor](oms-linux.md#troubleshoot-and-support)
| enterprisecloud. microsoftmonitoringagent | [Windows에 대 한 Azure Monitor](oms-windows.md#troubleshoot-and-support) |
| stackify. linuxagent. stackifylinuxagentextension | [Linux 용 Stackify 조사](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. ostcextensions | [Linux에 대 한 암호 다시 설정](vmaccess.md#troubleshoot-and-support) |
| vmsnapshot | [Linux에 대 한 스냅숏](vmsnapshot-linux.md#troubleshoot-and-support) |
| vmsnapshot | [Windows 용 스냅숏](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>다음 단계
* Linux 에이전트 및 확장의 작동 방식에 대 한 자세한 내용은 [linux 용 AZURE VM 확장 및 기능](features-linux.md)을 참조 하세요.
* Windows 게스트 에이전트 및 확장의 작동 방식에 대 한 자세한 내용은 [windows 용 AZURE VM 확장 및 기능](features-windows.md)을 참조 하세요.  
* Windows 게스트 에이전트를 설치 하려면 [Azure Windows 가상 머신 에이전트 개요](agent-windows.md)를 참조 하세요.  
* Linux 에이전트를 설치 하려면 [Azure Linux Virtual Machine 에이전트 개요](agent-linux.md)를 참조 하세요.  

