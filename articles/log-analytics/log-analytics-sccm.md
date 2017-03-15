---
title: "Log Analytics에 구성 관리자 연결 | Microsoft Docs"
description: "이 문서는 구성 관리자를 Log Analytics에 연결하고 데이터 분석을 시작하는 단계를 보여줍니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: aca83d2de9247bedacce0fb03efe141d903d8605
ms.openlocfilehash: f93d37ad5be4bf7fdc78d83ec68ba56a427b3e35
ms.lasthandoff: 02/23/2017


---
# <a name="connect-configuration-manager-to-log-analytics"></a>Log Analytics에 구성 관리자 연결
System Center Configuration Manager를 OMS의 Log Analytics에 연결하여 장치 수집 데이터를 동기화할 수 있습니다. 이 경우 OMS에서 구성 관리자 계층 구조의 데이터를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Log Analytics는 System Center Configuration Manager 현재 분기, 1606 이상 버전을 지원합니다.  

## <a name="configuration-overview"></a>구성 개요
다음 단계는 구성 관리자를 Log Analytics에 연결하는 과정을 요약하여 보여줍니다.  

1. Azure Management Portal에서 구성 관리자를 웹 응용 프로그램 및/또는 Web API 앱으로 등록하고 Azure Active Directory에서 등록할 때 사용한 클라이언트 ID와 클라이언트 비밀 키가 있는지 확인합니다. 이 단계를 수행하는 방법은 [포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 및 서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md)를 참조하세요.
2. Azure Management Portal에서 [구성 관리자(등록된 웹앱)에 OMS에 대한 액세스 권한을 제공](#provide-configuration-manager-with-permissions-to-oms)합니다.
3. 구성 관리자에서 [DMS 연결 추가 마법사를 사용하여 연결을 추가](#add-an-oms-connection-to-configuration-manager)합니다.
4. 구성 관리자에서 암호 또는 클라이언트 비밀 키가 만료되거나 분실된 경우 [연결 속성을 업데이트](#update-oms-connection-properties)합니다.
5. OMS 포털의 정보를 사용하여 구성 관리자 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에서 [Microsoft Monitoring Agent를 다운로드 및 설치](#download-and-install-the-agent)합니다. 에이전트가 구성 관리자 데이터를 OMS로 전송합니다.
6. Log Analytics에서 컴퓨터 그룹으로 [구성 관리자의 컬렉션을 가져옵니다](#import-collections).
7. Log Analytics에서 [컴퓨터 그룹](log-analytics-computer-groups.md)으로 구성 관리자에서 데이터를 봅니다.

[Microsoft Operations Management Suite에 구성 관리자의 데이터 동기화](https://technet.microsoft.com/library/mt757374.aspx)에서 구성 관리자를 OMS에 연결하는 방법에 대해 자세히 읽을 수 있습니다.

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>구성 관리자에 OMS에 대한 사용 권한 제공
다음 절차에서는 Azure Management Portal에 OMS에 대한 액세스 권한을 제공합니다. 특히, Azure 관리 포털에서 구성 관리자를 OMS에 연결할 수 있도록 하기 위해 리소스 그룹의 사용자에게 *참가자 역할*을 부여해야 합니다.

> [!NOTE]
> 구성 관리자의 OMS에 사용 권한을 지정해야 합니다. 그렇지 않고 구성 관리자에세 구성 마법사를 사용하면 오류 메시지가 표시됩니다.
>
>

1. [Azure Portal](https://portal.azure.com/)을 열고 **찾아보기** > **Log Analytics(OMS)**를 클릭하여 Log Analytics(OMS) 블레이드를 엽니다.  
2. **Log Analytics(OMS)** 블레이드에서 **추가**를 클릭해 **OMS 작업 영역** 블레이드를 엽니다.  
   ![OMS 블레이드](./media/log-analytics-sccm/sccm-azure01.png)
3. **OMS 작업 영역** 블레이드에서 다음 정보를 제공한 다음 **확인**을 클릭합니다.

   * **OMS 작업 영역**
   * **구독**
   * **리소스 그룹**
   * **위치**:
   * **가격 책정 계층**  
     ![OMS 블레이드](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > 위의 예제는 새 리소스 그룹을 만듭니다. 리소스 그룹은 구성 관리자에 이 예제의 OMS 작업 영역에 대한 권한을 제공하는 데에만 사용됩니다.
     >
     >
4. **찾아보기** > **리소스 그룹**을 클릭하여 **리소스 그룹** 블레이드를 엽니다.
5. **리소스 그룹** 블레이드에서 위에서 만든 리소스 그룹을 클릭하여 &lt;리소스 그룹 이름&gt; 설정 블레이드를 엽니다.  
   ![리소스 그룹 설정 블레이드](./media/log-analytics-sccm/sccm-azure03.png)
6. &lt;리소스 그룹&gt; 이름 설정 블레이드에서 Access Control(IAM)을 클릭하여 &lt;리소스 그룹 이름&gt; 사용자 블레이드를 엽니다.  
   ![리소스 그룹 사용자 블레이드](./media/log-analytics-sccm/sccm-azure04.png)  
7. &lt;리소스 그룹 이름&gt; 사용자 블레이드에서 **추가**를 클릭하여 **액세스 추가** 블레이드를 엽니다.
8. **액세스 추가** 블레이드에서 **역할 선택**을 클릭한 다음 **참가자** 역할을 선택합니다.  
   ![역할 선택](./media/log-analytics-sccm/sccm-azure05.png)  
9. **사용자 추가**를 클릭하고 구성 관리자 사용자를 선택한 다음 **선택**을 클릭하고 **확인**을 클릭합니다.  
   ![사용자 추가](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>구성 관리자에 OMS 연결 추가
OMS 연결을 추가하려면 구성 관리자 환경에 온라인 모드를 위해 구성된 [서비스 연결 지점](https://technet.microsoft.com/library/mt627781.aspx)이 있어야 합니다.

1. 구성 관리자의 **관리** 작업 영역에서 **OMS 커넥터**를 선택합니다. 그러면 **OMS 연결 추가 마법사**가 열립니다. **다음**을 선택합니다.
2. **일반** 화면에서 아래의 작업을 완료했는지와 각 항목에 대한 상세 정보가 있는지 확인하고 **다음**을 선택합니다.

   1. Azure Management Portal에서 구성 관리자를 웹 응용 프로그램 및/또는 웹 API 앱으로 등록했는지 여부 및 [등록 시 지정한 클라이언트 ID](../active-directory/active-directory-integrating-applications.md)가 있는지 여부
   2. Azure Management Portal에서 Azure Active Directory에 등록된 앱의 앱 비밀 키를 만들었는지 여부  
   3. Azure Management Portal에서 등록된 웹앱에 OMS에 대한 액세스 권한을 제공했는지 여부  
      ![OMS 마법사 일반 페이지 연결](./media/log-analytics-sccm/sccm-console-general01.png)
3. **Azure Active Directory** 화면에서 **테넌트** , **클라이언트 ID** , **클라이언트 비밀 키**를 지정하여 OMS에 대한 연결 설정을 구성하고 **다음**을 클릭합니다.  
   ![OMS Wizard Azure Active Directory 페이지에 연결](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. 나머지 절차를 성공적으로 완료한 경우 이 페이지에 **OMS 연결 구성** 화면이 자동으로 나타납니다. **Azure 구독**, **Azure 리소스 그룹**, **Operations Management Suite 작업 영역**에 대한 연결 설정 정보가 나타납니다.  
   ![OMS 연결 마법사의 OMS 연결 페이지](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. 이 마법사는 사용자가 입력한 정보를 사용하여 OMS 서비스에 연결합니다. OMS와 동기화하려는 장치 컬렉션을 선택한 다음 **추가**를 클릭합니다.  
   ![컬렉션 선택](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. **요약** 화면에서 연결 설정을 확인하고 **다음**을 선택합니다. **진행률** 화면에 연결 상태와 **완료**가 차례로 나타납니다.

> [!NOTE]
> OMS를 계층의 최고 계층 사이트에 연결해야 합니다. OMS를 독립형 기본 사이트에 연결한 다음 환경에 중앙 관리 사이트를 추가할 경우 새 계층 안에서 OMS 연결을 삭제하고 다시 만들어야 합니다.
>
>

OMS에 구성 관리자를 연결한 후에는 컬렉션을 추가 또는 제거하고 OMS 연결의 속성을 확인합니다.

## <a name="update-oms-connection-properties"></a>OMS 연결 속성 업데이트
암호 또는 클라이언트 비밀 키가 만료되거나 분실된 경우 OMS 연결 속성을 수동으로 업데이트해야 합니다.

1. **구성 관리자**에서 Cloud Services로 이동한 다음 **OMS 커넥터**를 선택하여 **OMS 연결 속성** 페이지를 엽니다.
2. 이 페이지에서 **Azure Active Directory** 탭을 클릭하여 **테넌트**, **클라이언트 ID**, **클라이언트 비밀 키 만료**를 클릭합니다. **클라이언트 비밀 키**가 만료되었는지 **확인**합니다.

## <a name="download-and-install-the-agent"></a>에이전트 다운로드 및 설치
1. OMS 포털에서 [OMS 에이전트 설치 파일을 다운로드](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms)합니다.
2. 다음 중 한 가지 방법을 사용하여 구성 관리자 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에 에이전트를 설치 및 구성합니다.
   * [설치를 에이전트 설치](log-analytics-windows-agents.md#install-the-agent-using-setup)
   * [명령줄을 사용하여 에이전트 설치](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
   * [Azure Automation에서 DSC를 사용하여 에이전트 설치](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)

## <a name="import-collections"></a>컬렉션 가져오기
구성 관리자에 OMS 연결을 추가하고 구성 관리자 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에 에이전트를 설치한 후 다음 단계는 컴퓨터 그룹으로 OMS의 구성 관리자 컬렉션을 가져오는 것입니다.

가져오기를 사용하도록 설정하면 3시간마다 컬렉션 멤버 자격 정보가 검색되어 컬렉션 멤버 자격을 최신 상태로 유지합니다. 언제든지 가져오기를 비활성화할 수 있습니다.

1. OMS 포털에서 **설정**을 클릭합니다.
2. **컴퓨터 그룹** 탭을 클릭한 다음 **SCCM** 탭을 클릭합니다.
3. **구성 관리자 컬렉션 멤버 자격 가져오기**를 선택한 다음 **저장**을 클릭합니다.  
   ![컴퓨터 그룹 - SCCM 탭](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>구성 관리자의 데이터 보기
구성 관리자에 OMS 연결을 추가하고 구성 관리자 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에 에이전트를 설치하면 에이전트의 데이터가 OMS로 전송됩니다. OMS에서 구성 관리자 컬렉션이 [컴퓨터 그룹](log-analytics-computer-groups.md)으로 나타납니다. **설정**의 **컴퓨터 그룹**에서 **구성 관리자** 페이지의 그룹을 볼 수 있습니다.

컬렉션을 가져오면 컬렉션 멤버 자격이 있는 컴퓨터 중 삭제된 컴퓨터 수를 확인할 수 있습니다. 또한 가져온 컬렉션 수도 확인할 수 있습니다.

![컴퓨터 그룹 - SCCM 탭](./media/log-analytics-sccm/sccm-computer-groups02.png)

각 항목을 클릭하면 가져온 그룹 전체 또는 각 그룹에 속하는 전체 컴퓨터가 표시된 검색이 열립니다. [로그 검색](log-analytics-log-searches.md)을 사용하여 구성 관리자 데이터의 자세한 분석을 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [로그 검색](log-analytics-log-searches.md)을 사용하여 구성 관리자 데이터에 대한 자세한 정보를 볼 수 있습니다.

