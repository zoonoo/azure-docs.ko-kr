---
title: 구성 관리자를 Azure 모니터에 연결 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Monitor의 작업 영역에 구성 관리자를 연결하고 데이터 분석을 시작하는 단계를 보여 주며 있습니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655259"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>구성 관리자를 Azure 모니터에 연결
Microsoft Endpoint 구성 관리자 환경을 Azure 모니터에 연결하여 장치 컬렉션 데이터를 동기화하고 Azure 모니터 및 Azure 자동화에서 이러한 컬렉션을 참조할 수 있습니다.  

## <a name="prerequisites"></a>사전 요구 사항

Azure Monitor는 구성 관리자 현재 분기, 버전 1606 이상을 지원합니다.

>[!NOTE]
>구성 관리자를 Log Analytics 작업 영역과 연결하는 기능은 선택 사항이며 기본적으로 활성화되지 않습니다. 이 기능은 사용하기 전에 활성화해야 합니다. 자세한 내용은 [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options)을 참조하십시오.

## <a name="configuration-overview"></a>구성 개요

다음 단계는 Azure Monitor와의 구성 관리자 통합을 구성하는 단계를 요약합니다.  

1. Azure Active Directory에서 구성 관리자를 웹 응용 프로그램 및/또는 웹 API 앱으로 등록하고 Azure Active Directory의 등록에서 클라이언트 ID 및 클라이언트 비밀 키가 있는지 확인합니다. 이 단계를 수행하는 방법에 대한 자세한 내용은 [포털 사용하여 리소스에 액세스할 수 있는 Active Directory 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

2. Azure Active Directory에서 [Azure Monitor에 액세스할 수 있는 권한이 있는 구성 관리자(등록된 웹 앱)를 부여합니다.](#grant-configuration-manager-with-permissions-to-log-analytics)

3. 구성 관리자에서 Azure 서비스 마법사를 사용하여 연결을 **추가합니다.**

4. Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에 [Windows용 로그 분석 에이전트를 다운로드하여 설치합니다.](#download-and-install-the-agent) 에이전트는 Azure 모니터의 로그 분석 작업 영역으로 구성 관리자 데이터를 보냅니다.

5. Azure Monitor에서 [구성 관리자에서 컬렉션을](#import-collections) 컴퓨터 그룹으로 가져옵니다.

6. Azure 모니터에서 구성 관리자의 데이터를 [컴퓨터 그룹으로](computer-groups.md)봅니다.

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Configuration Manager에 Log Analytics에 대한 사용 권한 제공

다음 절차에서는 Log Analytics 작업 영역의 *Contributor* 역할을 Configuration Manager에 대해 이전에 만든 AD 애플리케이션 및 서비스 주체에 부여합니다. 작업 영역이 아직 없는 경우 진행하기 전에 [Azure Monitor의 작업 영역 만들기를](../../azure-monitor/learn/quick-create-workspace.md) 참조하십시오. 이를 통해 Configuration Manager는 Log Analytics 작업 영역을 인증하고 연결할 수 있습니다.  

> [!NOTE]
> 구성 관리자에 대한 로그 분석 작업 영역에서 권한을 지정해야 합니다. 그렇지 않고 Configuration Manager에서 구성 마법사를 사용하면 오류 메시지가 표시됩니다.
>

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **로그 분석을**입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **로그 분석을 선택합니다.**

2. Log Analytics 작업 영역 목록에서 수정할 작업 영역을 선택합니다.

3. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.

4. [액세스 제어(IAM)] 페이지에서 **역할 할당 추가**를 클릭합니다. 그러면 **역할 할당 추가** 창이 나타납니다.

5. **역할 할당 추가** 창의 **역할** 드롭다운 목록 아래에서 **기여자** 역할을 선택합니다.  

6. **다음에 대한 액세스 할당** 드롭다운 목록 아래에서 이전에 AD에서 만든 Configuration Manager 애플리케이션을 선택한 다음, **확인**을 클릭합니다.  

## <a name="download-and-install-the-agent"></a>에이전트 다운로드 및 설치

[Windows 컴퓨터를 Azure의 Azure 모니터에 연결하는](agent-windows.md) 문서를 검토하여 Configuration Manager 서비스 연결 지점 시스템 역할을 호스팅하는 컴퓨터에 Windows용 Log Analytics 에이전트를 설치하는 데 사용할 수 있는 방법을 이해합니다.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>구성 관리자를 로그 분석 작업 영역에 연결

>[!NOTE]
> 로그 분석 연결을 추가하려면 Configuration Manager 환경에 온라인 모드로 구성된 [서비스 연결 지점이](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) 있어야 합니다.

> [!NOTE]
> 계층 구조의 최상위 사이트를 Azure Monitor에 연결해야 합니다. 독립 실행형 기본 사이트를 Azure Monitor에 연결한 다음 환경에 중앙 관리 사이트를 추가하는 경우 새 계층 구조 내에서 연결을 삭제하고 다시 만들어야 합니다.

1. 구성 관리자의 **관리** 작업 영역에서 **클라우드 서비스를** 선택한 다음 Azure **서비스를**선택합니다. 

2. **Azure 서비스** 오른쪽 단추를 클릭한 다음 Azure **서비스 구성을**선택합니다. **Azure 서비스 구성** 페이지가 나타납니다. 
   
3. **일반** 화면에서 아래의 작업을 완료했는지와 각 항목에 대한 상세 정보가 있는지 확인하고 **다음**을 선택합니다.

4. Azure 서비스 마법사의 Azure 서비스 페이지에서 다음 작업을 수행합니다.

    1. Configuration Manager의 개체 **이름**을 지정합니다.
    2. 서비스를 식별하는 데 도움이 되도록 선택적 **설명**을 지정합니다.
    3. Azure 서비스 **OMS 커넥터를**선택합니다.

    >[!NOTE]
    >OMS는 이제 Azure 모니터의 기능인 로그 분석이라고 합니다.

5. **다음**을 선택하여 Azure 서비스 마법사의 Azure 앱 속성 페이지로 넘어갑니다.

6. Azure 서비스 마법사의 **앱** 페이지에서 먼저 목록에서 Azure 환경을 선택한 다음 **가져오기**를 클릭합니다.

7. 앱 **가져오기** 페이지에서 다음 정보를 지정합니다.

    1. 앱에 대한 **Azure AD 테넌트 이름을** 지정합니다.

    2. Azure **AD 테넌트 ID를** Azure AD 테넌트를 지정합니다. 이 정보는 Azure Active 디렉터리 **속성** 페이지에서 찾을 수 있습니다. 

    3. 응용 **프로그램 이름을** 응용 프로그램 이름에 지정합니다.

    4. 클라이언트 **ID를**지정합니다.

    5. 생성된 Azure AD 앱의 클라이언트 비밀 키인 **비밀**키를 지정합니다.

    6. 비밀 **키 만료,** 키의 만료 날짜를 지정합니다.

    7. 이전에 만든 Azure AD 앱의 **앱 ID URI인 앱**ID URI를 지정합니다.

    8. **확인을** 선택하고 오른쪽에 결과가 **성공적으로 확인됨을 표시합니다!**.

8. **구성** 페이지에서 정보를 검토하여 Azure **구독,** **Azure 리소스 그룹**및 운영 관리 Suite 작업 **영역필드가** Azure AD 응용 프로그램에 리소스 그룹에 충분한 권한이 있음을 나타내는 사전 채워집니다. 필드가 비어 있으면 응용 프로그램에 필요한 권한이 없음을 나타냅니다. 장치 컬렉션을 선택하여 작업 영역으로 수집하고 전달한 다음 **추가 를**선택합니다.

9. **설정 확인** 페이지에서 옵션을 검토하고 **다음을** 선택하여 연결을 만들고 구성합니다.

10. 구성이 완료되면 **완료** 페이지가 나타납니다. **닫기**를 선택합니다. 

Configuration Manager를 Azure Monitor에 연결한 후 컬렉션을 추가 또는 제거하고 연결의 속성을 볼 수 있습니다.

## <a name="update-log-analytics-workspace-connection-properties"></a>로그 분석 작업 영역 연결 속성 업데이트

암호 또는 클라이언트 비밀 키가 만료되거나 손실된 경우 Log Analytics 연결 속성을 수동으로 업데이트해야 합니다.

1. 구성 관리자의 **관리** 작업 영역에서 **클라우드 서비스를** 선택한 다음 **OMS 커넥터를** 선택하여 **OMS 연결 속성** 페이지를 엽니다.
2. 이 페이지에서 **Azure Active Directory** 탭을 클릭하여 **테넌트**, **클라이언트 ID**, **클라이언트 비밀 키 만료**를 클릭합니다. **클라이언트 비밀 키**가 만료되었는지 **확인**합니다.

## <a name="import-collections"></a>컬렉션 가져오기

구성 관리자에 Log Analytics 연결을 추가하고 Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에 에이전트를 설치한 후 다음 단계는 Azure의 Configuration Manager에서 컬렉션을 가져오는 것입니다. 컴퓨터 그룹으로 모니터링합니다.

계층 구조에서 장치 컬렉션을 가져오기 위한 초기 구성을 완료한 후 3시간마다 컬렉션 정보가 검색되어 멤버 자격을 최신 상태로 유지합니다. 언제든지 이를 비활성화하도록 선택할 수 있습니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **로그 분석을**입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역**을 선택합니다.
2. Log Analytics 작업 영역 목록에서 Configuration Manager를 등록할 작업 영역을 선택합니다.  
3. **고급 설정**을 선택합니다.
4. **컴퓨터 그룹**을 선택한 다음, **SCCM**을 선택합니다.  
5. **구성 관리자 컬렉션 멤버 자격 가져오기**를 선택한 다음 **저장**을 클릭합니다.  
   
    ![컴퓨터 그룹 - SCCM 탭](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>구성 관리자의 데이터 보기

구성 관리자에 Log Analytics 연결을 추가하고 Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에 에이전트를 설치한 후 에이전트의 데이터가 Azure Monitor의 Log Analytics 작업 영역으로 전송됩니다. Azure 모니터에서 구성 관리자 컬렉션이 [컴퓨터 그룹으로](../../azure-monitor/platform/computer-groups.md)나타납니다. **Settings\Computer Groups** 아래 **Configuration Manager** 페이지에서 그룹을 볼 수 있습니다.

컬렉션을 가져오면 컬렉션 멤버 자격이 있는 컴퓨터 중 삭제된 컴퓨터 수를 확인할 수 있습니다. 또한 가져온 컬렉션 수도 확인할 수 있습니다.

![컴퓨터 그룹 - SCCM 탭](./media/collect-sccm/sccm-computer-groups02.png)

둘 중 하나를 클릭하면 가져온 모든 그룹 또는 각 그룹에 속한 모든 컴퓨터가 표시되는 로그 쿼리 편집기가 열립니다. [로그 검색을](../../azure-monitor/log-query/log-query-overview.md)사용하여 컬렉션 멤버 자격 데이터에 대한 심층 분석을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 사용하여 구성 관리자 데이터에 대한 자세한 정보를 볼 수 있습니다.
