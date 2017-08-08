---
title: "Azure 로그 통합 FAQ | Microsoft Docs"
description: "이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: e6aefe5f16e7148f7837a8741355c61851618495
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017


---
# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Azure 로그 통합 FAQ(질문과 대답)
이 FAQ는 Azure 리소스의 원시 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템에 통합할 수 있게 해주는 서비스인 Azure 로그 통합에 대한 질문에 답변합니다. 이 통합은 응용 프로그램과 관련된 보안 이벤트를 집계하고, 상관 관계를 설정하고, 분석하고, 경고할 수 있도록 온-프레미스 또는 클라우드의 모든 자산에 대한 통합 대시보드를 제공합니다.

## <a name="is-the-azure-log-integration-software-free"></a>Azure 로그 통합 소프트웨어는 무료입니까?
예. Azure 로그 통합 소프트웨어는 무료입니다.

## <a name="where-is-azure-log-integration-available"></a>Azure 로그 통합은 어디에서 사용할 수 있나요?

현재 Azure 상용 및 Azure Government에서 사용할 수 있으며 중국 또는 독일에서는 사용할 수 없습니다.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Azure 로그 통합이 Azure VM 로그를 가져오는 원본 저장소 계정을 확인하려면 어떻게 하나요?
**azlog source list**명령을 실행합니다.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Azure 로그 통합 로그를 가져오는 원본 구독을 확인하려면 어떻게 하나요?

AzureResourcemanagerJson 디렉터리에 배치된 감사 로그의 경우 구독 ID는 로그 파일 이름에 있습니다. AzureSecurityCenterJson 폴더에 있는 로그의 경우도 마찬가지입니다. 예:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Azure Active Directory 감사 로그에는 테넌트 ID가 이름의 일부로 포함됩니다.

이벤트 허브에서 읽은 진단 로그의 경우 구독 ID가 이름의 일부로 포함되지 않고 이벤트 허브 원본 생성 시 지정된 이름이 포함됩니다. 

## <a name="how-can-i-update-the-proxy-configuration"></a>프록시 구성을 업데이트하려면 어떻게 해야 합니까?
프록시 설정에서 Azure 저장소에 대한 직접 액세스를 허용하지 않으면 **c:\Program Files\Microsoft Azure Log Integration**에 있는 **AZLOG.EXE.CONFIG** 파일을 엽니다. 조직의 프록시 주소를 사용하여 **defaultProxy** 섹션을 포함하도록 이 파일을 업데이트합니다. 업데이트가 완료되면 **net stop azlog** 및 **net start azlog** 명령을 사용하여 서비스를 중지하고 다시 시작합니다.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Windows 이벤트에서 구독 정보를 확인하려면 어떻게 해야 합니까?
소스를 추가 하는 동안 이름에 **subscriptionid** 를 추가합니다.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
이벤트 XML은 구독 id를 포함하여 아래와 같이 메타데이터를 포함하고 있습니다.

![이벤트 XML][1]

## <a name="error-messages"></a>오류 메시지
### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>**azlog createazureid**명령을 실행할 때 다음 오류가 발생하는 이유는 무엇인가요?
오류:

  *AAD 응용 프로그램을 만들 수 없음 - 테넌트 72f988bf-86f1-41af-91ab-2d7cd011db37 - 이유 = '금지됨' - 메시지 = '권한이 부족하여 작업을 완료할 수 없습니다.'*

**Azlog createazureid** 명령은 Azure 로그인이 액세스 권한을 갖고 있는 구독에 대한 모든 Azure AD 테넌트에 서비스 주체를 만들려고 시도합니다. 사용자의 Azure 로그인이 해당 Azure AD 테넌트의 게스트 사용자인 경우 ‘권한이 부족하여 작업을 완료할 수 없습니다’ 오류와 함께 명령이 실패합니다. 계정을 테넌트에 사용자로 추가해 달라고 테넌트 관리자에게 요청하세요.

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>**azlog authorize**명령을 실행할 때 다음 오류가 발생하는 이유는 무엇인가요?
오류:

  *역할 할당 만들기 경고 - AuthorizationFailed: 개체 ID가 'fe9e03e4-4dad-4328-910f-fd24a9660bd2'인 janedo@microsoft.com 클라이언트에는 '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000' 범위에 대해 'Microsoft.Authorization/roleAssignments/write' 작업을 수행할 권한이 없습니다.*

**Azlog authorize** 명령을 사용하면 제공되는 구독에 **Azlog createazureid** 명령으로 만들어진 Azure AD 서비스 주체의 읽기 권한자 역할을 할당합니다. Azure 로그인이 구독의 공동 관리자 또는 소유자가 아닌 경우 "권한 부여 실패" 오류 메시지와 오류가 발생합니다. 이 작업을 완료하려면 공동 관리자 또는 소유자의 Azure RBAC(역할 기반 액세스 제어)가 필요합니다.

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>감사 로그의 속성 정의는 어디서 찾을 수 있나요?
다음을 참조하세요.

* [리소스 관리자로 작업 감사](../azure-resource-manager/resource-group-audit.md)
* [Azure Monitor REST API에서 구독의 관리 이벤트 나열](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Azure 보안 센터 경고에 대한 세부 정보는 어디서 찾을 수 있나요?
[Azure 보안 센터에서 보안 경고 관리 및 대응](../security-center/security-center-managing-and-responding-alerts.md)을 참조하세요.

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>VM 진단을 통해 수집되는 항목을 어떻게 수정할 수 있나요?
Windows Azure 진단 [(WAD)](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 구성을 가져오고, 수정하고, 설정하는 방법에 대한 자세한 내용은 *PowerShell을 사용하여 Windows를 실행하는 가상 컴퓨터에서 Azure 진단을 사용하도록 설정* 을 참조하세요. 다음은 샘플입니다.

### <a name="get-the-wad-config"></a>WAD 구성 가져오기
    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>WAD 구성 수정
다음은 보안 이벤트 로그에서 EventID 4624 및 EventId 4625만 수집되는 구성 예입니다. 시스템 이벤트 로그에서 Microsoft 맬웨어 방지 이벤트가 수집됩니다. XPath 식 사용에 대한 자세한 내용은 [이벤트 사용](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)을 참조하세요.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>WAD 구성 설정
    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

구성을 변경한 후에는 저장소 계정을 검사하여 올바른 이벤트가 수집되는지 확인합니다.

설치 및 구성 중에 문제가 발생하면 [지원 요청](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 열고 지원을 요청하는 서비스로 **로그 통합**을 선택합니다.

### <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Azure 로그 통합을 사용하여 Network Watcher 로그를 내 SIEM에 통합할 수 있나요?

Network Watcher는 다량의 로깅 정보를 생성하며, 이러한 로그는 SIEM에 전송하기 위한 것이 아닙니다. Network Watcher 로그의 대상으로 저장소 계정만 지원됩니다. Azlog는 이러한 로그를 읽고 SIEM에 제공하는 기능을 지원하지 않습니다.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

