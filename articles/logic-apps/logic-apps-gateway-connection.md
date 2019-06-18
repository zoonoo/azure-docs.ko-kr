---
title: Azure Logic Apps에서 온-프레미스 데이터 원본에 액세스 | Microsoft Docs
description: 온-프레미스 데이터 게이트웨이를 만들어 논리 앱에서 온-프레미스 데이터 원본에 연결
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 0580fe09c2cb6569724a9b4365233a3142645a47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546269"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Azure Logic Apps에서 온-프레미스 데이터 원본에 연결

논리 앱에서 온-프레미스 데이터 원본에 액세스하려면 Azure Portal에서 온-프레미스 데이터 게이트웨이 리소스를 만듭니다. 그러면 논리 앱은 [온-프레미스 커넥터](../logic-apps/logic-apps-gateway-install.md#supported-connections)를 사용할 수 있습니다. 이 문서에서는 [로컬 컴퓨터에서 게이트웨이를 다운로드하여 설치](../logic-apps/logic-apps-gateway-install.md)한 *후* Azure 게이트웨이 리소스를 만드는 방법을 보여 줍니다. 

> [!TIP]
> Azure Virtual Network에 연결하려면 대신 [*통합 서비스 환경*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)을 만드는 것을 고려합니다. 

다른 서비스에서 게이트웨이를 사용하는 방법에 대한 내용은 다음 문서를 참조하세요.

* [Microsoft Power BI 온-프레미스 데이터 게이트웨이](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow 온-프레미스 데이터 게이트웨이](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps 온-프레미스 데이터 게이트웨이](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services 온-프레미스 데이터 게이트웨이](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>필수 조건

* [로컬 컴퓨터에 데이터 게이트웨이를 다운로드하고 설치](../logic-apps/logic-apps-gateway-install.md)했어야 합니다.

* 게이트웨이 설치가 Azure의 게이트웨이 리소스에 아직 연결되어 있지 않아야 합니다. 게이트웨이 설치는 하나의 게이트웨이 리소스에만 연결할 수 있으며 이러한 연결은 게이트웨이 리소스를 만들고 게이트웨이 설치를 선택한 경우에만 수행됩니다. 이렇게 연결되면 다른 리소스에서는 해당 게이트웨이 설치를 사용할 수 없습니다.

* Azure Portal에 로그인하고 게이트웨이 리소스를 만들 때는 게이트웨이를 설치하는 데 사용한 것과 동일한 [Auzre 구독](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access)과 함께 이전에 [온-프레미스 데이터 게이트웨이를 설치](../logic-apps/logic-apps-gateway-install.md#requirements)할 때 사용한 것과 동일한 로그인 계정을 사용해야 합니다. 아직 Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* Azure Portal에서 게이트웨이 리소스를 만들고 유지 관리하려면 [Windows 서비스 계정](../logic-apps/logic-apps-gateway-install.md#windows-service-account)에 적어도 **기여자** 권한이 있어야 합니다. 온-프레미스 데이터 게이트웨이는 Windows 서비스로 실행되고 Windows 서비스 로그인 자격 증명에 `NT SERVICE\PBIEgwService`를 사용하도록 설정됩니다. 

  > [!NOTE]
  > Windows 서비스 계정은 온-프레미스 데이터 원본에 연결하는 데 사용되는 동일한 계정 및 클라우드 서비스에 로그인하는 데 사용되는 Azure 회사 또는 학교 계정과 다릅니다.

## <a name="download-and-install-gateway"></a>게이트웨이 다운로드 및 설치

이 문서의 단계를 계속 진행하려면 로컬 컴퓨터에 게이트웨이가 이미 설치되어 있는지 확인합니다.
아직 수행하지 않은 경우 [온-프레미스 데이터 게이트웨이를 다운로드하고 설치하는 단계](../logic-apps/logic-apps-gateway-install.md)에 따르세요. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>게이트웨이에 대한 Azure 리소스 만들기

로컬 컴퓨터에 게이트웨이를 설치한 후에 게이트웨이에 대한 Azure 리소스를 만들 수 있습니다. 또한 이 단계는 Azure 구독과 게이트웨이 리소스를 연결합니다.

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다. 게이트웨이를 설치하는 데 동일한 Azure 회사 또는 학교 이메일 주소를 사용해야 합니다.

2. 기본 Azure 메뉴에서 **리소스 만들기** > 
**통합** > **온-프레미스 데이터 게이트웨이**를 선택합니다.

   !["온-프레미스 데이터 게이트웨이" 찾기](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. **연결 게이트웨이 만들기** 페이지에서 게이트웨이 리소스에 대해 다음 정보를 제공합니다.

   | 자산 | 설명 | 
   |----------|-------------|
   | **Name** | 게이트웨이 리소스의 이름입니다. | 
   | **구독** | 논리 앱과 동일한 구독이어야 하는 Azure 구독의 이름입니다. 기본 구독은 로그인하는 데 사용한 Azure 계정을 기준으로 합니다. | 
   | **리소스 그룹** | 관련 리소스를 구성하기 위한 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)의 이름입니다. | 
   | **Location**: | Azure에서는 이 위치를 [게이트웨이 설치](../logic-apps/logic-apps-gateway-install.md) 중에 게이트웨이 클라우드 서비스에 대해 선택한 동일한 지역으로 제한합니다. <p>**참고**: 이 게이트웨이 리소스 위치가 게이트웨이 클라우드 서비스 위치와 일치하는지 확인합니다. 그렇지 않은 경우 게이트웨이 설치는 다음 단계에서 선택하도록 설치된 게이트웨이 목록에 나타나지 않을 수 있습니다. 게이트웨이 리소스 및 논리 앱에 서로 다른 지역을 사용할 수 있습니다. | 
   | **설치 이름** | 게이트웨이 설치가 선택되어 있지 않으면 이전에 설치한 게이트웨이를 선택합니다. | 
   | | | 

   다음은 예제입니다.

   ![온-프레미스 데이터 게이트웨이를 만들기 위해 세부 정보 제공](./media/logic-apps-gateway-connection/createblade.png)

4. 게이트웨이 리소스를 Azure 대시보드에 추가하려면 **대시보드에 고정**을 선택합니다. 작업을 완료하면 **만들기**를 선택합니다.

   언제든지 게이트웨이를 찾거나 확인하려면 기본 Azure 메뉴에서 **모든 서비스**를 선택합니다. 
   검색 상자에 “온-프레미스 데이터 게이트웨이”를 입력한 다음, **온-프레미스 데이터 게이트웨이**를 선택합니다.

   ![“온-프레미스 데이터 게이트웨이” 찾기](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>온-프레미스 데이터 연결

게이트웨이 리소스를 만들고 이 리소스에 Azure 구독을 연결한 후에는 해당 게이트웨이 사용하여 논리 앱과 온-프레미스 데이터 원본 간에 연결을 만들 수 있습니다.

1. Azure Portal의 논리 앱 디자이너에서 논리 앱을 만들거나 엽니다.

2. 온-프레미스 연결을 지원하는 **SQL Server**와 같은 커넥터를 추가합니다.

3. 이제 연결을 설정합니다.

   1. **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택합니다. 

   2. **게이트웨이**에 대해 이전에 만든 게이트웨이 리소스를 선택합니다. 

      게이트웨이 연결 위치는 논리 앱과 동일한 지역에 있어야 하지만 다른 지역의 게이트웨이를 선택할 수 있습니다.

   3. 고유한 연결 이름 및 기타 필요한 정보를 제공합니다. 

      고유한 연결 이름을 통해 나중에, 특히 여러 연결을 만들 때 해당 연결을 쉽게 식별할 수 있습니다. 해당하는 경우 사용자 이름의 정규화된 도메인도 포함됩니다.
   
      다음은 예제입니다.

      ![논리 앱과 데이터 게이트웨이 간에 연결 만들기](./media/logic-apps-gateway-connection/blankconnection.png)

   4. 작업을 완료하면 **만들기**를 선택합니다. 

게이트웨이 연결에 논리 앱을 사용할 준비가 되었습니다.

## <a name="edit-connection"></a>연결 편집

논리 앱에 게이트웨이를 연결한 후에 나중에 해당 특정 연결에 대한 설정을 업데이트하는 것이 좋습니다.

1. 다음과 같이 게이트웨이 연결을 찾습니다.

   * 사용자의 논리 앱에 대한 모든 API 연결만 찾으려면 **개발 도구**에서 **API 연결**을 선택합니다. 
   
     ![논리 앱으로 이동, "API 연결" 선택](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Azure 구독에 연결된 모든 API 연결을 찾으려면: 

     * 기본 Azure 메뉴에서 **모든 서비스** > **웹** > **API 연결**로 이동합니다. 
     * 또는 기본 Azure 메뉴에서 **모든 리소스**로 이동합니다.

2. 원하는 게이트웨이 연결을 선택하고 **API 연결 편집**을 선택합니다.

   > [!TIP]
   > 업데이트가 적용되지 않더라도 [게이트웨이 Windows 서비스를 중지 및 다시 시작](./logic-apps-gateway-install.md#restart-gateway)합니다.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>게이트웨이 리소스 삭제

다른 게이트웨이 리소스를 만들거나 다른 리소스와 게이트웨이를 연결하거나 게이트웨이 리소스를 제거하려면 게이트웨이 설치에 영향을 주지 않고 게이트웨이 리소스를 삭제할 수 있습니다. 

1. 기본 Azure 메뉴에서 **모든 리소스**로 이동합니다. 

2. 게이트웨이 리소스를 찾아 선택합니다.

3. 게이트웨이 리소스 메뉴에서 아직 선택하지 않은 경우 **온-프레미스 데이터 게이트웨이**를 선택합니다. 

4. 리소스 도구 모음에서 **삭제**를 선택합니다.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>질문과 대답

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [논리 앱 보안](./logic-apps-securing-a-logic-app.md)
* [논리 앱에 대한 일반적인 예제 및 시나리오](./logic-apps-examples-and-scenarios.md)
