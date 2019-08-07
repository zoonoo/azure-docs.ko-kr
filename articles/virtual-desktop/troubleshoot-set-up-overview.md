---
title: Windows 가상 데스크톱 문제 해결 개요, 사용자 의견 및 지원-Azure
description: Windows 가상 데스크톱 테 넌 트 환경을 설정 하는 동안 발생 하는 문제 해결에 대 한 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: e652c8a67db686159a2c3fd48e2ea65888bcc10d
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816356"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>문제 해결 개요, 피드백 및 지원

이 문서에서는 Windows 가상 데스크톱 테 넌 트 환경을 설정할 때 발생할 수 있는 문제에 대 한 개요를 제공 하 고 문제를 해결 하는 방법을 제공 합니다.

## <a name="provide-feedback"></a>피드백 제공

Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다. [Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="escalation-tracks"></a>에스컬레이션 트랙

다음 표를 사용 하 여 원격 데스크톱 클라이언트를 사용 하 여 테 넌 트 환경을 설정할 때 발생할 수 있는 문제를 식별 하 고 해결할 수 있습니다. 테 넌 트가 설정 되 면 새로운 [진단 서비스](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) 를 사용 하 여 일반적인 시나리오에 대 한 문제를 식별할 수 있습니다.

>[!NOTE]
>Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다. Windows 가상 데스크톱 지원을 참조할 때마다 지금은 기술 커뮤니티 포럼으로 이동 하세요. [Windows 가상 데스크톱 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 를 방문 하 여 제품 팀 및 활성 커뮤니티 구성원과 관련 된 문제를 논의 합니다. 지원 문제를 해결 해야 하는 경우 문제가 발생 했을 때의 활동 ID와 대략적인 시간 프레임을 포함 합니다.

| **문제점**                                                            | **제안 된 솔루션**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 테 넌 트 만들기                                                    | Azure 중단이 있는 경우 [Azure 지원](https://azure.microsoft.com/support/options/)에 문의 하세요. 그렇지 않으면 **원격 데스크톱 서비스/Windows 가상 데스크톱 지원**담당자에 게 문의 하세요.|
| Azure Portal에서 Marketplace 템플릿 액세스       | Azure 중단이 있는 경우 [Azure 지원](https://azure.microsoft.com/support/options/)에 문의 하세요. <br> <br> Azure Marketplace Windows 가상 데스크톱 템플릿을 무료로 사용할 수 있습니다.|
| GitHub에서 Azure Resource Manager 템플릿 액세스                                  | [테 넌 트 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)의 "Windows 가상 데스크톱 세션 호스트 vm 만들기" 섹션을 참조 하세요. 그래도 문제가 해결 되지 않으면 [GitHub 지원 팀](https://github.com/contact)에 문의 하세요. <br> <br> GitHub에서 템플릿에 액세스 한 후에 오류가 발생 하는 경우 [Azure 지원](https://azure.microsoft.com/support/options/)에 문의 하세요.|
| 세션 호스트 풀 Azure Virtual Network (VNET) 및 Express 경로 설정               | **Azure 지원 (네트워킹)** 에 문의 하세요. |
| Windows 가상 데스크톱과 함께 제공 되는 Azure Resource Manager 템플릿을 사용 하지 않는 경우 세션 호스트 풀 VM (가상 머신) 만들기 | **Azure 지원 (Compute)** 에 문의 하세요. <br> <br> Windows 가상 데스크톱과 함께 제공 되는 Azure Resource Manager 템플릿에 대 한 문제는 [테 넌 트 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)의 Windows 가상 데스크톱 테 넌 트 만들기 섹션을 참조 하세요. |
| Azure Portal에서 Windows 가상 데스크톱 세션 호스트 환경 관리    | **Azure 지원**에 문의 하세요. <br> <br> 원격 데스크톱 서비스/Windows 가상 데스크톱 PowerShell을 사용 하는 경우의 관리 문제에 대 한 자세한 내용은 [Windows 가상 데스크톱 powershell](troubleshoot-powershell.md) 또는 **원격 데스크톱 서비스/windows 가상 데스크톱 지원 팀**에 문의 하세요. |
| 호스트 풀 및 응용 프로그램 그룹에 연결 되는 Windows 가상 데스크톱 구성 관리 (앱 그룹)      | [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조 하거나 **원격 데스크톱 서비스/Windows 가상 데스크톱 지원 팀**에 문의 하세요. <br> <br> 문제가 샘플 GUI (그래픽 사용자 인터페이스)에 연결 된 경우 Yammer 커뮤니티에 연락 하세요.|
| 시작 시 원격 데스크톱 클라이언트 오작동                                                 | [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md) 을 참조 하 고, 문제가 해결 되지 않으면 **원격 데스크톱 서비스/Windows 가상 데스크톱 지원 팀**에 문의 하세요.  <br> <br> 네트워크 문제인 경우 사용자는 네트워크 관리자에 게 문의 해야 합니다. |
| 연결 되었지만 피드 없음                                                                 | [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md)의 "사용자 연결을 제외한 아무 것도 표시 되지 않습니다 (피드 없음)" 섹션을 사용 하 여 문제를 해결 합니다. <br> <br> 사용자가 앱 그룹에 할당 된 경우 **원격 데스크톱 서비스/Windows 가상 데스크톱 지원 팀**으로 에스컬레이션 합니다. |
| 네트워크로 인 한 피드 검색 문제                                            | 사용자가 네트워크 관리자에 게 문의 해야 합니다. |
| 클라이언트 연결                                                                    | [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md) 을 참조 하 고, 문제가 해결 되지 않으면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조 하세요. |
| 원격 응용 프로그램 또는 데스크톱의 응답성                                      | 문제가 특정 응용 프로그램 또는 제품에 연결 된 경우 해당 제품을 담당 하는 팀에 문의 하십시오. |
| 라이선스 메시지 또는 오류                                                          | 문제가 특정 응용 프로그램 또는 제품에 연결 된 경우 해당 제품을 담당 하는 팀에 문의 하십시오. |

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 환경에서 테 넌 트 및 호스트 풀을 만드는 동안 발생 하는 문제를 해결 하려면 [테 넌 트 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows 가상 데스크톱에서 VM (가상 컴퓨터)을 구성 하는 동안 발생 하는 문제를 해결 하려면 [세션 호스트 가상 컴퓨터 구성](troubleshoot-vm-configuration.md)을 참조 하세요.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결 하려면 [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md)을 참조 하세요.
- Windows 가상 데스크톱과 함께 PowerShell을 사용할 때 발생 하는 문제를 해결 하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조 하세요.
- 미리 보기 서비스에 대 한 자세한 내용은 [Windows Desktop Preview environment](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)를 참조 하세요.
- 문제 해결 자습서를 진행하려면 [자습서: 리소스 관리자 템플릿 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)문제를 해결 합니다.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)를 참조하세요.