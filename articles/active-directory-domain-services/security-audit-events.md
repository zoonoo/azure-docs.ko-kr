---
title: Azure AD Domain Services에 대 한 보안 감사를 사용 하도록 설정 | Microsoft Docs
description: Azure AD Domain Services에 대 한 보안 감사를 사용 하도록 설정
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566496"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Azure AD Domain Services (미리 보기)에 대 한 보안 감사를 사용 하도록 설정
Azure AD 도메인 서비스 보안 감사 스트림 보안 감사 이벤트 대상된 리소스를 Azure AD 도메인 서비스 포털을 사용 하 여 연결할 수 있습니다. 이러한 이벤트를 받을 수 있는 리소스는 Azure Storage, Azure Log Analytics 작업 영역 또는 Azure 이벤트 허브에 포함 됩니다. 보안 감사 이벤트를 사용 하도록 설정한 직후 Azure AD 도메인 서비스는 대상된 리소스에 선택한 범주에 대 한 모든 감사 된 이벤트를 보냅니다. Azure storage로 고객 보관 감사 이벤트를 사용 하도록 설정 하는 보안 감사 이벤트. 또한 고객에 게 보안 정보 및 이벤트 관리 (SIEM) 소프트웨어 (또는 이와 동등한) event hubs를 사용 하 여 이벤트 스트림 하거나 자신의 분석과 insights Azure portal에서 Azure Log Analytics를 사용 하 여 수행할 수 있습니다. 

> [!IMPORTANT]
> Azure AD Domain Services 보안 감사는 Azure AD Domain Services에 대 한 Azure Resource Manager 기반 인스턴스 에서만 사용할 수 있습니다.
>
>

## <a name="auditing-event-categories"></a>감사 이벤트 범주
Azure AD Domain Services 보안 감사 Active Directory Domain Services 도메인 컨트롤러에 대해 제공 되는 기존 감사 맞춥니다. 기존 감사 패턴을 다시 사용 하면 이벤트를 분석 하는 경우에 동일한 논리를 사용할 수 있습니다. Azure AD Domain Services 보안 감사 이벤트 범주가 포함 되어 있습니다.

| 감사 범주 이름 | 설명 |
|:---|:---|
| 계정 로그온|계정 또는 데이터를 도메인 컨트롤러에는 로컬 보안 계정 관리자 (SAM) 인증 시도 감사 합니다.</p>로그온 및 로그 오프 정책 설정 및 이벤트는 특정 컴퓨터에 액세스 하려는 시도 추적 합니다. 설정 및 이벤트가이 범주에서 사용 되는 계정 데이터베이스에 집중 합니다. 이 범주에는 같은 하위 범주가 포함 됩니다.<ul><li>[감사 자격 증명 유효성 검사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos 인증 서비스 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Kerberos 서비스 티켓 작업 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[다른 로그온/로그 오프 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| 계정 관리|감사는 사용자 및 컴퓨터 계정 및 그룹을 변경 합니다. 이 범주에는 같은 하위 범주가 포함 됩니다.<ul><li>[응용 프로그램 그룹 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[컴퓨터 계정 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[메일 그룹 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[다른 계정 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[보안 그룹 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[사용자 계정 관리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 추적 세부 정보|개별 응용 프로그램 및 사용자의 컴퓨터는 사용 되는 방식을 이해 하 고 해당 컴퓨터에서 작업을 감사 합니다. 이 범주에는 같은 하위 범주가 포함 됩니다.<ul><li>[DPAPI 활동 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP 활동 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[프로세스 만들기 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[프로세스 종료 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| 디렉터리 서비스 액세스|감사 액세스 하 고 Active Directory Domain Services (AD DS)의 개체를 수정 하려고 시도 합니다. 이러한 감사 이벤트가 도메인 컨트롤러에만 기록 됩니다. 이 범주에는 같은 하위 범주가 포함 됩니다.<ul><li>[세부 디렉터리 서비스 복제 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[디렉터리 서비스 액세스 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[디렉터리 서비스 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[디렉터리 서비스 복제 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| 로그온-로그 오프|대화형으로 또는 네트워크를 통해 컴퓨터에 로그온 시도 감사 합니다. 이러한 이벤트는 사용자 활동을 추적 하 고 네트워크 리소스에 대 한 잠재적인 공격을 식별 하는 데 유용 합니다. 이 범주에는 같은 하위 범주가 포함 됩니다.<ul><li>[계정 잠금 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[감사 사용자/장치 클레임](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec 확장 모드 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[감사 그룹 멤버 자격](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec 주 모드 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec 빠른 모드 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[감사 로그 오프](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[로그온 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[네트워크 정책 서버 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[다른 로그온/로그 오프 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[특수 로그온 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|개체 액세스| 특정 개체나 네트워크 또는 컴퓨터에 있는 개체의 형식에 액세스 하려는 시도 감사 합니다. 이 범주에는 같은 하위 범주가 포함 됩니다.<ul><li>[응용 프로그램 생성 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[인증 서비스 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[세부 파일 공유 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[파일 공유 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[파일 시스템 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[필터링 플랫폼 연결 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[감사 필터링 플랫폼 패킷 삭제](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[핸들 조작 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[커널 개체 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[다른 개체 액세스 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[레지스트리 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[이동식 저장소 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[중앙 액세스 정책 준비 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|정책 변경|감사는 로컬 시스템 또는 네트워크에서 중요 한 보안 정책을 변경 합니다. 정책은 관리자가 보안 네트워크 리소스를 일반적으로 설정 됩니다. 변경 하거나 이러한 정책을 변경 하려는 시도 모니터링 합니다. 네트워크에 대 한 보안 관리의 중요 한 측면을 수 있습니다. 이 범주에는 같은 하위 범주가 포함 됩니다.<ul><li>[감사 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[인증 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[권한 부여 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[필터링 플랫폼 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC 규칙 수준 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[기타 정책 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|권한 사용| 하나 이상의 시스템에 특정 권한이의 사용을 감사 합니다. 이 범주에는 같은 하위 범주가 포함 됩니다.<ul><li>[중요 하지 않은 권한 사용 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[중요 한 권한 사용 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[기타 권한 사용 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|시스템| 감사 시스템 수준 변경 하 고 다른 범주에 포함 되지 않습니다 컴퓨터에 잠재적인 보안 영향을 줄. 이 범주에는 같은 하위 범주가 포함 됩니다.<ul><li>[IPsec 드라이버 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[기타 시스템 이벤트 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[보안 상태 변경 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[보안 시스템 확장 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[시스템 무결성 감사](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>범주별 이벤트 Id
 Azure AD Domain Services 보안 감사 특정 작업에는 감사 가능한 이벤트를 트리거할 때 다음 이벤트 Id를 기록 합니다.

| 이벤트 카테고리 이름 | 이벤트 ID |
|:---|:---|
|계정 로그온 보안|4767, 4774, 4775, 4776, 4777|
|계정 관리 보안|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|세부 정보 추적 보안|없음|
|DS Access 보안|5136, 5137, 5138, 5139, 5141|
|로그온-로그 오프 보안|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|개체 액세스 보안|없음|
|정책 변경 보안|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|보안 사용 권한|4985|
|시스템 보안|4612, 4621|

## <a name="enable-security-audit-events"></a>보안 감사 이벤트를 사용 하도록 설정
다음 지침을 사용 하면 성공적으로 Azure AD 도메인 서비스 보안 감사 이벤트를 구독할 수 있습니다.

> [!IMPORTANT]
> Azure AD Domain Services 보안 감사 소급 되지 않습니다. 과거 로부터의 완벽 한 이벤트를 재생 하거나 과거 로부터의 완벽 한 이벤트를 검색할 수 없는 합니다. 서비스만 설정 되 면 발생 하는 이벤트를 보낼 수 있습니다.
>

### <a name="choose-the-target-resource"></a>대상 리소스를 선택 합니다.
에 보안 감사를 위해 Azure Storage, Azure Event Hubs 또는 Azure Log Analytics 작업 영역 조합을 대상 리소스로 사용할 수 있습니다. 사용 사례에 대 한 최상의 리소스에 대 한 다음 표에서를 것이 좋습니다.

> [!IMPORTANT]
> Azure AD Domain Services 보안 감사를 활성화 하기 전에 대상 리소스를 생성 해야 합니다.
>

| 대상 리소스 | 시나리오 |
|:---|:---|
|Azure Storage|기본 요구 사항에 보관을 위해 보안 감사 이벤트를 저장 하는 경우이 대상을 사용 하는 것이 좋습니다. 다른 대상 보관 목적으로 사용할 수 있습니다. 그러나 해당 대상을 초과 보관의 기본 기능을 제공합니다. Azure Storage 계정을 만들려면 다음과 [저장소 계정을 만듭니다.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Azure Event Hubs|기본 요구 사항에 데이터 분석 소프트웨어 또는 보안 정보 및 이벤트 관리 (SIEM) 소프트웨어 등의 추가 소프트웨어를 사용 하 여 보안 감사 이벤트를 공유 하는 경우이 대상을 사용 하는 것이 좋습니다. 이벤트 허브를 만들려면 따라 [빠른 시작: Azure portal을 사용 하 여 이벤트 허브를 만듭니다.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure Log Analytics 작업 영역|기본 요구 사항을 분석 하 고 Azure portal에서 보안 감사를 직접 검토 되 면이 대상을 사용 하는 것이 좋습니다.  Log Analytics 작업 영역을 만들려면 다음과 [Azure portal에서 Log Analytics 작업 영역을 만듭니다.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Azure portal을 사용 하 여 보안 이벤트 감사를 사용 하도록 설정 하려면 
1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.  Azure portal에서 모든 서비스를 클릭 합니다. 리소스 목록에서 입력 **도메인**합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. 클릭 **Azure AD Domain Services**합니다.
2. 목록에서 Azure AD Domain Services 인스턴스를 클릭 합니다.
3. 클릭 **진단 설정 (미리 보기)** 왼쪽에 대 한 작업 목록에서.</p>
![진단 설정 작업](./media/security-audit-events/diagnostic-settings-action.png)
4. 진단 구성의 이름을 입력 합니다 (**aadds 감사** 예로).</p>
![진단 설정 페이지](./media/security-audit-events/diagnostic-settings-page.png)
5. 보안 감사 이벤트를 사용 하 여 사용 하 여 대상된 리소스 옆에 있는 적절 한 확인란을 선택 합니다.
    > [!NOTE]
    > 이 페이지에서 대상 리소스를 만들 수 없습니다.
    >
    
    **azure storage:**</p>
    선택 **저장소 계정에 보관**합니다. 클릭 **구성**합니다. 선택 합니다 **구독** 하며 **저장소 계정** 보안 감사 이벤트를 보관 하려면 사용 하려는. **확인**을 클릭합니다.</p>
    
    ![진단 저장소 설정](./media/security-audit-events/diag-settings-storage.png)
    
    **Azure event hubs의 경우:**</p>
    선택 **이벤트 허브로 Stream**합니다. 클릭 **구성**합니다. 에 **선택 이벤트 허브 페이지**를 선택 합니다 **구독** 이벤트 허브를 만드는 데. 다음을 선택 합니다 **이벤트 허브 네임 스페이스**, **이벤트 허브 이름**, 및 **이벤트 허브 정책 이름**합니다. **확인**을 클릭합니다.</p>
    ![진단 이벤트 허브 설정](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Azure 로그 분석 작업 영역:**</p>
    **Log Analytics에 보내기**를 선택합니다. 선택 된 **구독** 하 고 **Log Analytics 작업 영역** 보안 감사 이벤트를 저장 하는 데 사용 합니다.</p>
    ![진단 작업 영역 설정](./media/security-audit-events/diag-settings-log-analytics.png)

6. 특정 대상 리소스에 포함 하려는 로그 범주를 선택 합니다. 저장소 계정을 사용 하는 경우에 보존 정책을 구성할 수 있습니다.

    > [!NOTE]
    > 단일 구성 내에서 각 대상된 리소스에 대 한 다양 한 로그 범주를 선택할 수 있습니다. 이 사용 하는 로그를 보관 하려는 사용자는 로그 범주 및 Log Analytics에 대 한 유지 하려는 범주를 선택할 수 있습니다.
    >

7. 클릭 **저장할** 변경 내용을 커밋 하도록 합니다. 대상 리소스에 구성을 저장 한 후에 곧 Azure AD Domain Services 보안 감사 이벤트를 받게 됩니다.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Azure PowerShell을 사용 하 여 보안 이벤트 감사를 사용 하도록 설정 하려면
 
### <a name="prerequisites"></a>필수 조건

이 문서의 지침에 따라 [Azure PowerShell 모듈을 설치하고 Azure 구독에 연결](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)합니다.

### <a name="enable-security-audits"></a>보안 감사를 사용 하도록 설정

1. 해당 테 넌 트 및 사용 하 여 구독에 대 한 Azure Resource Manager로 인증 합니다 **Connect AzAccount** Azure PowerShell cmdlet.
2. 보안에 대 한 대상 리소스를 감사 이벤트를 만듭니다.</p>
    **azure storage:**</p>
    따릅니다 [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) 저장소 계정을 만듭니다.</p>
    **Azure event hubs의 경우:**</p>
    에 따라 [빠른 시작: Azure PowerShell을 사용 하 여 이벤트 허브 만들기](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) 이벤트 허브를 만들려고 합니다. 사용 해야 합니다 [새로 만들기-AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) Azure PowerShell cmdlet을 통해 이벤트 허브에 대 한 Active Directory AD Domain Services 사용 권한을 허용 하는 권한 부여 규칙을 만들려면 **네임 스페이스**. 권한 부여 규칙을 포함 해야 합니다는 **관리**를 **수신**, 및 **송신** 권한.
    > [!IMPORTANT]
    > 이벤트 허브 네임 스페이스 및 event hub 아닌 권한 부여 규칙을 설정 하면 확인 합니다.
       
    </p>
    
    **Azure 로그 분석 작업 영역:**</p>
    따릅니다 [Azure PowerShell을 사용 하 여 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh) 작업 영역을 만들려면.
3. Azure AD Domain Services 인스턴스에 대 한 리소스 ID를 가져옵니다. 열린, 인증 된 Windows PowerShell 콘솔에서 다음 명령을 입력 합니다. 사용 된 **$aadds 합니다. ResourceId** 향후 cmdlet에 대 한 Azure AD Domain Services 리소스 ID에 대 한 매개 변수로 변수입니다.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. 사용 합니다 **집합 AzDiagnosticSetting** cmdlet을 Azure AD Domain Services 보안 감사 이벤트에 대 한 대상 리소스를 사용 하도록 Azure 진단 설정을 구성 합니다. 변수의 $aadds 아래 예제입니다. ResourceId 리소스 ID를 Azure AD Domain Services 인스턴스를 나타냅니다 (3 단계 참조).</p>
    **azure storage:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    바꿉니다 *storageAccountId* 저장소 계정 id입니다.</p>
    
    **Azure event hubs의 경우:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    바꿉니다 *eventHubName* 이벤트 허브의 이름입니다. 바꿉니다 *eventHubRuleId* 를 이전에 만든 권한 부여 규칙 ID입니다.</p>
    
    **Azure 로그 분석 작업 영역:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    바꿉니다 *workspaceId* 이전에 만든 Log Analytics 작업 영역 id입니다. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Azure Monitor를 사용 하 여 보안 감사 이벤트 보기
로그 분석 작업 영역을 사용 하 여 확인 하 고 Azure Monitor 및 Kusto 쿼리 언어를 사용 하 여 보안 감사 이벤트를 분석할 수 있습니다. 쿼리 언어는 읽기 쉬운 구문 사용 하 여 전원 분석 기능을 제공 하는 읽기 전용으로 사용 하도록 설계 되었습니다.
Kusto 쿼리 언어를 사용 하 여 시작할 수 있도록 하는 데 일부의 리소스는 다음과 같습니다.
* [Azure Monitor 설명서](https://docs.microsoft.com/azure/azure-monitor/)
* [Azure Monitor에서 Log Analytics 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Azure Monitor에서 로그 쿼리를 사용 하 여 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Log Analytics 데이터의 대시보드 만들기 및 공유](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>샘플 쿼리

### <a name="sample-query-1"></a>예제 쿼리 1
모든 계정 잠금 이벤트 지난 7 일간입니다.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>예제 쿼리 2
모든 계정 잠금 (4740) 사이의 이벤트 2019 년 6 월 26, 오전 9 시 및 2019 년 7 월 1 일 자정 정렬 된 날짜 및 시간을 기준으로 오름차순을 지정 합니다.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>예제 쿼리 3
계정 로그 이벤트에 7 일 전 (지금부터) 명명 된 사용자 계정에 대 한 합니다.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>예제 쿼리 4
계정 로그온 이벤트 7 일 이내에서 이제 계정의 이름이 잘못 된 암호 (0xC0000006a)를 사용 하 여 로그인 하려고 하는 사용자입니다.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>예제 쿼리 5
계정 로그온 이벤트 명명 된 계정 (0xC0000234) 잠긴 동안 로그인 하려고 하는 사용자를 계정에 대해 이제에서 전 7 일입니다.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>예제 쿼리 6
계정 로그온 이벤트의 수에 지금에서 전 7 일 동안 사용자가 잠긴 모든에 대해 발생 한 횟수에 로그인 합니다.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>관련 콘텐츠
* [개요](https://docs.microsoft.com/azure/kusto/query/) 의 Kusto 쿼리 언어입니다.
* [Kusto 자습서](https://docs.microsoft.com/azure/kusto/query/tutorial) 쿼리 기본 사항 알아둘 필요가 있습니다.
* [샘플 쿼리](https://docs.microsoft.com/azure/kusto/query/samples) 데 도움이 되는 데이터를 참조 하는 새로운 방법에 알아봅니다.
* Kusto [모범 사례](https://docs.microsoft.com/azure/kusto/query/best-practices) – 성공에 대 한 쿼리를 최적화 합니다.














 
