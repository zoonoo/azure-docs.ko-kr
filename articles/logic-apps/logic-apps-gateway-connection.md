---
title: 온프레미스데이터 소스 액세스
description: Azure 온-프레미스 데이터 게이트웨이 리소스를 만들어 Azure Logic Apps의 온-프레미스 데이터 원본에 연결
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657128"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Azure Logic Apps에서 온-프레미스 데이터 원본에 연결

논리 앱에서 온-프레미스데이터 원본에 액세스하려면 로컬 [컴퓨터에 *온-프레미스 데이터 게이트웨이를* 설치한](../logic-apps/logic-apps-gateway-install.md)후 Azure 리소스를 만들어야 합니다. 그런 다음 논리 앱은 Azure Logic Apps에 사용할 수 있는 [온-프레미스 커넥터에서](../connectors/apis-list.md#on-premises-connectors) 제공하는 트리거 및 작업에서 이 Azure 게이트웨이 리소스를 사용합니다.

이 문서에서는 로컬 컴퓨터에서 이전에 설치된 [게이트웨이에](../logic-apps/logic-apps-gateway-install.md)대해 Azure 게이트웨이 리소스를 만드는 방법을 보여 줍니다. 게이트웨이에 대한 자세한 내용은 [게이트웨이 작동 방식을](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)참조하십시오.

> [!TIP]
> Azure Virtual Network에 연결하려면 대신 [*통합 서비스 환경*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 만드는 것을 고려합니다. 

다른 서비스에서 게이트웨이를 사용하는 방법에 대한 내용은 다음 문서를 참조하세요.

* [Microsoft Power, 온-프레미스 데이터 게이트웨이 자동화](/power-automate/gateway-reference)
* [Microsoft Power BI 온-프레미스 데이터 게이트웨이](/power-bi/service-gateway-onprem)
* [마이크로소프트 파워 애플 리 케이 션 온-프레미스 데이터 게이트웨이](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 온-프레미스 데이터 게이트웨이](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>지원되는 데이터 원본

Azure Logic Apps에서 온-프레미스 데이터 게이트웨이는 이러한 데이터 원본에 대한 [온-프레미스 커넥터를](../connectors/apis-list.md#on-premises-connectors) 지원합니다.

* BizTalk Server 2016
* 파일 시스템
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle 데이터베이스
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Azure Logic Apps는 데이터 게이트웨이를 통해 읽기 및 쓰기 작업을 지원합니다. 그러나 이러한 작업에는 [페이로드 크기에 제한이 있습니다.](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations) 게이트웨이 자체에 추가 비용이 발생하지 는 않지만 [논리 앱 가격 책정 모델은](../logic-apps/logic-apps-pricing.md) 이러한 커넥터 및 Azure Logic Apps의 다른 작업에 적용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* 로컬 [컴퓨터에 온-프레미스 데이터 게이트웨이를 이미 설치했습니다.](../logic-apps/logic-apps-gateway-install.md)

* 해당 데이터 게이트웨이를 설치할 때 사용된 [것과 동일한 Azure 계정 및 구독을](../logic-apps/logic-apps-gateway-install.md#requirements) 사용하고 있습니다. 이 Azure 계정은 단일 [Azure Active Directory(Azure AD) 테넌트 또는 디렉터리](../active-directory/fundamentals/active-directory-whatis.md#terminology)에 속해야 합니다.

* 게이트웨이 설치가 다른 Azure 게이트웨이 리소스에 의해 아직 등록되고 클레임되지 않았습니다.

  Azure 포털에서 게이트웨이 리소스를 만들 때 게이트웨이 리소스에만 연결되는 게이트웨이 설치를 선택합니다. Azure Logic Apps에서 온-프레미스 트리거 및 작업은 온-프레미스 데이터 원본에 연결하기 위해 게이트웨이 리소스를 사용합니다. 이러한 트리거 및 작업에서 Azure 구독 및 사용하려는 연결된 게이트웨이 리소스를 선택합니다. 각 게이트웨이 리소스는 하나의 게이트웨이 설치에만 연결되며, 이 설치는 하나의 Azure 계정에만 연결됩니다.

  > [!NOTE]
  > 게이트웨이 관리자만 Azure 포털에서 게이트웨이 리소스를 만들 수 있습니다. 현재 서비스 주체는 지원되지 않습니다. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Azure 게이트웨이 리소스 만들기

로컬 컴퓨터에 게이트웨이를 설치한 후 게이트웨이에 대한 Azure 리소스를 만듭니다.

1. 게이트웨이를 설치하는 데 사용된 것과 동일한 Azure 계정으로 [Azure 포털에](https://portal.azure.com) 로그인합니다.

1. Azure 포털 검색 상자에서 "온-프레미스 데이터 게이트웨이"를 입력하고 **온-프레미스 데이터 게이트웨이를 선택합니다.**

   !["온-프레미스 데이터 게이트웨이" 찾기](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. **온-프레미스 데이터 게이트웨이에서** **추가를**선택합니다.

   ![데이터 게이트웨이에 대한 새 Azure 리소스 추가](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. **연결 게이트웨이 만들기에서**게이트웨이 리소스에 대해 이 정보를 제공합니다. 완료되면 **만들기**를 선택합니다.

   | 속성 | 설명 |
   |----------|-------------|
   | **리소스 이름** | 문자, 숫자, 하이픈`-`(), 밑줄 (),`_`괄호 (,`(` `)`또는 마침표)만 포함하는`.`게이트웨이 리소스의 이름을 제공합니다. |
   | **구독** | 게이트웨이 설치에 사용된 Azure 계정의 Azure 구독을 선택합니다. 기본 구독은 로그인하는 데 사용한 Azure 계정을 기반으로 합니다. |
   | **리소스 그룹** | 사용하려는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md) |
   | **위치** | 게이트웨이 [설치](../logic-apps/logic-apps-gateway-install.md)중에 게이트웨이 클라우드 서비스에 대해 선택된 동일한 지역 또는 위치입니다. 그렇지 않으면 게이트웨이 설치가 설치 **이름** 목록에 나타나지 않습니다. 논리 앱 위치는 게이트웨이 리소스 위치와 다를 수 있습니다. |
   | **설치 이름** | 이러한 조건이 충족될 때만 목록에 표시되는 게이트웨이 설치를 선택합니다. <p><p>- 게이트웨이 설치는 만들려는 게이트웨이 리소스와 동일한 리전을 사용합니다. <br>- 게이트웨이 설치가 다른 Azure 게이트웨이 리소스에 연결되지 않습니다. <br>- 게이트웨이 설치는 게이트웨이 리소스를 만드는 데 사용하는 것과 동일한 Azure 계정에 연결됩니다. <br>- Azure 계정은 단일 [Azure Active Directory(Azure AD) 테넌트 또는 디렉터리에](../active-directory/fundamentals/active-directory-whatis.md#terminology) 속하며 게이트웨이 설치에 사용된 것과 동일한 계정입니다. <p><p>자세한 내용은 자주 [묻는 질문](#faq) 섹션을 참조하십시오. |
   |||

   다음은 게이트웨이 리소스와 동일한 지역에 있고 동일한 Azure 계정에 연결된 게이트웨이 설치를 보여 주는 예제입니다.

   ![데이터 게이트웨이 리소스를 만들기 위한 세부 정보 제공](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>온-프레미스 데이터 연결

게이트웨이 리소스를 만들고 이 리소스에 Azure 구독을 연결한 후에는 해당 게이트웨이 사용하여 논리 앱과 온-프레미스 데이터 원본 간에 연결을 만들 수 있습니다.

1. Azure Portal의 논리 앱 디자이너에서 논리 앱을 만들거나 엽니다.

1. 온-프레미스 연결을 지원하는 **SQL Server**와 같은 커넥터를 추가합니다.

1. **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택합니다.

1. **게이트웨이**에서 구독 목록에서 원하는 게이트웨이 리소스가 있는 Azure 구독을 **선택합니다.**

1. 선택한 구독에서 사용 가능한 게이트웨이 리소스를 보여 주는 **연결 게이트웨이** 목록에서 원하는 게이트웨이 리소스를 선택합니다. 각 게이트웨이 리소스는 단일 게이트웨이 설치에 연결됩니다.

   > [!NOTE]
   > 게이트웨이 목록에는 논리 앱의 위치가 게이트웨이 리소스의 위치와 다를 수 있으므로 다른 지역의 게이트웨이 리소스가 포함됩니다. 

1. 만들려는 연결에 따라 달라지도록 고유한 연결 이름 및 기타 필수 정보를 제공합니다.

   고유한 연결 이름을 사용하면 특히 여러 연결을 만드는 경우 나중에 해당 연결을 쉽게 찾을 수 있습니다. 해당하는 경우 사용자 이름의 정규화된 도메인도 포함됩니다.

   다음은 예제입니다.

   ![논리 앱과 데이터 게이트웨이 간에 연결 만들기](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 완료되면 **만들기**를 선택합니다.

게이트웨이 연결에 논리 앱을 사용할 준비가 되었습니다.

## <a name="edit-connection"></a>연결 편집

게이트웨이 연결에 대한 설정을 업데이트하려면 연결을 편집할 수 있습니다.

1. 로직 앱에 대한 모든 API 연결을 찾으려면 **개발 도구에서**논리 앱의 메뉴에서 **API 연결을**선택합니다.

   ![논리 앱 메뉴에서 "API 연결"을 선택합니다.](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. 원하는 게이트웨이 연결을 선택한 다음 **API 연결 편집을**선택합니다.

   > [!TIP]
   > 업데이트가 적용되지 않으면 게이트웨이 설치를 위해 [게이트웨이 Windows 서비스 계정을 중지하고 다시 시작해](../logic-apps/logic-apps-gateway-install.md#restart-gateway) 보십시오.

Azure 구독에 연결된 모든 API 연결을 찾으려면:

* Azure 포털 메뉴에서 **모든 서비스** > **웹** > **API 연결을**선택합니다.
* 또는 Azure 포털 메뉴에서 **모든 리소스를**선택합니다. **형식** 필터를 **API 연결로**설정합니다.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>게이트웨이 리소스 삭제

다른 게이트웨이 리소스를 만들려면 게이트웨이 설치를 다른 게이트웨이 리소스에 연결하거나 게이트웨이 리소스를 제거하려면 게이트웨이 설치에 영향을 주지 않고 게이트웨이 리소스를 삭제할 수 있습니다.

1. Azure 포털 메뉴에서 **모든 리소스를**선택하거나 모든 페이지에서 **모든 리소스를** 검색하고 선택합니다. 게이트웨이 리소스를 찾아 선택합니다.

1. 게이트웨이 리소스 메뉴에서 아직 선택하지 않은 경우 **온-프레미스 데이터 게이트웨이**를 선택합니다. 게이트웨이 리소스 도구 모음에서 **삭제를**선택합니다.

   다음은 그 예입니다.

   ![Azure에서 게이트웨이 리소스 삭제](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>질문과 대답

**Q:** Azure에서 게이트웨이 리소스를 만들 때 게이트웨이 설치가 나타나지 않는 이유는 무엇입니까? <br/>
**A**: 이 문제는 다음과 같은 이유 때문에 발생할 수 있습니다.

* Azure 계정은 로컬 컴퓨터의 게이트웨이 설치에 연결된 계정과 동일한 계정이어야 합니다. 게이트웨이 설치에 연결된 ID와 동일한 ID로 Azure 포털에 로그인되어 있는지 확인합니다. 또한 Azure 계정이 단일 [Azure AD 테넌트 또는 디렉터리에](../active-directory/fundamentals/active-directory-whatis.md#terminology) 속하고 게이트웨이 설치 중에 사용된 것과 동일한 Azure AD 테넌트 또는 디렉터리로 설정되어 있는지 확인합니다.

* 게이트웨이 리소스 및 게이트웨이 설치는 동일한 리전을 사용해야 합니다. 그러나 논리 앱 위치는 게이트웨이 리소스 위치와 다를 수 있습니다.

* 게이트웨이 설치가 이미 등록되어 있고 다른 게이트웨이 리소스에 의해 클레임됩니다. 이러한 설치는 **설치 이름** 목록에 나타나지 않습니다. Azure 포털에서 게이트웨이 등록을 검토하려면 모든 Azure 구독에서 **온-프레미스 데이터 게이트웨이** 유형이 있는 *모든* Azure 리소스를 찾습니다. 다른 게이트웨이 리소스에서 게이트웨이 설치의 연결을 해제하려면 [게이트웨이 리소스 삭제](#change-delete-gateway-resource)를 참조하십시오.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>다음 단계

* [논리 앱 보안](./logic-apps-securing-a-logic-app.md)
* [논리 앱에 대한 일반적인 예제 및 시나리오](./logic-apps-examples-and-scenarios.md)
