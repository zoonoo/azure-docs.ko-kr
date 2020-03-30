---
title: 윈도우 가상 데스크톱 문제 해결 개요 - Azure
description: Windows 가상 데스크톱 테넌트 환경을 설정하는 동안 문제 해결에 대한 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127409"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>문제 해결 개요, 피드백 및 지원

이 문서에서는 Windows 가상 데스크톱 테넌트 환경을 설정할 때 발생할 수 있는 문제에 대한 개요를 제공하고 문제를 해결하는 방법을 제공합니다.

## <a name="provide-feedback"></a>피드백 제공

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="escalation-tracks"></a>에스컬레이션 트랙

다음 표를 사용하여 원격 데스크톱 클라이언트를 사용하여 테넌트 환경을 설정할 때 발생할 수 있는 문제를 식별하고 해결합니다. 테넌트가 설정되면 새 [진단 서비스를](diagnostics-role-service.md) 사용하여 일반적인 시나리오에 대한 문제를 식별할 수 있습니다.

>[!NOTE]
> 우리는 당신이 제품 팀 및 활성 지역 사회 구성원과 문제를 논의하기 위해 방문 할 수있는 기술 커뮤니티 포럼이 있습니다. Windows [가상 데스크톱 기술 커뮤니티를](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 방문하여 토론을 시작합니다.

| **문제**                                                            | **제안된 솔루션**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Windows 가상 데스크톱 테넌트 만들기                                                    | Azure 가동 중단이 있는 경우 [Azure 지원 요청을 엽니다.](https://azure.microsoft.com/support/create-ticket/) 그렇지 않으면 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/)서비스에 대한 Windows 가상 **데스크톱을** 선택하고 문제 유형에 대한 **배포를** 선택한 다음 문제 하위 유형에 대한 **Windows 가상 데스크톱 테넌트를 만드는 문제를** 선택합니다.|
| Azure 포털에서 마켓플레이스 템플릿 액세스       | Azure 가동 중단이 있는 경우 [Azure 지원 요청을 엽니다.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Azure 마켓플레이스 Windows 가상 데스크톱 템플릿을 자유롭게 사용할 수 있습니다.|
| GitHub에서 Azure 리소스 관리자 템플릿 액세스                                  | [테넌트 및 호스트 풀 생성의](troubleshoot-set-up-issues.md) [Windows 가상 데스크톱 세션 호스트 VM](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) 만들기 섹션을 참조하십시오. 그래도 문제가 해결되지 않으면 [GitHub 지원 팀에](https://github.com/contact)문의하십시오. <br> <br> GitHub에서 템플릿에 액세스한 후 오류가 발생하면 [Azure 지원에](https://azure.microsoft.com/support/create-ticket/)문의하십시오.|
| 세션 호스트 풀 Azure 가상 네트워크(VNET) 및 익스프레스 경로 설정               | [Azure 지원 요청을 연](https://azure.microsoft.com/support/create-ticket/)다음 네트워킹 범주에서 적절한 서비스를 선택합니다. |
| Windows 가상 데스크톱과 함께 제공되는 Azure 리소스 관리자 템플릿이 사용되지 않는 경우 세션 호스트 풀 VM(VM) 생성 | [Azure 지원 요청을 연](https://azure.microsoft.com/support/create-ticket/)다음 서비스에 대 **한 Windows를 실행 하는 가상 컴퓨터를** 선택 합니다. <br> <br> Windows 가상 데스크톱과 함께 제공되는 Azure 리소스 관리자 템플릿에 대한 문제에 대한 자세한 내용은 [테넌트의](troubleshoot-set-up-issues.md)Windows 가상 데스크톱 테넌트 만들기 섹션 및 호스트 풀 만들기 를 참조하십시오. |
| Azure 포털에서 Windows 가상 데스크톱 세션 호스트 환경 관리    | [Azure 지원 요청을 엽니다.](https://azure.microsoft.com/support/create-ticket/) <br> <br> 원격 데스크톱 서비스/Windows 가상 데스크톱 PowerShell을 사용할 때 관리 문제의 경우 [Windows 가상 데스크톱 PowerShell을](troubleshoot-powershell.md) 참조하거나 [Azure 지원 요청을 열고,](https://azure.microsoft.com/support/create-ticket/)서비스에 대한 **Windows 가상 데스크톱을** 선택하고, 문제 유형에 대한 **구성 및 관리를** 선택한 다음 문제 하위 유형에 **대해 PowerShell을 사용하여 테넌트를 구성하는 문제를** 선택합니다. |
| 호스트 풀 및 응용 프로그램 그룹(앱 그룹)에 연결된 Windows 가상 데스크톱 구성 관리      | [Windows 가상 데스크톱 PowerShell을](troubleshoot-powershell.md)참조하거나 Azure 지원 요청을 [열고](https://azure.microsoft.com/support/create-ticket/)서비스에 대한 Windows **가상 데스크톱을** 선택한 다음 적절한 문제 유형을 선택합니다.|
| FSLogix 프로필 컨테이너 배포 및 관리 | [FSLogix 제품에 대한 문제 해결 가이드를](/fslogix/fslogix-trouble-shooting-ht/) 참조하고 문제가 해결되지 않으면 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/)서비스에 대한 Windows 가상 **데스크톱을** 선택하고 문제 유형에 대해 **FSLogix를** 선택한 다음 적절한 문제 하위 유형을 선택합니다. |
| 원격 데스크톱 클라이언트 시작 시 오작동                                                 | [원격 데스크톱 클라이언트 문제 해결을](troubleshoot-client.md) 참조 하 고 문제가 해결 되지 않는 경우 Azure 지원 요청을 [열고](https://azure.microsoft.com/support/create-ticket/)서비스에 대 한 Windows **가상 데스크톱을** 선택 한 다음 문제 유형에 대 한 원격 데스크톱 **클라이언트를** 선택 합니다.  <br> <br> 네트워크 문제인 경우 사용자는 네트워크 관리자에게 문의해야 합니다. |
| 연결되었지만 피드가 없음                                                                 | [사용자가 연결하지만](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) [Windows 가상 데스크톱 서비스 연결의](troubleshoot-service-connection.md)피드 없음 섹션을 사용하여 문제를 해결합니다. <br> <br> 사용자가 앱 그룹에 할당된 경우 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/)서비스에 대한 **Windows 가상 데스크톱을** 선택한 다음 문제 유형에 대한 **원격 데스크톱 클라이언트를** 선택합니다. |
| 네트워크로 인한 피드 검색 문제                                            | 사용자는 네트워크 관리자에게 문의해야 합니다. |
| 클라이언트 연결                                                                    | [Windows 가상 데스크톱 서비스 연결을](troubleshoot-service-connection.md) 참조하고 문제가 해결되지 않으면 세션 호스트 가상 시스템 [구성을](troubleshoot-vm-configuration.md)참조하십시오. |
| 원격 응용 프로그램 또는 데스크톱의 응답성                                      | 문제가 특정 응용 프로그램 이나 제품에 연결 된 경우 해당 제품에 대 한 책임 팀에 문의 하십시오. |
| 메시지 또는 오류 에대한 라이선스                                                          | 문제가 특정 응용 프로그램 이나 제품에 연결 된 경우 해당 제품에 대 한 책임 팀에 문의 하십시오. |
| GitHub에서 Windows 가상 데스크톱 도구를 사용할 때 의 문제 (Azure 리소스 관리자 템플릿, 진단 도구, 관리 도구) | 문제를 보고하는 [원격 데스크톱 서비스에 대한 Azure 리소스 관리자 템플릿을](https://github.com/Azure/RDS-Templates/blob/master/README.md) 참조하십시오. |

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 환경에서 테넌트 및 호스트 풀을 만드는 동안 문제를 해결하려면 [테넌트 및 호스트 풀 만들기를](troubleshoot-set-up-issues.md)참조하십시오.
- Windows 가상 데스크톱에서 가상 시스템(VM)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 시스템 구성을](troubleshoot-vm-configuration.md)참조하십시오.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결하려면 [Windows 가상 데스크톱 서비스 연결을](troubleshoot-service-connection.md)참조하십시오.
- 원격 데스크톱 클라이언트의 문제 해결을 위해 [원격 데스크톱 클라이언트 문제 해결을](troubleshoot-client.md) 참조하세요.
- Windows 가상 데스크톱에서 PowerShell을 사용할 때 문제를 해결하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조하십시오.
- 서비스에 대한 자세한 내용은 [Windows 가상 데스크톱 환경을](environment-setup.md)참조하십시오.
- 문제 해결 자습서를 진행하려면 [자습서: 리소스 관리자 템플릿 배포 문제를 해결 합니다.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 하는 동안 오류를 확인 하는 작업에 대 한 자세한 내용은 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조 합니다.
