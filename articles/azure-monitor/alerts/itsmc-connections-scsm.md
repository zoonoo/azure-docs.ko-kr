---
title: IT 서비스 관리 커넥터와 SCSM 연결
description: 이 문서에서는 ITSMC 작업 항목을 중앙에서 모니터링 하 고 관리 하기 위해 Azure Monitor에서 ITSMC (IT 서비스 관리 커넥터)를 사용 하는 방법에 대 한 정보를 제공 합니다.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 3f80c42be217d062510c687075cf46b4e7539419
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045346"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>IT 서비스 관리 커넥터를 사용 하 여 System Center Service Manager 연결

이 문서에서는 작업 항목을 중앙에서 관리 하기 위해 Log Analytics에서 System Center Service Manager 인스턴스와 IT 서비스 관리 커넥터 (ITSMC) 간의 연결을 구성 하는 방법에 대 한 정보를 제공 합니다.

다음 섹션에서는 System Center Service Manager 제품을 Azure의 ITSMC에 연결하는 방법을 자세히 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 필수 조건이 충족되는지 확인합니다.

- ITSMC가 설치되어 있습니다. 추가 정보: [IT 서비스 관리 커넥터 솔루션 추가](./itsmc-definition.md).
- Service Manager 웹 애플리케이션(웹앱)이 배포 및 구성되어 있습니다. 웹앱에 대한 정보는 [여기](#create-and-deploy-service-manager-web-app-service)를 참조하세요.
- 하이브리드 연결이 생성 및 구성되어 있습니다. 추가 정보: [하이브리드 연결 구성](#configure-the-hybrid-connection).
- 지원되는 Service Manager 버전:  2012 R2 또는 2016.
- 사용자 역할:  [고급 운영자](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- 현재 Azure Monitor에서 전송 된 경고는 System Center Service Manager 인시던트에 서 만들 수 있습니다.

> [!NOTE]
> - ITSM 커넥터는 클라우드 기반 ServiceNow 인스턴스에만 연결할 수 있습니다. 온-프레미스 ServiceNow 인스턴스는 현재 지원되지 않습니다.
> - 작업의 일부로 사용자 지정 [템플릿을](./itsmc-definition.md#define-a-template) 사용 하려면 scsm 템플릿의 "ProjectionType" 매개 변수를 "IncidentManagement"에 매핑해야 합니다. ProjectionType "를

## <a name="connection-procedure"></a>연결 절차

System Center Service Manager 인스턴스를 ITSMC에 연결하려면 다음 절차를 사용합니다.

1. Azure Portal에서 **모든 리소스** 로 이동하여 **ServiceDesk(YourWorkspaceName)** 를 찾습니다.

2. **작업 영역 데이터 원본** 에서 **ITSM 연결** 을 클릭합니다.

    ![새 연결](media/itsmc-connections-scsm/add-new-itsm-connection.png)

3. 왼쪽 창의 맨 위에 있는 **추가** 를 클릭합니다.

4. 다음 표에 설명된 대로 정보를 제공하고 **확인** 을 클릭하여 연결을 만듭니다.

> [!NOTE]
> 이러한 모든 매개 변수는 필수입니다.

| **필드** | **설명** |
| --- | --- |
| **연결 이름**   | ITSMC에 연결하려는 System Center Service Manager 인스턴스의 이름을 입력합니다.  이 이름은 나중에 이 인스턴스/보기의 자세한 로그 분석에서 작업 항목을 구성할 때 사용합니다. |
| **파트너 유형**   | **System Center Service Manager** 를 선택합니다. |
| **서버 URL**   | Service Manager 웹앱의 URL을 입력합니다. Service Manager 웹앱에 대한 자세한 내용은 [여기](#create-and-deploy-service-manager-web-app-service)에 나와 있습니다.
| **클라이언트 ID**   | 웹앱을 인증하기 위해 생성한 클라이언트 ID를 입력합니다(자동 스크립트 사용). 자동화된 스크립트에 대한 자세한 내용은 [여기](./itsmc-service-manager-script.md)에 나와 있습니다.|
| **클라이언트 암호**   | 이 ID에 대해 생성된 클라이언트 암호를 입력합니다.   |
| **데이터 동기화**   | ITSMC를 통해 동기화할 Service Manager 작업 항목을 선택합니다.  이러한 작업 항목을 Log Analytics로 가져옵니다. **옵션:**  인시던트, 변경 요청.|
| **데이터 동기화 범위** | 데이터를 원하는 이전 일 수를 입력합니다. **최대 제한**: 120일. |
| **ITSM 솔루션에서 새 구성 항목 만들기** | ITSM 제품에서 구성 항목을 만들려는 경우 이 옵션을 선택합니다. 이 옵션을 선택하면 Log Analytics는 지원되는 ITSM 시스템에서 영향을 받는 CI를 구성 항목으로(존재하지 않는 CI의 경우) 만듭니다. **기본**: 사용하지 않도록 설정됩니다. |

![서비스 관리자 연결](media/itsmc-connections-scsm/service-manager-connection.png)

**성공적으로 연결 및 동기화된 경우**:

- Service Manager에서 선택한 작업 항목을 Azure **Log Analytics** 로 가져옵니다. IT Service Management Connector 타일에서 이러한 작업 항목에 대한 요약을 볼 수 있습니다.

- Log Analytics 경고 또는 로그 레코드에서, 또는 이 Service Manager 인스턴스의 Azure 경고에서 인시던트를 만들 수 있습니다.

자세한 정보: [Azure Alerts에서 ITSM 작업 항목 만들기](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)

## <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager 웹앱 서비스 만들기 및 배포

Azure에서 ITSMC에 온-프레미스 Service Manager를 연결하기 위해 Microsoft는 GitHub에 Service Manager 웹앱을 만들었습니다.

Service Manager에 대해 ITSM 웹앱을 설정하려면 다음을 수행합니다.

- **웹앱 배포** – 웹앱을 배포하고, 속성을 설정하고, Azure AD를 사용하여 인증합니다. Microsoft에서 제공한 [자동화 스크립트](./itsmc-service-manager-script.md)를 사용하여 웹앱을 배포할 수 있습니다.
- **하이브리드 연결 구성** - 수동으로 [이 연결을 구성](#configure-the-hybrid-connection)합니다.

### <a name="deploy-the-web-app"></a>웹앱 배포
자동화 [스크립트](./itsmc-service-manager-script.md)를 사용하여 웹앱을 배포하고, 속성을 설정하고, Azure AD를 사용하여 인증합니다.

다음 필수 정보를 제공하여 스크립트를 실행합니다.

- Azure 구독 정보
- 리소스 그룹 이름
- 위치
- Service Manager 서버 세부 정보(서버 이름, 도메인, 사용자 이름 및 암호)
- 웹앱에 대한 사이트 이름 접두사
- ServiceBus 네임스페이스.

이 스크립트는 사용자가 지정한 이름(및 웹앱을 고유하게 만드는 몇 가지 추가 설정)을 사용하여 웹앱을 만듭니다. **웹앱 URL**, **클라이언트 ID** 및 **클라이언트 암호** 를 생성합니다.

값을 저장한 다음, ITSMC와의 연결을 만들 때 사용합니다.

**웹앱 설치 확인**

1. **Azure Portal** > **리소스** 로 이동합니다.
2. 웹앱을 선택하고 **설정** > **애플리케이션 설정** 을 클릭합니다.
3. 스크립트를 통해 앱을 배포할 때 제공한 Service Manager 인스턴스에 대한 정보를 확인합니다.

## <a name="configure-the-hybrid-connection"></a>하이브리드 연결 구성

다음 절차에 따라 Service Manager 인스턴스를 Azure의 ITSMC에 연결하는 하이브리드 연결을 구성합니다.

1. **Azure 리소스** 아래에서 Service Manager 웹앱을 찾습니다.
2. **설정** > **네트워킹** 을 클릭합니다.
3. **하이브리드 연결** 에서 **하이브리드 연결 엔드포인트 구성** 을 클릭합니다.

    ![하이브리드 연결 네트워킹](media/itsmc-connections-scsm/itsmc-hybrid-connection-networking-and-end-points.png)
4. **하이브리드 연결** 블레이드에서 **하이브리드 연결 추가** 를 클릭합니다.

    ![하이브리드 연결 추가](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-add.png)

5. **하이브리드 연결 추가** 블레이드에서 **새 하이브리드 연결 만들기** 를 클릭합니다.

    ![새 하이브리드 연결](media/itsmc-connections-scsm/itsmc-create-new-hybrid-connection.png)

6. 다음 값을 입력합니다.

   - **엔드포인트 이름**: 새 하이브리드 연결의 이름을 지정합니다.
   - **엔드포인트 호스트**: Service Manager 관리 서버의 FQDN입니다.
   - **엔드포인트 포트**: Type 5724
   - **Servicebus 네임스페이스**: 기존 Servicebus 네임스페이스를 사용하거나 새로 만듭니다.
   - **위치**: 위치를 선택합니다.
   - **Name**: Servicebus를 만드는 경우 이름을 지정합니다.

     ![하이브리드 연결 값](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-values.png)
6. **확인** 을 클릭하여 **하이브리드 연결 만들기** 블레이드를 닫고 하이브리드 연결을 만듭니다.

    하이브리드 연결이 만들어지면 블레이드 아래에 표시됩니다.

7. 하이브리드 연결을 만든 후 연결을 선택하고 **선택한 하이브리드 연결 추가** 를 클릭합니다.

    ![새 하이브리드 연결](media/itsmc-connections-scsm/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>수신기 설정 구성

다음 절차에 따라 하이브리드 연결에 대한 수신기 설정을 구성합니다.

1. **하이브리드 연결** 블레이드에서 **연결 관리자 다운로드** 를 클릭한 후 System Center Service Manager 인스턴스가 실행되고 있는 컴퓨터에 설치합니다.

    설치가 완료되면 **시작** 메뉴 아래에서 **하이브리드 연결 관리자 UI** 옵션을 사용할 수 있습니다.

2. **하이브리드 연결 관리자 UI** 를 클릭하면 Azure 자격 증명을 지정하라는 메시지가 표시됩니다.

3. Azure 자격 증명으로 로그인하고 하이브리드 연결이 만들어진 구독을 선택합니다.

4. **저장** 을 클릭합니다.

하이브리드 연결이 성공적으로 설정됩니다.

![하이브리드 연결 성공](media/itsmc-connections-scsm/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> 하이브리드 연결이 만들어진 후 배포된 Service Manager 웹앱을 방문하여 연결을 확인한 후 테스트합니다. Azure에서 ITSMC에 연결을 시도하기 전에 연결이 성공적으로 설정되었는지 확인합니다.

다음 샘플 이미지는 성공적으로 설정된 연결의 세부 정보를 보여줍니다.

![하이브리드 연결 테스트](media/itsmc-connections-scsm/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>다음 단계

* [ITSM 커넥터 개요](itsmc-overview.md)
* [Azure 경고에서 ITSM 작업 항목 만들기](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [ITSM 커넥터의 문제 해결](./itsmc-resync-servicenow.md)