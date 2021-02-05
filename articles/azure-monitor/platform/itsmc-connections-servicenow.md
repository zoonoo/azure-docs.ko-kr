---
title: IT 서비스 관리 커넥터를 사용 하 여 ServiceNow 연결
description: Azure Monitor에서 ServiceNow를 IT 서비스 관리 커넥터 (ITSMC)와 연결 하 여 ITSMC 작업 항목을 중앙에서 모니터링 하 고 관리 하는 방법을 알아봅니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 222257f5045984a71c2aee9de83b5fa420306728
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573418"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>IT 서비스 관리 커넥터를 사용 하 여 ServiceNow 연결

이 문서에서는 ITSMC (IT Service Management) 작업 항목을 중앙에서 관리할 수 있도록 Log Analytics의 ServiceNow 인스턴스와 IT 서비스 관리 커넥터 (ITSMC) 간에 연결을 구성 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소
연결에 대 한 다음 필수 구성 요소를 충족 하는지 확인 합니다.

### <a name="itsmc-installation"></a>ITSMC 설치

ITSMC를 설치 하는 방법에 대 한 자세한 내용은 [IT 서비스 관리 커넥터 솔루션 추가](./itsmc-definition.md#add-it-service-management-connector)를 참조 하세요.

> [!NOTE]
> ITSMC는 ServiceNow의 공식 SaaS (software as a service) 제공만 지원 합니다. ServiceNow의 개인 배포는 지원 되지 않습니다.

### <a name="oauth-setup"></a>OAuth 설정

ServiceNow 지원 버전에는 파리, 올랜도, 뉴욕, 마드리드, 런던, Kingston, 자카르타, 이스탄불, 헬싱키 및 Geneva가 있습니다.

ServiceNow 관리자는 ServiceNow 인스턴스에 대 한 클라이언트 ID 및 클라이언트 암호를 생성 해야 합니다. 필요에 따라 다음 정보를 참조 하세요.

- [파리에 대해 OAuth 설정](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [올랜도에 대 한 OAuth 설정](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [New York에 대해 OAuth 설정](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Madrid에 대해 OAuth 설정](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [London에 대해 OAuth 설정](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Kingston에 대해 OAuth 설정](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Jakarta에 대해 OAuth 설정](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Istanbul에 대해 OAuth 설정](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Helsinki에 대해 OAuth 설정](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Geneva에 대해 OAuth 설정](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

OAuth 설정의 일부로 다음을 수행 하는 것이 좋습니다.

1. [클라이언트에서 인스턴스에 액세스할 수 있는 끝점을 만듭니다](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. 새로 고침 토큰의 수명을 업데이트 합니다.

   1. **ServiceNow** 창에서 **System OAuth** 를 검색 하 고 **응용 프로그램 레지스트리** 를 선택 합니다. 
   1. 정의 된 OAuth의 이름을 선택 하 고 **새로 고침 토큰 수명을** **7776000 초** (90 일)로 변경 합니다. 
   1. **업데이트** 를 선택합니다. 

1. 연결이 활성 상태로 유지 되도록 내부 프로시저를 설정 합니다. 새로 고침 토큰 수명이 정상적으로 만료 되기까지 몇 일이 지나면 다음 작업을 수행 합니다.

   1. [ITSM 커넥터 구성에 대 한 수동 동기화 프로세스를 완료](./itsmc-resync-servicenow.md)합니다.

   1. 이전 새로 고침 토큰을 취소 합니다. 보안상의 이유로 이전 키를 유지 하지 않는 것이 좋습니다. 
   
      1. **ServiceNow** 창에서 **System OAuth** 를 검색 한 다음 **토큰 관리** 를 선택 합니다. 
      
      1. OAuth 이름 및 만료 날짜에 따라 목록에서 이전 토큰을 선택 합니다.

         ![OAuth에 대 한 토큰 목록을 보여 주는 스크린샷](media/itsmc-connections/snow-system-oauth.png)
      1. **액세스**  >  **취소** 취소를 선택 합니다.

## <a name="install-the-user-app-and-create-the-user-role"></a>사용자 앱을 설치 하 고 사용자 역할을 만듭니다.

다음 절차를 사용 하 여 지금 서비스 사용자 앱을 설치 하 고이에 대 한 통합 사용자 역할을 만듭니다. 이러한 자격 증명을 사용 하 여 Azure에서 ServiceNow 연결을 만듭니다.

> [!NOTE]
> ITSMC는 ServiceNow 저장소에서 다운로드 된 Microsoft Log Analytics 통합을 위한 공식 사용자 앱만 지원 합니다. ITSMC는 ServiceNow 쪽 또는 공식 ServiceNow 솔루션에 포함 되지 않은 응용 프로그램에 대 한 코드 수집을 지원 하지 않습니다. 

1. Servicenow [저장소](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) 를 방문 하 고 ServiceNow 인스턴스에 **SERVICENOW 및 Microsoft OMS 통합을 위한 사용자 앱** 을 설치 합니다.
   
   >[!NOTE]
   >Microsoft Operations Management Suite (OMS)에서 Azure Monitor로 진행 중인 전환의 일부로 OMS를 이제 Log Analytics 이라고 합니다.     
2. 설치 후 ServiceNow 인스턴스의 왼쪽 탐색 모음으로 이동 하 여 **MICROSOFT OMS 통합자** 를 검색 하 고 선택 합니다.  
3. **설치 검사 목록** 을 선택 합니다.

   사용자 역할이 아직 만들어지지 않았기 때문에 상태가  **완료 되지 않음** 으로 표시 됩니다.

4. **통합 사용자 만들기** 옆의 텍스트 상자에 AZURE에서 itsmc에 연결할 수 있는 사용자의 이름을 입력 합니다.
5. 이 사용자에 대 한 암호를 입력 한 다음 **확인** 을 선택 합니다.  

새로 만든 사용자가 기본 역할이 할당 된 상태로 표시 됩니다.

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

사용자를 성공적으로 만든 후에는 **설치 검사 목록 확인** 의 상태가 **완료** 로 이동 하 고 앱에 대해 만든 사용자 역할의 세부 정보를 나열 합니다.

> [!NOTE]
> ITSMC는 ServiceNow 인스턴스에 설치 된 다른 모듈 없이 ServiceNow에 인시던트를 보낼 수 있습니다. ServiceNow 인스턴스에서 EventManagement 모듈을 사용 중이 고 커넥터를 사용 하 여 ServiceNow에서 이벤트 또는 경고를 만들려는 경우 통합 사용자에 게 다음 역할을 추가 합니다.
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>연결 만들기
다음 절차를 사용 하 여 ServiceNow 연결을 만들 수 있습니다.

> [!NOTE]
> Azure Monitor에서 전송 되는 경고는 ServiceNow에서 이벤트, 인시던트 또는 경고 요소 중 하나를 만들 수 있습니다. 

1. Azure Portal에서 **모든 리소스** 로 이동 하 여 **Servicedesk (YourWorkspaceName)** 를 찾습니다.

2. **작업 영역 데이터 원본** 에서 **itsm 연결** 을 선택 합니다.

   ![데이터 원본 선택 항목을 보여 주는 스크린샷](media/itsmc-connections/add-new-itsm-connection.png)

3. 오른쪽 창 위쪽에서 **추가** 를 선택 합니다.

4. 다음 표에 설명 된 대로 정보를 제공 하 고 **확인** 을 선택 합니다.

   | **필드** | **설명** |
   | --- | --- |
   | **연결 이름**   | ITSMC에 연결 하려는 ServiceNow 인스턴스의 이름을 입력 합니다. 이 이름은 나중에 ITSM 작업 항목을 구성 하 고 자세한 분석을 볼 때 Log Analytics에서 사용 합니다. |
   | **파트너 유형**   | **ServiceNow** 를 선택합니다. |
   | **서버 Url**   | ITSMC에 연결 하려는 ServiceNow 인스턴스의 URL을 입력 합니다. URL은 *servicenow.com* (예:)를 사용 하 여 지원 되는 SaaS 버전을 가리켜야 합니다 `https://XXXXX.service-now.com/` .|
   | **사용자 이름**   | ITSMC에 대 한 연결을 지원 하기 위해 ServiceNow 앱에서 만든 통합 사용자 이름을 입력 합니다.|
   | **암호**   | 이 사용자 이름과 연결 된 암호를 입력 하십시오. **참고**: 사용자 이름 및 암호는 인증 토큰을 생성 하는 데만 사용 됩니다. ITSMC 서비스 내의 어디에도 저장 되지 않습니다.  |
   | **클라이언트 Id**   | 이전에 생성 한 OAuth2 인증에 사용할 클라이언트 ID를 입력 합니다. 클라이언트 ID와 비밀을 생성 하는 방법에 대 한 자세한 내용은 [OAuth 설정](https://old.wiki/index.php/OAuth_Setup)을 참조 하세요. |
   | **클라이언트 암호**   | 이 ID에 대해 생성 된 클라이언트 암호를 입력 합니다.   |
   | **데이터 동기화 범위 (일)** | 데이터를 원하는 지난 일 수를 입력 합니다. 제한은 120 일입니다. |
   | **동기화 할 작업 항목**   | ITSMC를 통해 Azure Log Analytics와 동기화 할 ServiceNow 작업 항목을 선택 합니다. 선택한 값을 Log Analytics으로 가져옵니다. 인시던트 및 변경 요청 옵션입니다.|
   | **ITSM 제품에서 새 구성 항목 만들기** | ITSM 제품에서 구성 항목을 만들려는 경우 이 옵션을 선택합니다. 이 항목을 선택 하면 ITSMC는 지원 되는 ITSMC 시스템에 구성 항목 (없는 경우)을 만듭니다. 기본적으로 사용하지 않도록 설정되어 있습니다. |

![ServiceNow 연결을 추가 하기 위한 상자 및 옵션의 스크린샷](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

성공적으로 연결 되 고 동기화 된 경우:

- ServiceNow 인스턴스에서 선택한 작업 항목을 Log Analytics으로 가져옵니다. **IT 서비스 관리 커넥터** 타일에서 이러한 작업 항목의 요약을 볼 수 있습니다.

- Log Analytics 경고 또는 로그 레코드나이 ServiceNow 인스턴스의 Azure 경고에서 인시던트를 만들 수 있습니다.

> [!NOTE]
> ServiceNow에는 시간당 요청에 대 한 요율 제한이 있습니다. 제한을 구성 하려면 ServiceNow 인스턴스에서 **인바운드 REST API 전송률 제한을** 정의 합니다.

## <a name="next-steps"></a>다음 단계

* [ITSM 커넥터 개요](itsmc-overview.md)
* [Azure 경고에서 ITSM 작업 항목 만들기](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [ITSM 커넥터 문제 해결](./itsmc-resync-servicenow.md)
