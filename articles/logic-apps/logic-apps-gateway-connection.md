---
title: "온-프레미스 데이터에 액세스 - Azure Logic Apps | Microsoft Docs"
description: "온-프레미스 데이터 게이트웨이를 연결하여 논리 앱이 온-프레미스 데이터에 액세스할 수 있는 방법입니다."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: ef7df25d8080cae41235dffb287906508d4a652d
ms.lasthandoff: 03/30/2017


---
# <a name="connect-to-on-premises-data-from-logic-apps"></a>논리 앱에서 온-프레미스 데이터에 연결

온-프레미스 데이터에 액세스하려면 지원되는 Azure Logic Apps 커넥터의 온-프레미스 데이터 게이트웨이에 대한 연결을 설정할 수 있습니다. 다음 단계에서는 논리 앱에서 작동하도록 온-프레미스 데이터 게이트웨이를 설치 및 설정하는 방법을 설명합니다.
온-프레미스 데이터 게이트웨이는 다음과 같은 데이터 원본 연결을 지원합니다.

*   BizTalk Server
*    DB2  
*   파일 시스템
*   Informix
*   MQ
*    Oracle 데이터베이스 
*   SAP 응용 프로그램 서버 
*   SAP 메시지 서버
*    SQL Server

이러한 연결에 대한 자세한 내용은 [Azure Logic Apps에 대한 커넥터](https://docs.microsoft.com/azure/connectors/apis-list)를 참조하세요.

## <a name="requirements"></a>요구 사항

* Azure에서 온-프레미스 데이터 게이트웨이를 계정(Azure Active Directory 기반 계정)으로 연결할 회사 또는 학교 전자 메일 주소가 있어야 합니다.

* Microsoft 계정(예: @outlook.com)을 사용하는 경우 Azure 계정을 사용하여 [회사 또는 학교 전자 메일 주소를 만들](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal) 수 있습니다.

* [온-프레미스 데이터 게이트웨이가 로컬 컴퓨터에 설치](logic-apps-gateway-install.md)되어 있어야 합니다.

* 하나의 게이트웨이 리소스에만 설치를 연결할 수 있습니다. 게이트웨이는 다른 Azure 온-프레미스 데이터 게이트웨이에서 사용할 수 없습니다. 클레임은 ([이 항목의 2단계 중 만들기](#2-create-an-azure-on-premises-data-gateway-resource))에서 발생합니다.

## <a name="install-and-configure-the-connection"></a>연결 설치 및 구성

### <a name="1-install-the-on-premises-data-gateway"></a>1. 온-프레미스 데이터 게이트웨이 설치

아직 수행하지 않은 경우 다음 단계에 따라 [온-프레미스 데이터 게이트웨이를 설치](logic-apps-gateway-install.md)합니다. 다른 단계를 계속하기 전에 온-프레미스 컴퓨터에 데이터 게이트웨이를 설치했는지 확인합니다.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Azure 온-프레미스 데이터 게이트웨이 리소스 만들기

게이트웨이를 설치한 후에 Azure 구독을 게이트웨이에 연결해야 합니다.

> [!IMPORTANT] 
> 게이트웨이 리소스가 논리 앱과 동일한 Azure 지역에 만들어졌는지 확인합니다. 동일한 지역에 배포하지 않은 경우 논리 앱에서 액세스할 수 없습니다. 
> 

1. 게이트웨이 설치 중에 사용된 동일한 회사 또는 학교 전자 메일 주소를 사용하여 Azure에 로그인합니다.
2. **새로 만들기**를 선택합니다.
3. **온-프레미스 데이터 게이트웨이**를 찾아 선택합니다.
4. 게이트웨이를 계정과 연결하려면 정보를 모두 기입합니다. 여기에는 적합한 **설치 이름**을 선택하는 것도 포함됩니다.
   
    ![온-프레미스 데이터 게이트웨이 연결][1]

5. 리소스 그룹을 만들려면 **만들기**를 클릭합니다.

### <a name="3-create-a-logic-app-connection-in-logic-app-designer"></a>3. Logic App Designer에서 논리 앱 연결 만들기

Azure 구독이 온-프레미스 데이터 게이트웨이 인스턴스와 연결되었으므로 논리 앱에서 게이트웨이에 연결할 수 있습니다.

1. 논리 앱을 열고 온-프레미스 연결을 지원하는 SQL Server와 같은 커넥터를 선택합니다.
2. **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택합니다.
   
    ![논리 앱 디자이너 게이트웨이 만들기][2]

3. 연결하려는 **게이트웨이**를 선택하고 기타 필수 연결 정보를 완료합니다.
4. 연결을 만들려면 **만들기**를 클릭합니다.

이제 논리 앱에 사용할 연결을 구성합니다.

## <a name="data-gateway-connection-modifications"></a>데이터 게이트웨이 연결 수정
논리 앱에 데이터 게이트웨이 연결을 추가하면 해당 연결에 고유한 설정을 조정하도록 수정해야 합니다. 두 위치 중 한 곳에서 연결을 찾을 수 있습니다.
* 논리 앱에 대한 주요 블레이드에 개발 도구 섹션의 API 연결에 대한 패널이 표시됩니다. 이를 선택하면 논리 앱과 연결된 모든 API 연결이 표시됩니다. 이 중 하나는 데이터 게이트웨이 연결이 됩니다. 이를 선택하면 연결과 관련된 설정을 보고 수정할 수 있습니다.
* API 연결을 선택하면 주 블레이드는 구독에 모든 API 연결을 표시합니다. 여기에서 목록은 데이터 게이트웨이 연결이 됩니다. 이를 선택하면 연결과 관련된 설정을 보고 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [논리 앱에 대한 일반적인 예제 및 시나리오](../logic-apps/logic-apps-examples-and-scenarios.md)
* [엔터프라이즈 통합 기능](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png

