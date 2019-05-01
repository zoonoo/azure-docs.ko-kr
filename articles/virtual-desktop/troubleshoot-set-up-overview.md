---
title: Windows 가상 데스크톱 개요, 피드백 및 지원-Azure 문제 해결
description: Windows 가상 데스크톱 테 넌 트 환경을 설정 하는 동안 문제를 해결 하는 것에 대 한 개요입니다.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 8e344d6908ba19f8e2294c7777b9c1016eafaf52
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927644"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>문제 해결 개요, 피드백 및 지원

이 문서에서는 Windows 가상 데스크톱 테 넌 트 환경을 설정 하는 경우 발생할 수 있습니다 하 고 문제를 해결 하는 방법을 제공 하는 문제에 설명 합니다.

## <a name="provide-feedback"></a>피드백 제공

Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다. [Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="escalation-tracks"></a>에스컬레이션 추적

다음 표를 사용 하 여 원격 데스크톱 클라이언트를 사용 하 여 테 넌 트 환경을 설정 하는 경우 발생할 수 있습니다 문제 식별 및 해결 합니다.

>[!NOTE]
>Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다. Windows 가상 데스크톱 지원, 참조 될 때마다 현재 기술 커뮤니티 포럼으로 이동 합니다. 방문 합니다 [Windows 가상 데스크톱 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 제품 팀과 활발 한 커뮤니티 멤버를 사용 하 여 문제를 설명 합니다. 지원 문제를 해결 해야 할 경우 작업 ID와 포함 하기 위한 대략적인 시간 프레임 문제가 발생 한 경우.

| **문제**                                                            | **제안 된 솔루션**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 테 넌 트 만들기                                                    | Azure는 가동 중단의 경우에 문의 [Azure 지원](https://azure.microsoft.com/support/options/)연락이 고, 그렇지 않으면 **원격 데스크톱 서비스/Windows 가상 데스크톱 지원**합니다.|
| Azure portal에서 액세스 Marketplace 템플릿       | Azure는 가동 중단의 경우에 문의 [Azure 지원](https://azure.microsoft.com/support/options/)합니다. <br> <br> Azure Marketplace의 Windows 가상 데스크톱 템플릿 자유롭게 사용할 수 있습니다.|
| GitHub에서 Azure Resource Manager 템플릿 액세스                                  | "Creating Windows 가상 데스크톱 세션 호스트 Vm" 섹션을 참조 하세요 [테 넌 트 및 호스트 풀 생성](troubleshoot-set-up-issues.md)합니다. 문제가 여전히 확인할 수 없는 경우에 문의 합니다 [GitHub 지원 팀](https://github.com/contact)합니다. <br> <br> 오류가 발생 하는 GitHub의 템플릿과 액세스 한 후에 문의 [Azure 지원](https://azure.microsoft.com/support/options/)합니다.|
| 세션 호스트 풀 Azure Virtual Network (VNET) 및 Express 경로 설정               | 연락처 **Azure 지원 (네트워킹)** 합니다. |
| 세션 호스트 풀 Windows 가상 데스크톱을 사용 하 여 제공 되는 Azure Resource Manager 템플릿을 사용 하지 않는 경우 가상 머신 (VM) 만들기 | 연락처 **Azure 지원 (계산)** 합니다. <br> <br> Windows 가상 데스크톱 만들기 테 넌 트 섹션을 참조 하는 Windows 가상 데스크톱을 사용 하 여 제공 되는 Azure Resource Manager 템플릿 사용 하 여 문제에 대 한 [테 넌 트 및 호스트 풀 생성](troubleshoot-set-up-issues.md)합니다. |
| Azure portal에서 Windows 가상 데스크톱 세션 호스트 환경 관리    | 연락처 **Azure 지원**합니다. <br> <br> 원격 데스크톱 서비스/Windows 가상 데스크톱 PowerShell을 사용 하는 경우 문제를 관리, 참조 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md) 에 문의 하거나는 **원격 데스크톱 서비스/Windows 가상 데스크톱 지원 팀** . |
| 호스트 풀과 응용 프로그램 그룹 (앱 그룹)에 연결 된 Windows 가상 데스크톱 구성 관리      | 참조 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)에 게 문의 하거나 합니다 **원격 데스크톱 서비스/Windows 가상 데스크톱 지원 팀**합니다. <br> <br> 문제는 샘플 그래픽 사용자 인터페이스 (GUI)와 연결 하는 경우 Yammer 커뮤니티에 문의 합니다.|
| 시작 시 원격 데스크톱 클라이언트 오류                                                 | 참조 [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md) 문제를 해결 하지 않습니다 하는 경우에 문의 **원격 데스크톱 서비스/Windows 가상 데스크톱 지원팀**합니다.  <br> <br> 네트워크 문제로 인 경우 사용자는 해당 네트워크 관리자에 게 문의 해야 합니다. |
| 연결 되었지만 피드가 없습니다.                                                                 | 사용 하 여 문제를 해결 합니다 "사용자 연결 하지만 아무 것도 표시 (피드가 없습니다)" 섹션 [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md)합니다. <br> <br> 앱 그룹에 사용자를 할당 하는 경우를 에스컬레이션 합니다 **원격 데스크톱 서비스/Windows 가상 데스크톱 지원 팀**합니다. |
| 네트워크로 인해 피드 검색 문제                                            | 사용자는 해당 네트워크 관리자에 게 문의 해야 합니다. |
| 클라이언트 연결                                                                    | 참조 [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md) 문제를 해결 하지 않습니다 하는 경우 [세션 호스트 가상 머신 구성을](troubleshoot-vm-configuration.md)합니다. |
| 원격 응용 프로그램 또는 데스크톱의 응답성                                      | 문제는 특정 응용 프로그램 또는 제품에 연결 되어 해당 제품 담당 팀에 문의 합니다. |
| 라이선스 메시지 또는 오류                                                          | 문제는 특정 응용 프로그램 또는 제품에 연결 되어 해당 제품 담당 팀에 문의 합니다. |

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 환경에서 테 넌 트 및 호스트 풀을 만드는 동안 문제를 해결 하려면 참조 [테 넌 트 및 호스트 풀 생성](troubleshoot-set-up-issues.md)합니다.
- Windows 가상 데스크톱의 가상 머신 (VM)를 구성 하는 동안 문제를 해결 하려면 참조 [세션 호스트 가상 머신 구성을](troubleshoot-vm-configuration.md)합니다.
- Windows 가상 데스크톱 클라이언트 연결을 사용 하 여 문제를 해결 하려면 참조 [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md)합니다.
- Windows 가상 데스크톱을 사용 하 여 PowerShell을 사용 하는 경우 문제를 해결 하려면 참조 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)합니다.
- 미리 보기 서비스에 대 한 자세한 내용은 참조 하세요 [Windows 데스크톱 미리 보기 환경](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)합니다.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)합니다.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)를 참조하세요.