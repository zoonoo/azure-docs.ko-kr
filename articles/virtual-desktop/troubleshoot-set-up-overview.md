---
title: Windows 가상 데스크톱 문제 해결 개요-Azure
description: Windows 가상 데스크톱 환경을 설정 하는 동안 발생 하는 문제 해결에 대 한 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1010f89057d83a11fb2e156f01509e909270b044
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083575"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Windows 가상 데스크톱에 대 한 문제 해결 개요, 피드백 및 지원

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows Virtual Desktop 개체를 사용하여 2020년 봄 업데이트에 적용됩니다. Azure Resource Manager 개체 없이 Windows Virtual Desktop 2019년 가을 릴리스를 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)를 참조하세요.
>
> Windows Virtual Desktop 2020 봄 업데이트는 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Windows 가상 데스크톱 환경을 설정할 때 발생할 수 있는 문제에 대 한 개요를 제공 하 고 문제를 해결 하는 방법을 제공 합니다.

## <a name="report-issues-during-public-preview"></a>공개 미리 보기 중 문제 보고

스프링 2020 릴리스에 대 한 공개 미리 보기 중에 문제를 보고 하거나 기능을 제안 하려면 [Windows 가상 데스크톱 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문 하세요. 기술 커뮤니티를 사용 하 여 모범 사례를 논의 하거나 새로운 기능을 제안 하 고 투표를 할 수 있습니다. 공개 미리 보기 릴리스와 관련 된 문제를 보고 하는 경우 문제 유형에 **스프링 2020 업데이트 (미리 보기)** 로 레이블을 지정 해야 합니다.

도움을 요청 하거나 새로운 기능을 제안 하는 게시물을 작성 하는 경우 최대한 많은 세부 정보로 항목을 설명 해야 합니다. 자세한 정보는 다른 사용자가 질문에 답변 하거나 투표를 제안 하는 기능을 이해 하는 데 도움이 될 수 있습니다.

## <a name="escalation-tracks"></a>에스컬레이션 트랙

다른 작업을 수행 하기 전에 azure [상태 페이지](https://status.azure.com/status) 를 확인 하 고 [Azure Service Health](https://azure.microsoft.com/features/service-health/) 하 여 azure 서비스가 올바르게 실행 되 고 있는지 확인 합니다.

다음 표를 사용 하 여 원격 데스크톱 클라이언트를 사용 하 여 환경을 설정할 때 발생할 수 있는 문제를 식별 하 고 해결할 수 있습니다. 환경을 설정한 후에는 새로운 [진단 서비스](diagnostics-role-service.md) 를 사용 하 여 일반적인 시나리오에 대 한 문제를 식별할 수 있습니다.

| **문제점**                                                            | **제안 된 솔루션**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 세션 호스트 풀 Azure Virtual Network (VNET) 및 Express 경로 설정               | [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/)네트워킹 범주 아래에서 적절 한 서비스를 선택 합니다. |
| Windows 가상 데스크톱과 함께 제공 되는 Azure Resource Manager 템플릿을 사용 하지 않는 경우 세션 호스트 풀 VM (가상 머신) 만들기 | [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/)서비스의 **Windows 가상 데스크톱** 을 선택 합니다. <br> <br> Windows 가상 데스크톱과 함께 제공 되는 Azure Resource Manager 템플릿에 대 한 문제는 [호스트 풀 만들기](troubleshoot-set-up-issues.md)의 Azure Resource Manager 템플릿 오류 섹션을 참조 하세요. |
| Azure Portal에서 Windows 가상 데스크톱 세션 호스트 환경 관리    | [Azure 지원 요청을 엽니다](https://azure.microsoft.com/support/create-ticket/). <br> <br> 원격 데스크톱 서비스/Windows 가상 데스크톱 PowerShell을 사용 하는 경우 관리 문제에 대 한 자세한 내용은 [Windows 가상 데스크톱 powershell](troubleshoot-powershell.md) 또는 [Azure 지원 요청 열기](https://azure.microsoft.com/support/create-ticket/)를 참조 하 고, 서비스에 대 한 **windows 가상 데스크톱** 을 선택 하 고, 문제 유형에 대 한 **구성 및 관리** 를 선택한 다음, 문제 하위 형식에 대해 **PowerShell을 사용 하 여 환경 구성** |
| 호스트 풀 및 응용 프로그램 그룹에 연결 되는 Windows 가상 데스크톱 구성 관리 (앱 그룹)      | [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조 하거나, [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에 대 한 **windows 가상 데스크톱** 을 선택한 다음, 적절 한 문제 유형을 선택 합니다.|
| FSLogix 프로필 컨테이너 배포 및 관리 | [Fslogix 제품에 대 한 문제 해결 가이드](/fslogix/fslogix-trouble-shooting-ht/) 를 참조 하 고, 문제가 해결 되지 않으면 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에 대 한 **Windows 가상 데스크톱** 을 선택 하 고, 문제 유형으로 **fslogix** 를 선택한 다음, 적절 한 문제 하위 유형을 선택 합니다. |
| 시작 시 원격 데스크톱 클라이언트 오작동                                                 | [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md) 을 참조 하 고, 문제가 해결 되지 않으면 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에 대 한 **Windows 가상 데스크톱** 을 선택한 다음, 문제 유형으로 **원격 데스크톱 클라이언트** 를 선택 합니다.  <br> <br> 네트워크 문제인 경우 사용자는 네트워크 관리자에 게 문의 해야 합니다. |
| 연결 되었지만 피드 없음                                                                 | 사용자 연결을 사용 하 여 문제를 해결 합니다. 그러나 [Windows 가상 데스크톱 서비스 연결](troubleshoot-service-connection.md)의 경우 [아무 것도 표시 되지 않습니다 (피드 없음)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) . <br> <br> 사용자가 앱 그룹에 할당 된 경우 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에 대해 **Windows 가상 데스크톱** 을 선택한 다음, 문제 유형으로 **원격 데스크톱 클라이언트** 를 선택 합니다. |
| 네트워크로 인 한 피드 검색 문제                                            | 사용자가 네트워크 관리자에 게 문의 해야 합니다. |
| 클라이언트 연결                                                                    | [Windows 가상 데스크톱 서비스 연결](troubleshoot-service-connection.md) 을 참조 하 고, 문제가 해결 되지 않으면 [세션 호스트 가상 컴퓨터 구성](troubleshoot-vm-configuration.md)을 참조 하세요. |
| 원격 응용 프로그램 또는 데스크톱의 응답성                                      | 문제가 특정 응용 프로그램 또는 제품에 연결 된 경우 해당 제품을 담당 하는 팀에 문의 하십시오. |
| 라이선스 메시지 또는 오류                                                          | 문제가 특정 응용 프로그램 또는 제품에 연결 된 경우 해당 제품을 담당 하는 팀에 문의 하십시오. |
| 타사 인증 방법에 대 한 문제 | 타사 공급자가 Windows 가상 데스크톱 시나리오를 지원 하는지 확인 하 고 알려진 문제와 관련 된 방법을 확인 합니다. |
| Windows 가상 데스크톱에 대 한 Log Analytics 사용 문제 | 진단 스키마 문제에 대 한 자세한 내용은 [Azure 지원 요청을 여세요](https://azure.microsoft.com/support/create-ticket/).<br><br>Log Analytics의 쿼리, 시각화 또는 기타 문제의 경우 Log Analytics에서 적절 한 문제 유형을 선택 합니다. |
| M365 apps를 사용 하는 문제 | [M365 관리 센터 도움말 옵션](/microsoft-365/admin/contact-support-for-business-products/)중 하나를 사용 하 여 M365 관리 센터에 문의 하십시오. |

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 환경에서 호스트 풀을 만드는 동안 발생 하는 문제를 해결 하려면 [호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows Virtual Desktop에서 VM(가상 머신)을 구성하면서 생기는 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결 하려면 [Windows 가상 데스크톱 서비스 연결](troubleshoot-service-connection.md)을 참조 하세요.
- 원격 데스크톱 클라이언트와 관련 된 문제를 해결 하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md) 을 참조 하세요.
- Windows Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 서비스에 대 한 자세한 내용은 [Windows 가상 데스크톱 환경](environment-setup.md)을 참조 하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 하는 동안 오류를 확인 하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조 하세요.
