---
title: SAP 시스템에 연결
description: Azure Logic Apps로 워크플로를 자동화 하 여 SAP 리소스 액세스 및 관리
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 03/08/2021
tags: connectors
ms.openlocfilehash: b9238d099c7b33e904c2fc8de3c4fc08369f1f36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489840"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps에서 SAP 시스템에 연결

이 문서에서는 [sap 커넥터](/connectors/sap/)를 사용 하 여 LOGIC APPS에서 sap 리소스에 액세스 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* SAP 리소스에 액세스 하려는 논리 앱입니다. Logic Apps를 처음 접하는 경우 [Azure Portal에서 첫 번째 논리 앱을 만들기 위한](../logic-apps/quickstart-create-first-logic-app-workflow.md) [Logic Apps 서비스 개요](../logic-apps/logic-apps-overview.md) 및 빠른 시작을 참조 하세요.

    * 더 이상 사용 되지 않는 이전 버전의 SAP 커넥터를 사용한 경우에는 SAP 서버에 연결 하기 전에 [현재 커넥터로 마이그레이션해야](#migrate-to-current-connector) 합니다.

    * 다중 테 넌 트 Azure에서 논리 앱을 실행 하는 경우 [다중 테 넌 트 필수 구성 요소](#multi-tenant-azure-prerequisites)를 참조 하세요.

    * 프리미엄 수준 [ise (integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 논리 앱을 실행 하는 경우 [ise 필수 구성 요소](#ise-prerequisites)를 참조 하세요.

* Logic Apps에서 액세스 하려는 [sap 응용 프로그램 서버](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) 또는 [sap 메시지 서버](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) 입니다. 커넥터에서 사용할 수 있는 SAP 서버 및 SAP 작업에 대 한 자세한 내용은 [sap compatibility](#sap-compatibility)를 참조 하세요.

* 샘플 IDoc 파일과 같이 SAP 서버에 보낼 메시지 내용입니다. 이 콘텐츠는 XML 형식 이어야 하며 사용 하려는 SAP 작업의 네임 스페이스를 포함 해야 합니다. [XML 봉투 (envelope)로 래핑하여 플랫 파일 스키마를 사용 하 여 IDocs를 보낼](#send-flat-file-idocs)수 있습니다.

* **SAP 트리거에서 메시지를 받을 때** 를 사용 하려면 다음을 수행 해야 합니다.

    * 이 설정을 사용 하 여 SAP gateway 보안 권한을 설정 합니다. `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

    * SAP gateway 보안 로깅을 설정 하 여 ACL (Access Control 목록)을 찾을 수 있습니다. 자세한 내용은 [게이트웨이 로깅 설정에 대 한 SAP 도움말 항목](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)을 참조 하세요. 그렇지 않으면 다음과 같은 오류가 표시 됩니다. `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    > [!NOTE]
    > 이 트리거는 동일한 URI 위치를 사용 하 여 webhook 구독을 갱신 하 고 구독 취소 합니다. 갱신 작업은 HTTP 메서드를 사용 하는 `PATCH` 반면, 구독 취소 작업은 http 메서드를 사용 합니다 `DELETE` . 이 동작으로 인해 갱신 작업이 트리거 기록에 구독 취소 작업으로 표시 될 수 있지만 트리거는가 `PATCH` 아닌 HTTP 메서드로를 사용 하기 때문에 작업이 여전히 갱신 됩니다 `DELETE` .

### <a name="sap-compatibility"></a>SAP 호환성

SAP 커넥터는 다음과 같은 유형의 SAP 시스템과 호환 됩니다.

* S/4 HANA와 같은 온-프레미스 및 클라우드 기반 HANA 기반 SAP 시스템

* R/3 및 ECC와 같은 클래식 온-프레미스 SAP 시스템

SAP 커넥터는 SAP NetWeaver 기반 시스템에서 다음 메시지 및 데이터 통합 유형을 지원 합니다.

* 중간 문서 (IDoc)

* BAPI (비즈니스 응용 프로그램 프로그래밍 인터페이스)

* RFC (원격 함수 호출) 및 트랜잭션 RFC (tRFC)

SAP 커넥터는 [NCo (sap .Net connector) 라이브러리](https://support.sap.com/en/product/connectors/msnet.html)를 사용 합니다. 커넥터에서 다음 SAP 작업 및 트리거를 사용할 수 있습니다.

* **SAP로 메시지** 를 보내 [trfc 작업을 통해 idocs를 보냅니다](#send-idoc-action) .이 작업은 다음 작업을 수행 하는 데 사용할 수 있습니다.

    * [RFC를 통해 BAPI 함수를 호출 합니다.](#call-bapi-action)

    * SAP 시스템에서 RFC/tRFC 호출

    * 상태 저장 세션 만들기 또는 닫기

    * BAPI 트랜잭션 커밋 또는 롤백

    * 트랜잭션 식별자 확인

    * IDocs를 보내고, 해당 번호에서 Idocs의 상태를 가져오고, 트랜잭션에 대 한 IDocs 목록을 가져옵니다.

    * SAP 테이블 읽기

* **SAP 트리거에서 메시지가 수신 되 면** 다음 작업에 사용할 수 있습니다.

    * TRFC를 통해 IDocs 받기

    * TRFC를 통해 BAPI 함수를 호출 합니다.

    * SAP 시스템에서 RFC/tRFC 호출

* **스키마 생성** 작업-IDOC, BAPI 또는 RFC에 대 한 SAP 아티팩트의 스키마를 생성 하는 데 사용할 수 있습니다.

이러한 SAP 작업을 사용 하려면 먼저 사용자 이름 및 암호를 사용 하 여 연결을 인증 해야 합니다. SAP 커넥터는 [SNC (보안 네트워크 통신)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)도 지원 합니다. SNC는 SAP NetWeaver Single Sign-On (SSO) 또는 외부 제품의 추가 보안 기능에 사용할 수 있습니다. SNC를 사용 하는 경우 [snc 필수 구성 요소](#snc-prerequisites)를 참조 하세요.

### <a name="migrate-to-current-connector"></a>현재 커넥터로 마이그레이션

이전 SAP 응용 프로그램 서버 및 SAP 메시지 서버 커넥터는 2020 년 2 월 29 일에 사용 되지 않습니다. 현재 SAP 커넥터로 마이그레이션하려면 다음 단계를 수행 합니다.

1. [온-프레미스 데이터 게이트웨이](https://www.microsoft.com/download/details.aspx?id=53127) 를 현재 버전으로 업데이트 합니다. 자세한 내용은 [Azure Logic Apps에 대 한 온-프레미스 데이터 게이트웨이 설치](../logic-apps/logic-apps-gateway-install.md)를 참조 하세요.

1. 사용 되지 않는 SAP 커넥터를 사용 하는 논리 앱에서 **sap로 보내기** 작업을 삭제 합니다.

1. 현재 SAP 커넥터에서 **sap로 메시지 보내기** 작업을 추가 합니다.

1. 새 작업에서 SAP 시스템에 다시 연결 합니다.

1. 논리 앱을 저장합니다.

### <a name="multi-tenant-azure-prerequisites"></a>다중 테넌트 Azure 필수 구성 요소

이러한 필수 구성 요소는 논리 앱이 다중 테 넌 트 Azure에서 실행 되는 경우에 적용 됩니다. 관리 되는 SAP 커넥터는 [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 기본적으로 실행 되지 않습니다.

> [!TIP]
> 프리미엄 수준 ISE를 사용 하는 경우 관리 되는 SAP 커넥터 대신 SAP ISE 커넥터를 사용할 수 있습니다. 자세한 내용은 [ISE 필수 구성 요소](#ise-prerequisites)를 참조 하세요.

관리 되는 SAP 커넥터는 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 통해 sap 시스템에 통합 됩니다. 예를 들어 메시지 보내기 시나리오에서 논리 앱에서 SAP 시스템으로 메시지를 보내는 경우 데이터 게이트웨이는 RFC 클라이언트 역할을 하 고 논리 앱에서 받은 요청을 SAP로 전달 합니다. 마찬가지로 메시지 수신 시나리오에서 데이터 게이트웨이는 SAP에서 요청을 수신 하 고 논리 앱에 전달 하는 RFC 서버 역할을 합니다.

* [온-프레미스 데이터 게이트웨이를 다운로드 하](../logic-apps/logic-apps-gateway-install.md) 여 연결 하는 SAP 시스템과 동일한 가상 네트워크에 있는 호스트 컴퓨터 또는 가상 컴퓨터에 설치 합니다.

* Azure Portal에서 온-프레미스 데이터 게이트웨이에 대 한 [Azure 게이트웨이 리소스를 만듭니다](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) . 이 게이트웨이를 사용 하면 온-프레미스 데이터 및 리소스에 안전 하 게 액세스할 수 있습니다. 지원 되는 게이트웨이 버전을 사용 해야 합니다.

    * 게이트웨이에 문제가 발생 한 경우 [최신 버전으로 업그레이드](https://aka.ms/on-premises-data-gateway-installer)해 보세요. 여기에는 문제를 해결 하기 위한 업데이트가 포함 될 수 있습니다.

* 온-프레미스 데이터 게이트웨이와 동일한 로컬 컴퓨터에서 [최신 SAP 클라이언트 라이브러리를 다운로드 하 여 설치](#sap-client-library-prerequisites) 합니다.

### <a name="ise-prerequisites"></a>ISE 필수 조건

이러한 필수 구성 요소는 프리미엄 수준 ISE에서 논리 앱을 실행 하는 경우에 적용 됩니다. 그러나 개발자 수준 ISE에서 실행 되는 논리 앱에는 적용 되지 않습니다. ISE는 Azure virtual network에 의해 보호 되는 리소스에 대 한 액세스를 제공 하 고, 논리 앱이 온-프레미스 데이터 게이트웨이를 사용 하지 않고 온-프레미스 리소스에 직접 액세스할 수 있도록 하는 다른 ISE 네이티브 커넥터를 제공 합니다.

> [!NOTE]
> SAP ISE 커넥터가 개발자 수준 ISE 내에서 표시 되는 동안에는 커넥터를 설치 하려는 시도가 실패 합니다.

1. Blob 컨테이너를 포함 하는 Azure Storage 계정이 아직 없는 경우 [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) 또는 [Azure Storage 탐색기](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)를 사용 하 여 컨테이너를 만듭니다.

1. 로컬 컴퓨터에서 [최신 SAP 클라이언트 라이브러리를 다운로드 하 여 설치](#sap-client-library-prerequisites) 합니다. 다음 어셈블리 파일이 있어야 합니다.

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. 이러한 어셈블리 파일이 포함 된 .zip 파일을 만듭니다. Azure Storage의 blob 컨테이너에 패키지를 업로드 합니다.

1. Azure Portal 또는 Azure Storage 탐색기에서 .zip 파일을 업로드 한 컨테이너 위치로 이동 합니다.

1. 컨테이너 위치의 URL을 복사 합니다. Sas 토큰에 대 한 권한이 부여 되도록 SAS (공유 액세스 서명) 토큰을 포함 해야 합니다. 그렇지 않으면 SAP ISE 커넥터에 대 한 배포가 실패 합니다.

1. ISE에서 SAP 커넥터를 설치 하 고 배포 합니다. 자세한 내용은 [ISE 커넥터 추가](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)를 참조 하세요.

   1. [Azure Portal](https://portal.azure.com)에서 ISE를 찾아 엽니다.

   1. ISE 메뉴에서 **관리 되는 커넥터** &gt; **추가** 를 선택 합니다. 커넥터 목록에서 **SAP** 를 찾아 선택 합니다.

   1. **새 관리 커넥터 추가** 창의 **sap 패키지** 상자에 sap 어셈블리를 포함 하는 .zip 파일의 URL을 붙여넣습니다. SAS 토큰을 포함 해야 합니다.
 
  1. **만들기** 를 선택 하 여 ISE 커넥터 만들기를 완료 합니다.

1. SAP 인스턴스와 ISE가 서로 다른 가상 네트워크에 있는 경우 연결 되도록 [해당 네트워크를 피어](../virtual-network/tutorial-connect-virtual-networks-portal.md) 링 해야 합니다.

### <a name="sap-client-library-prerequisites"></a>SAP 클라이언트 라이브러리 필수 구성 요소

커넥터에서 사용 중인 SAP 클라이언트 라이브러리의 필수 구성 요소입니다.

* [.NET Framework 4.0-Windows 64 비트 (x64)를 사용 하 여 컴파일된 Microsoft .NET 3.0.22.0에 대 한 최신 버전인 SAP Connector (NCo 3.0)](https://support.sap.com/en/product/connectors/msnet.html)를 설치 해야 합니다. 이전 버전의 SAP NCo는 동시에 두 개 이상의 IDoc 메시지를 보낼 때 문제가 발생할 수 있습니다. 이 조건은 SAP 대상으로 전송 되는 이후의 모든 메시지를 차단 하므로 메시지의 시간 제한이 초과 됩니다.

* 데이터 게이트웨이는 64 비트 시스템 에서만 실행 되므로 64 비트 버전의 SAP 클라이언트 라이브러리가 설치 되어 있어야 합니다. 지원 되지 않는 32 비트 버전을 설치 하면 "잘못 된 이미지" 오류가 발생 합니다.

* 다음과 같이 사용자의 시나리오에 따라 기본 설치 폴더의 어셈블리 파일을 다른 위치로 복사 합니다.

    * ISE에서 실행 되는 논리 앱의 경우에는 [ise 전제 조건을](#ise-prerequisites) 대신 따르세요.

    * 다중 테 넌 트 Azure에서 실행 되는 논리 앱 및 온-프레미스 데이터 게이트웨이를 사용 하는 경우 어셈블리 파일을 데이터 게이트웨이 설치 폴더에 복사 합니다. 

        
        * SAP 연결에 실패 하 고 오류 메시지가 표시 되 면 **계정 정보 및/또는 사용 권한을 확인 하 고 다시 시도 하세요**. 그러면 어셈블리 파일이 data gateway 설치 폴더에 복사 되었는지 확인 합니다.
        
        * [.Net 어셈블리 바인딩 로그 뷰어](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)를 사용 하 여 추가 문제를 해결 합니다. 이 도구를 사용 하 여 어셈블리 파일이 올바른 위치에 있는지 확인할 수 있습니다. 
        
        * 필요에 따라 SAP 클라이언트 라이브러리를 설치할 때 **전역 어셈블리 캐시 등록** 옵션을 선택 합니다.

SAP 클라이언트 라이브러리, .NET Framework, .NET 런타임 및 게이트웨이 간의 다음 관계를 확인 합니다.

* Microsoft SAP 어댑터와 게이트웨이 호스트 서비스는 모두 .NET Framework 4.7.2를 사용 합니다.

* .NET Framework 4.0에 대 한 SAP NCo는 .NET runtime 4.0을 사용 하는 프로세스에서 4.8로 작동 합니다.

* .NET Framework 2.0에 대 한 SAP NCo는 .NET runtime 2.0을 3.5에 사용 하는 프로세스와 함께 작동 하지만 더 이상 최신 게이트웨이로 작동 하지 않습니다.

### <a name="snc-prerequisites"></a>SNC 필수 조건

다중 테 넌 트 Azure 에서만 지원 되는 선택적 SNC와 함께 온-프레미스 데이터 게이트웨이를 사용 하는 경우 이러한 추가 설정을 구성 해야 합니다.

SSO와 함께 SNC를 사용 하는 경우 SAP 사용자에 대해 매핑된 사용자로 데이터 게이트웨이 서비스가 실행 되 고 있는지 확인 합니다. 기본 계정을 변경 하려면 **계정 변경** 을 선택 하 고 사용자 자격 증명을 입력 합니다.

![선택 된 게이트웨이 서비스 계정을 변경 하는 단추가 있는 서비스 설정 페이지를 표시 하는 Azure Portal의 온-프레미스 데이터 게이트웨이 설정 스크린샷](./media/logic-apps-using-sap-connector/gateway-account.png)

외부 보안 제품을 통해 SNC를 사용 하도록 설정 하는 경우 데이터 게이트웨이가 설치 된 컴퓨터와 동일한 컴퓨터에 SNC 라이브러리를 복사 합니다. SNC 제품의 예로는 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS 및 NTLM이 있습니다. 데이터 게이트웨이에 대해 SNC를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [보안 네트워크 통신 사용](#enable-secure-network-communications)을 참조 하세요.

## <a name="send-idoc-messages-to-sap-server"></a>SAP 서버에 IDoc 메시지 보내기

다음 예제를 수행 하 여 IDoc 메시지를 SAP 서버에 보내고 응답을 반환 하는 논리 앱을 만듭니다.

1. [HTTP 요청에 의해 트리거되는 논리 앱을 만듭니다.](#create-http-request-trigger)

1. [SAP로 메시지를 보내기 위해 워크플로에 작업을 만듭니다.](#create-sap-action-to-send-message)

1. [워크플로에서 HTTP 응답 작업을 만듭니다.](#create-http-response-action)

1. [RFC를 사용 하 여 SAP ABAP에서 회신을 받는 경우에는 RFC (원격 함수 호출) 요청-응답 패턴을 만듭니다.](#create-rfc-request-response)

1. [논리 앱을 테스트 합니다.](#test-logic-app)

### <a name="create-http-request-trigger"></a>HTTP 요청 트리거 만들기

> [!NOTE]
> 논리 앱이 SAP에서 IDocs를 수신 하면 [요청 트리거](../connectors/connectors-native-reqres.md) 는 이제 SAP 일반 XML 형식을 지원 합니다. IDocs를 일반 XML로 받으려면 **SAP에서 메시지를 받을 때** 트리거를 사용 합니다. **Idoc 형식의** 매개 변수를 **SapPlainXml** 로 설정 합니다.

먼저 논리 앱에 *HTTP POST* 요청을 보내기 위해 Azure의 끝점을 사용 하 여 논리 앱을 만듭니다. 논리 앱이 이러한 HTTP 요청을 수신 하면 [트리거가](../logic-apps/logic-apps-overview.md#logic-app-concepts) 발생 하 고 워크플로의 다음 단계가 실행 됩니다.

1. [Azure Portal](https://portal.azure.com)에서 **Logic Apps 디자이너** 를 여는 빈 논리 앱을 만듭니다.

1. 검색 상자에서 필터로 `http request`을 입력합니다. **트리거** 목록에서 **HTTP 요청을 받을 때** 를 선택 합니다.

   ![논리 앱에 추가 되는 새 HTTP 요청 트리거를 보여 주는 Logic Apps 디자이너의 스크린샷](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 논리 앱에 대 한 끝점 URL을 생성할 수 있도록 논리 앱을 저장 합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다. 이제 끝점 URL이 트리거에 표시 됩니다. 

   ![생성 된 게시 URL이 복사 되는 HTTP 요청 트리거를 보여 주는 Logic Apps 디자이너의 스크린샷](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>메시지를 보내는 SAP 작업 만들기

다음으로 [HTTP 요청 트리거가](#create-http-request-trigger) 발생할 때 idoc 메시지를 SAP로 보내는 작업을 만듭니다.

1. Logic Apps 디자이너의 트리거 아래에서 **새 단계** 를 선택 합니다.

   ![새 단계를 추가 하기 위해 편집 중인 논리 앱을 보여 주는 Logic Apps 디자이너의 스크린샷](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 검색 상자에서 필터로 `sap`을 입력합니다. **작업** 목록에서 **SAP로 메시지 보내기** 를 선택 합니다.
  
   !["SAP로 메시지 보내기" 작업을 선택 하 여 표시 하는 Logic Apps 디자이너의 스크린샷](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   또는 **Enterprise** 탭을 선택 하 고 SAP 작업을 선택할 수 있습니다.

   ![엔터프라이즈 탭에서 "SAP로 메시지 보내기" 작업을 선택 표시 하는 Logic Apps 디자이너의 스크린샷](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 연결이 이미 있는 경우 다음 단계를 계속 합니다. 새 연결을 만들지 묻는 메시지가 표시 되 면 다음 정보를 제공 하 여 온-프레미스 SAP 서버에 연결 합니다.

   1. 연결 이름을 제공합니다.

   1. 데이터 게이트웨이를 사용 하는 경우 다음 단계를 수행 합니다.
   
      1. **데이터 게이트웨이** 섹션의 **구독** 에서 먼저 데이터 게이트웨이 설치에 대 한 Azure Portal에서 만든 데이터 게이트웨이 리소스에 대 한 Azure 구독을 선택 합니다.
   
      1. **연결 게이트웨이** 아래에서 Azure의 데이터 게이트웨이 리소스를 선택 합니다.

   1. 연결 정보를 계속 제공 합니다. **Logon 유형** 속성의 경우 속성이 **응용 프로그램 서버** 또는 **그룹** 으로 설정 되었는지 여부에 따라 단계를 수행 합니다.
   
      * **응용 프로그램 서버의** 경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 애플리케이션 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **그룹** 의 경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행 하 여 잘못 된 값을 확인 하는 데 사용 됩니다. 이 동작은 이전에 발생 한 문제를 검색 하는 데 도움이 될 수 있습니다. **안전 입력** 옵션은 이전 버전과의 호환성을 위해 사용할 수 있으며 문자열 길이만 확인 합니다. [안전 입력 옵션](#safe-typing)에 대해 자세히 알아보세요.

   1. 작업이 완료되면 **만들기** 를 선택합니다.

      Logic Apps는 연결을 설정 하 고 테스트 하 여 연결이 제대로 작동 하는지 확인 합니다.

    > [!NOTE]

    > 다음 오류가 표시 되 면 SAP NCo 클라이언트 라이브러리를 설치 하는 데 문제가 있는 것입니다. 
    >
    > **연결 테스트에 실패 했습니다. 오류 ' 요청을 처리 하지 못했습니다. 오류 세부 정보: ' 파일 또는 어셈블리 ' sapnco, Version = 3.0.0.42, Culture = 중립, PublicKeyToken 50436dca5c7f7d23 ' 또는 해당 종속성 중 하나를 로드할 수 없습니다. 시스템에서 지정 된 파일을 찾을 수 없습니다. '. '**
    >
    > [필요한 SAP NCo 클라이언트 라이브러리 버전을 설치 하 고 다른 모든 필수 구성 요소를 충족](#sap-client-library-prerequisites)하는지 확인 합니다.

1. 이제 SAP 서버에서 작업을 찾아 선택합니다.

   1. **SAP 작업** 상자에서 폴더 아이콘을 선택 합니다. 파일 목록에서 사용하려는 SAP 메시지를 찾아서 선택합니다. 목록을 탐색하려면 화살표를 사용합니다.

      이 예에서는 **Orders** 형식이 포함 된 idoc를 선택 합니다.

      ![IDoc 작업을 찾아서 선택합니다.](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      원하는 작업을 찾을 수 없는 경우 경로를 수동으로 입력할 수 있습니다. 예를 들면 다음과 같습니다.

      ![수동으로 IDoc 작업 경로 입력](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 식 편집기를 통해 **SAP 동작** 에 대 한 값을 제공 합니다. 이렇게 하면 여러 메시지 유형에 동일한 작업을 사용할 수 있습니다.

      IDoc 작업에 대 한 자세한 내용은 [idoc 작업에 대 한 메시지 스키마](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 참조 하세요.

   1. **입력 메시지** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 해당 목록의 **HTTP 요청을 수신하는 경우** 아래에서 **본문** 필드를 선택합니다.

      이 단계에서는 HTTP 요청 트리거의 본문 콘텐츠를 포함 하 고 해당 출력을 SAP 서버에 보냅니다.

      ![트리거에서 "Body" 속성을 선택 합니다.](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      완료 되 면 SAP 작업은 다음 예제와 같습니다.

      ![SAP 작업 완료](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

#### <a name="send-flat-file-idocs"></a>플랫 파일 송신 IDocs

XML 봉투 (envelope)로 래핑할 경우 플랫 파일 스키마와 함께 IDocs를 사용할 수 있습니다. 플랫 파일 IDoc를 보내려면 일반 지침을 사용 하 여 다음 변경 내용이 포함 된 [idoc 메시지를 보내는 SAP 작업을 만듭니다](#create-sap-action-to-send-message) .

1. **Sap로 메시지 보내기** 작업의 경우 SAP action URI를 사용 `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc` 합니다.

1. XML 봉투 (envelope)를 사용 하 여 입력 메시지의 서식을 지정 합니다. 예를 들어 다음 예제 메시지를 참조 하세요.

```xml
<?xml version="1.0" encoding="utf-8"?>
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
  <idocData>EDI_DC    300                      ORDERS052SAPMSS    LIMSFTABCSWI                                                                                           ED  93AORDERSOLP     VLTRFC    KUMSFTABCSWI                                                                                           13561                       231054476                                                                           20190523085430ORDERSORDERS05          US
E2EDK01005300                1     E2EDK010050     1       USD                                                                        Z4O14506907554
E2EDK03   300                2     E2EDK03   0     2   02220190523
E2EDKA1   300                3     E2EDKA1   0     2   RE                  MSFTASWI
E2EDKA1   300                4     E2EDKA1   0     2   US                  MSFTASWI
E2EDKA1   300                5     E2EDKA1   0     2   WE                  MSFTASWILIC
E2EDKA1   300                6     E2EDKA1   0     2   Z1 KKKKKKK                           ABC YYYYYYYYYYY ZZ                                                                                                                          BBBBBBBBBBBBBBBB 11                                                                                      ttttttttttt                                 6666              US                                                                                                999 999 99 99                                                                                                                SSSSSSS SSS SSSSSS                                                                                                                                SSSSSSS SSS SSSSSS
E2EDKA1   300                7     E2EDKA1   0     2   Z2 KKKKKKK                           BBBBBBBBBBBBBBBB DDDDDDDD ZZ                                                                                                                EEEEEEEEEEE 86                                                                                           rrrrrrrr                                    8888              US                                                                                                999 999 99 99                                                                                                                NNNNNN NNNNNN                                                                                                                                     NNNNNN NNNNNN
E2EDK02   300                8     E2EDK02   0     2   901Z
E2EDK02   300                9     E2EDK02   0     2   90399680096ZZS2002
E2EDK02   300                10    E2EDK02   0     2   902S
E2EDKT1   300                11    E2EDKT1   0     2   Z1EME
E2EDKT2   300                12    E2EDKT2   0     3   xxx@xxx-xx.xx
E2EDKT1   300                13    E2EDKT1   0     2   Z2EME
E2EDKT2   300                14    E2EDKT2   0     3   x.xxxxxx@xxxxxxxx-xxxxxxxxxx.xx
E2EDP01001300                15    E2EDP010010     2   10         1              EA                          999.9
E2EDP19   300                16    E2EDP19   0     3   00AAAA-11111</idocData>
</SendIdoc>
```



### <a name="create-http-response-action"></a>HTTP 응답 작업 만들기

이제 논리 앱의 워크플로에 응답 작업을 추가하고 SAP 작업의 출력을 포함합니다. 이렇게 하면 논리 앱이 SAP 서버의 결과를 원래 요청자에게 반환합니다.

1. Logic Apps 디자이너의 SAP 작업에서 **새 단계** 를 선택 합니다.

1. 검색 상자에서 필터로 `response`을 입력합니다. **작업** 목록에서 **응답** 을 선택 합니다.

1. **본문** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 이 목록에서 **SAP로 메시지 보내기** 에서 **본문** 필드를 선택 합니다.

   ![SAP 작업 완료](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 논리 앱을 저장합니다.

#### <a name="create-rfc-request-response"></a>RFC 요청 만들기-응답

> [!NOTE]
> SAP 트리거는 tRFC를 통해 IDocs를 수신 하며,이는 응답 매개 변수가 디자인에 포함 되지 않습니다. 

SAP ABAP에서 Logic Apps 하는 데 RFC (원격 함수 호출)를 사용 하 여 응답을 수신 해야 하는 경우 요청 및 응답 패턴을 만들어야 합니다. 논리 앱에서 IDocs를 수신 하려면 첫 번째 작업을 상태 코드 및 콘텐츠가 없는 [HTTP 요청](../connectors/connectors-native-reqres.md#add-a-response-action) 으로 설정 해야 합니다 `200 OK` . 이 권장 단계에서는 tRFC를 통해 SAP LUW 비동기 전송을 완료 합니다 .이를 통해 SAP CPIC 대화를 다시 사용할 수 있습니다. 그런 다음 추가 전송을 차단 하지 않고 수신 된 IDoc를 처리 하도록 논리 앱에 추가 작업을 추가할 수 있습니다.

요청 및 응답 패턴을 구현 하려면 먼저 [ `generate schema` 명령을](#generate-schemas-for-artifacts-in-sap)사용 하 여 RFC 스키마를 검색 해야 합니다. 생성 된 스키마에는 두 개의 가능한 루트 노드가 있습니다. 

1. SAP에서 수신 하는 호출인 요청 노드입니다.

1. SAP에 회신 하는 응답 노드입니다.

다음 예제에서는 RFC 모듈에서 요청 및 응답 패턴이 생성 됩니다 `STFC_CONNECTION` . 요청 XML이 구문 분석 되어 SAP 요청이 있는 노드 값을 추출 `<ECHOTEXT>` 합니다. 응답은 현재 타임 스탬프를 동적 값으로 삽입 합니다. 논리 앱에서 SAP로 RFC를 보낼 때 비슷한 응답을 받게 `STFC_CONNECTION` 됩니다.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-logic-app"></a>Logic App 테스트

1. 논리 앱을 아직 사용 하도록 설정 하지 않은 경우 논리 앱 메뉴에서 **개요** 를 선택 합니다. 도구 모음에서 **사용** 을 선택 합니다.

1. 디자이너 도구 모음에서 **실행** 을 선택 합니다. 이 단계에서는 논리 앱을 수동으로 시작합니다.

1. HTTP 요청 트리거의 URL에 HTTP POST 요청을 전송 하 여 논리 앱을 트리거합니다.
요청에 메시지 콘텐츠를 포함 합니다. 요청을 보내려면 [Postman](https://www.getpostman.com/apps)과 같은 도구를 사용할 수 있습니다.

   이 문서에서는 요청이 XML 형식으로, 사용 중인 SAP 작업에 대한 네임스페이스를 포함해야 하는 IDoc 파일을 보냅니다. 예를 들면 다음과 같습니다.

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. HTTP 요청을 보낸 후 논리 앱의 응답을 기다립니다.

   > [!NOTE]
   > 응답에 필요한 모든 단계가 [요청 시간 제한](./logic-apps-limits-and-config.md) 내에 완료되지 않을 경우 논리 앱이 시간 초과될 수 있습니다. 이 조건이 발생하면 요청이 차단될 수 있습니다. 문제를 진단하려면 [논리 앱을 확인하고 모니터링](../logic-apps/monitor-logic-apps.md)하는 방법을 알아보세요.

이제 SAP 서버와 통신할 수 있는 논리 앱을 만들었습니다. 논리 앱에 대한 SAP 연결을 설정했으므로, BAPI 및 RFC와 같은 다른 사용 가능한 SAP 작업을 살펴볼 수 있습니다.

## <a name="receive-message-from-sap"></a>SAP에서 메시지 받기

이 예제에서는 앱이 SAP 시스템에서 메시지를 받을 때 트리거하는 논리 앱을 사용 합니다.

### <a name="add-an-sap-trigger"></a>SAP 트리거 추가

1. Azure Portal에서 Logic Apps 디자이너를 여는 빈 논리 앱을 만듭니다.

1. 검색 상자에서 필터로 `sap`을 입력합니다. **트리거** 목록에서 **SAP에서 메시지를 받을 때** 를 선택 합니다.

   ![SAP 트리거 추가](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   또는 **Enterprise** 탭을 선택한 다음 트리거를 선택할 수 있습니다.

   ![Enterprise 탭에서 SAP 트리거 추가](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 연결이 이미 있는 경우 다음 단계를 계속 진행 하 여 SAP 작업을 설정할 수 있습니다. 그러나 연결 정보를 입력 하 라는 메시지가 표시 되 면 온-프레미스 SAP 서버에 대 한 연결을 만들 수 있도록 정보를 제공 합니다.

   1. 연결 이름을 제공합니다.

   1. 데이터 게이트웨이를 사용 하는 경우 다음 단계를 수행 합니다.

      1. **데이터 게이트웨이** 섹션의 **구독** 에서 먼저 데이터 게이트웨이 설치에 대 한 Azure Portal에서 만든 데이터 게이트웨이 리소스에 대 한 Azure 구독을 선택 합니다.

      1. **연결 게이트웨이** 아래에서 Azure의 데이터 게이트웨이 리소스를 선택 합니다.

   1. 연결에 대 한 정보를 계속 제공 합니다. **Logon 유형** 속성의 경우 속성이 **응용 프로그램 서버** 또는 **그룹** 으로 설정 되었는지 여부에 따라 단계를 수행 합니다.

      * **응용 프로그램 서버의** 경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 애플리케이션 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **그룹** 의 경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행 하 여 잘못 된 값을 확인 하는 데 사용 됩니다. 이 동작은 이전에 발생 한 문제를 검색 하는 데 도움이 될 수 있습니다. **안전 입력** 옵션은 이전 버전과의 호환성을 위해 사용할 수 있으며 문자열 길이만 확인 합니다. [안전 입력 옵션](#safe-typing)에 대해 자세히 알아보세요.

   1. 작업이 완료되면 **만들기** 를 선택합니다.

      Logic Apps는 연결을 설정 하 고 테스트 하 여 연결이 제대로 작동 하는지 확인 합니다.

1. SAP 시스템 구성에 따라 [필요한 매개 변수](#parameters) 를 제공 합니다. 

   Sap [작업 목록을 지정 하 여 sap 서버에서 수신 하는 메시지를 필터링](#filter-with-sap-actions)할 수 있습니다.

   파일 선택기에서 SAP 작업을 선택할 수 있습니다.

   ![논리 앱에 SAP 작업 추가](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   또는 작업을 수동으로 지정할 수 있습니다.

   ![사용할 SAP 작업을 수동으로 입력 합니다.](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   다음은 두 개 이상의 메시지를 수신하도록 트리거를 설정할 때 작업이 어떻게 나타나는지 보여주는 예제입니다.

   ![여러 메시지를 받는 트리거 예제](media/logic-apps-using-sap-connector/example-trigger.png)

   SAP 동작에 대 한 자세한 내용은 [IDoc 작업에 대 한 메시지 스키마](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations) 를 참조 하세요.

1. 이제 SAP 시스템에서 메시지 수신을 시작할 수 있도록 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

이제 논리 앱이 SAP 시스템에서 메시지를 수신할 준비가 완료되었습니다.

> [!NOTE]
> SAP 트리거는 폴링 트리거가 아니라 webhook 기반 트리거입니다. 데이터 게이트웨이를 사용 하는 경우 메시지가 존재 하는 경우에만 데이터 게이트웨이에서 트리거가 호출 되므로 폴링이 필요 하지 않습니다.

**' Sapgw00 ' 서비스** 와 유사한 메시지와 함께 **500 잘못 된 게이트웨이** 오류를 수신 하는 경우 API 연결에서 게이트웨이 서비스 이름을 바꾸고 구성의 포트 번호를 트리거합니다. 다음 예제 오류에서는 `sapgw00` 실제 포트 번호 (예:)로 바꾸어야 `3300` 합니다. 

```json
"body": {
   "error": {
      "code": 500,
      "source": "EXAMPLE-FLOW-NAME.eastus.environments.microsoftazurelogicapps.net",
      "clientRequestId": "00000000-0000-0000-0000-000000000000",
      "message": "BadGateway",
      "innerError": {
         "error": {
            "code": "UnhandledException",
            "message": "\nERROR service 'sapgw00' unknown\nTIME Wed Nov 11 19:37:50 2020\nRELEASE 721\nCOMPONENT NI (network interface)\nVERSION 40\nRC -3\nMODULE ninti.c\nLINE 933\nDETAIL NiPGetServByName: 'sapgw00' not found\nSYSTEM CALL getaddrinfo\nCOUNTER 1\n\nRETURN CODE: 20"
         }
      }
```

#### <a name="parameters"></a>매개 변수

SAP 커넥터는 간단한 문자열 및 숫자 입력과 함께 다음 테이블 매개 변수 (입력)를 허용 합니다 `Type=ITAB` .

* 이전 SAP 릴리스에 대 한 테이블 방향 매개 변수 (입력 및 출력 모두)입니다.

* 매개 변수 변경-최신 SAP 릴리스에 대 한 테이블 방향 매개 변수를 대체 합니다.

* 계층적 테이블 매개 변수

#### <a name="filter-with-sap-actions"></a>SAP 작업을 사용 하 여 필터링

필요에 따라 단일 또는 여러 SAP 작업에 목록 또는 배열을 제공 하 여 논리 앱에서 수신 하는 메시지를 필터링 할 수 있습니다. 기본적으로이 배열은 비어 있습니다. 즉, 논리 앱이 필터링 하지 않고 SAP 서버의 모든 메시지를 수신 합니다. 

배열 필터를 설정 하는 경우 트리거는 지정 된 SAP 작업 유형에 서 메시지를 수신 하 고 SAP 서버에서 다른 모든 메시지를 거부 합니다. 그러나이 필터는 수신 된 페이로드의 입력이 취약 하거나 강력 한지에 영향을 주지 않습니다.

SAP 작업 필터링은 온-프레미스 데이터 게이트웨이에 대 한 SAP 어댑터 수준에서 발생 합니다. 자세한 내용은 [SAP에서 Logic Apps에 테스트 IDocs를 보내는 방법](#test-sending-idocs-from-sap)을 참조 하세요.

SAP에서 논리 앱의 트리거로 IDoc 패킷을 보낼 수 없는 경우 SAP tRFC 대화 상자 (T 코드 SM58)에서 트랜잭션 RFC (tRFC) 호출 거부 메시지를 참조 하세요. SAP 인터페이스에 다음과 같은 오류 메시지가 나타날 수 있습니다 .이 오류 메시지는 **상태 텍스트** 필드의 부분 문자열 제한으로 인해 잘립니다.

* `The RequestContext on the IReplyChannel was closed without a reply being`: 오류 때문에 채널에 대 한 모든 catch 처리기가 채널을 종료 하 고 다른 메시지를 처리 하기 위해 채널을 다시 작성 하는 경우 예기치 않은 오류가 발생 합니다.

  * 논리 앱이 IDoc를 수신 했음을 인정 하려면 상태 코드를 반환 하는 [응답 작업을 추가](../connectors/connectors-native-reqres.md#add-a-response-action) `200 OK` 합니다. IDoc는 응답 페이로드를 허용 하지 않는 tRFC를 통해 전송 됩니다.

  * 대신 IDoc를 거부 해야 하는 경우에는이 아닌 모든 HTTP 상태 코드를 사용 하 여 응답 `200 OK` 합니다. 따라서 Sap 어댑터는 사용자를 대신 하 여 sap에 다시 예외를 반환 합니다. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: IDoc XML 페이로드를 생성 하는 데 실패 하는 것과 같은 다른 오류가 발생 하는 경우와 같은 오류가 발생 합니다 .이는 해당 세그먼트가 SAP에 의해 해제 되지 않으므로 변환에 필요한 세그먼트 유형 메타 데이터가 누락 되었기 때문입니다. 

  * SAP에서 이러한 세그먼트를 해제 하려면 SAP 시스템의 ABAP 엔지니어에 게 문의 하세요.
### <a name="asynchronous-request-reply-for-triggers"></a>비동기 요청-트리거에 대 한 회신

SAP 커넥터는 Logic Apps 트리거에 대 한 Azure의 [비동기 요청-회신 패턴](/azure/architecture/patterns/async-request-reply) 을 지원 합니다. 이 패턴을 사용 하면 기본 동기 요청-회신 패턴으로 인해 실패 한 성공적인 요청을 만들 수 있습니다. 

> [!TIP]
> 여러 응답 작업을 포함 하는 논리 앱에서는 모든 응답 작업에서 동일한 요청-회신 패턴을 사용 해야 합니다. 예를 들어 논리 앱에서 가능한 여러 응답 작업으로 스위치 컨트롤을 사용 하는 경우 동기 또는 비동기 같은 요청-응답 패턴을 사용 하도록 모든 응답 작업을 구성 해야 합니다. 

응답 작업에 대 한 비동기 응답을 사용 하면 요청을 처리할 수 있는 경우 논리 앱이 회신을 사용 하 여 응답할 수 있습니다 `202 Accepted` . 회신에는 요청의 최종 상태를 검색 하는 데 사용할 수 있는 위치 헤더가 포함 되어 있습니다.

SAP 커넥터를 사용 하 여 논리 앱에 대 한 비동기 요청-회신 패턴을 구성 하려면:

1. **Logic Apps 디자이너** 에서 논리 앱을 엽니다.

1. SAP 커넥터가 논리 앱에 대 한 트리거 인지 확인 합니다.

1. 논리 앱의 **응답** 작업을 엽니다. 작업의 제목 표시줄에서 메뉴 (**...**) &gt; 를 선택 합니다. **설정**.

1. 응답 동작에 대 한 **설정** 에서 **비동기 응답** 의 설정/해제를 설정 합니다. 완료를 선택 합니다.

1. 논리 앱에 대 한 변경 내용을 저장 합니다.

## <a name="find-extended-error-logs"></a>확장 오류 로그 찾기

전체 오류 메시지의 경우 SAP 어댑터의 확장 로그를 확인 합니다. [SAP 커넥터에 확장 로그 파일을 사용 하도록 설정할](#extended-sap-logging-in-on-premises-data-gateway)수도 있습니다.

6 월 2020 이상에서 온-프레미스 데이터 게이트웨이 릴리스의 경우 [앱 설정에서 게이트웨이 로그를 사용 하도록 설정할](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app)수 있습니다. 

* 기본 로깅 수준은 **경고** 입니다.

* 온-프레미스 데이터 게이트웨이 앱의 **진단** 설정에서 **추가 로깅을** 사용 하도록 설정 하면 로깅 수준이 **정보** 로 증가 합니다.

* 로깅 수준을 **자세히** 로 늘리려면 구성 파일에서 다음 설정을 업데이트 합니다. 일반적으로 구성 파일은에 `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` 있습니다.

```json
<setting name="SapTraceLevel" serializeAs="String">
   <value>Verbose</value>
</setting>
```

4 월 2020 일 이전 버전의 온-프레미스 데이터 게이트웨이 릴리스에서는 기본적으로 로그가 사용 하지 않도록 설정 됩니다.

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이의 확장 된 SAP 로깅

[Logic Apps에 대 한 온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md)를 사용 하는 경우 SAP 커넥터에 대 한 확장 로그 파일을 구성할 수 있습니다. 온-프레미스 데이터 게이트웨이를 사용 하 여 ETW(Windows용 이벤트 추적) (ETW) 이벤트를 게이트웨이의 로깅 .zip 파일에 포함 된 순환 로그 파일로 리디렉션할 수 있습니다. 

게이트웨이 앱의 설정에서의 .zip 파일로 [게이트웨이의 모든 구성 및 서비스 로그를 내보낼](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) 수 있습니다.

> [!NOTE]
> 확장 로깅은 항상 사용 하도록 설정 하는 경우 논리 앱의 성능에 영향을 줄 수 있습니다. 문제를 분석 하 고 문제를 해결 한 후에 확장 된 로그 파일을 해제 하는 것이 좋습니다.

#### <a name="capture-etw-events"></a>ETW 이벤트 캡처

필요에 따라 고급 사용자는 ETW 이벤트를 직접 캡처할 수 있습니다. 그런 다음 [Event Hubs에서 Azure 진단 데이터를 사용](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md) 하거나 데이터를 [수집 하 여 로그를 Azure Monitor](/azure/azure-monitor/agents/diagnostics-extension-logs)수 있습니다. 자세한 내용은 [데이터 수집 및 저장에 대 한 모범 사례](/azure/architecture/best-practices/monitoring#collecting-and-storing-data)를 참조 하세요. [Perfview](https://github.com/Microsoft/perfview/blob/master/README.md) 를 사용 하 여 결과 ETL 파일 작업을 수행 하거나 프로그램을 직접 작성할 수 있습니다. 이 연습에서는 PerfView를 사용 합니다.

1. Perfview 메뉴 **에서 collect collect** &gt;  를 선택 하 여 이벤트를 캡처합니다.

1. **추가 공급자** 필드에를 입력 하 여 sap `*Microsoft-LobAdapter` 어댑터 이벤트를 캡처할 sap 공급자를 지정 합니다. 이 정보를 지정 하지 않으면 추적에 일반 ETW 이벤트만 포함 됩니다.

1. 다른 기본 설정은 그대로 둡니다. 원하는 경우 **데이터 파일** 필드에서 파일 이름이 나 위치를 변경할 수 있습니다.

1. **컬렉션 시작** 을 선택 하 여 추적을 시작 합니다.

1. 문제를 재현 하거나 충분 한 분석 데이터를 수집한 후 **수집 중지** 를 선택 합니다.

Azure 지원 엔지니어와 같은 다른 당사자와 데이터를 공유 하려면 ETL 파일을 압축 합니다.

추적의 내용을 보려면 다음을 수행 합니다.

1. Perfview에서 **파일** &gt; **열기** 를 선택 하 고 방금 생성 한 ETL 파일을 선택 합니다.

1. PerfView 사이드바의 ETL 파일 아래 **이벤트** 섹션

1. **필터** 에서를 필터링 하 여 `Microsoft-LobAdapter` 관련 이벤트 및 게이트웨이 프로세스만 표시 합니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 논리 앱을 트리거하기 위해, SAP 시스템에서 메시지를 보냅니다.

1. 논리 앱 메뉴에서 **개요** 를 선택 합니다. 논리 앱에 대 한 모든 새 실행에 대 한 **실행 기록을** 검토 합니다.

1. 가장 최근 실행을 엽니다. 그러면 SAP 시스템에서 보낸 메시지가 트리거 출력 섹션에 표시됩니다.

### <a name="test-sending-idocs-from-sap"></a>SAP에서 IDocs를 보내는 테스트

SAP에서 논리 앱으로 IDocs를 보내려면 다음과 같은 최소 구성이 필요 합니다.

> [!IMPORTANT]
> 논리 앱을 사용 하 여 SAP 구성을 테스트 하는 경우에만 다음 단계를 수행 합니다. 프로덕션 환경에는 추가 구성이 필요 합니다.

1. [SAP에서 RFC 대상 구성](#create-rfc-destination)

1. [RFC 대상에 대 한 ABAP 연결 만들기](#create-abap-connection)

1. [받는 사람 포트 만들기](#create-receiver-port)

1. [보낸 사람 포트 만들기](#create-sender-port)

1. [논리적 시스템 파트너 만들기](#create-logical-system-partner)

1. [파트너 프로필 만들기](#create-partner-profiles)

1. [메시지 보내기 테스트](#test-sending-messages)

#### <a name="create-rfc-destination"></a>RFC 대상 만들기

1. SAP 인터페이스에서 **RFC 연결 설정의 구성을** 열려면 **/n** 접두사와 함께 **s m 59** 트랜잭션 코드 (t-sql)를 사용 합니다.

1. **Tcp/ip 연결**  >  **만들기** 를 선택 합니다.

1. 다음 설정을 사용 하 여 새 RFC 대상을 만듭니다.
    
    * **RFC 대상** 에 대해 이름을 입력 합니다.
    
    * **기술 설정** 탭의 **정품 인증 유형에** 대해 **등록 된 서버 프로그램** 을 선택 합니다. **프로그램 ID** 에 값을 입력 합니다. SAP에서이 식별자를 사용 하 여 논리 앱의 트리거를 등록 합니다.

    > [!IMPORTANT]
    > SAP **프로그램 ID** 는 대/소문자를 구분 합니다. 논리 앱과 SAP 서버를 구성할 때 **프로그램 ID** 에 대해 동일한 대/소문자 형식을 일관 되 게 사용 해야 합니다. 그렇지 않으면 IDoc를 SAP로 보내려고 할 때 tRFC 모니터 (T-코드 SM58)에서 다음과 같은 오류가 발생할 수 있습니다.
    >
    > * **함수 IDOC_INBOUND_ASYNCHRONOUS 찾을 수 없음**
    > * **비 ABAP RFC 클라이언트 (파트너 유형)는 지원 되지 않습니다.**
    >
    > SAP에서 자세한 내용은 다음 참고 사항 (로그인 필요) 및를 참조 <https://launchpad.support.sap.com/#/notes/2399329> 하세요 <https://launchpad.support.sap.com/#/notes/353597> .
    
    * **유니코드** 탭에서 **대상 시스템과 통신 형식** 에 대해 **유니코드** 를 선택 합니다.

1. 변경 내용을 저장합니다.

1. Azure Logic Apps를 사용 하 여 새 **프로그램 ID** 를 등록 합니다.

1. 연결을 테스트 하려면 SAP 인터페이스의 새 **RFC 대상** 아래에서 **연결 테스트** 를 선택 합니다.

#### <a name="create-abap-connection"></a>ABAP 연결 만들기

1. **RFC 연결 설정의 구성을** 열려면 SAP 인터페이스에서 _ */n** 접두사와 함께 **S m 59** _ 트랜잭션 코드 (t-sql)를 사용 합니다.

1. **Abap 연결**  >  **만들기** 를 선택 합니다.

1. **RFC 대상** 의 경우 [테스트 SAP 시스템](#create-rfc-destination)에 대 한 식별자를 입력 합니다.

1. 변경 내용을 저장합니다.

1. 연결을 테스트 하려면 **연결 테스트** 를 선택 합니다.

#### <a name="create-receiver-port"></a>받는 사람 포트 만들기

1. **IDOC 처리 설정의 포트** 를 열려면 SAP 인터페이스에서 **/n** 접두사와 함께 **we21** 트랜잭션 코드 (t-sql)를 사용 합니다.

1.   >  **트랜잭션 RFC**  >  **만들기** 포트를 선택 합니다.

1. 열리는 설정 상자에서 **자체 포트 이름** 을 선택 합니다. 테스트 포트에 대해 **이름을** 입력 합니다. 변경 내용을 저장합니다.

1. 새 받는 사람 포트의 설정에서 **rfc 대상** 에 [테스트 rfc 대상](#create-rfc-destination)에 대 한 식별자를 입력 합니다.

1. 변경 내용을 저장합니다.

#### <a name="create-sender-port"></a>보낸 사람 포트 만들기

1.  **IDOC 처리 설정의 포트** 를 열려면 SAP 인터페이스에서 **/n** 접두사와 함께 **we21** 트랜잭션 코드 (t-sql)를 사용 합니다.

1.   >  **트랜잭션 RFC**  >  **만들기** 포트를 선택 합니다.

1. 열리는 설정 상자에서 **자체 포트 이름** 을 선택 합니다. 테스트 포트에 대해 **SAP** 로 시작 하는 **이름을** 입력 합니다. 모든 보낸 사람 포트 이름은 **SAP**(예: **SAPTEST**) 문자로 시작 해야 합니다. 변경 내용을 저장합니다.

1. 새 발신자 포트의 설정에서 **RFC 대상** 에 대해 [abap 연결](#create-abap-connection)에 대 한 식별자를 입력 합니다.

1. 변경 내용을 저장합니다.

#### <a name="create-logical-system-partner"></a>논리 시스템 파트너 만들기

1. **변경 뷰 "논리 시스템": 개요** 설정을 열려면 SAP 인터페이스에서 **bd54** 트랜잭션 코드 (T 코드)를 사용 합니다.

1. 표시 되는 경고 메시지를 수락 합니다. **주의: 테이블이 교차 클라이언트입니다** .

1. 기존 논리 시스템을 표시 하는 목록 위에서 **새 항목** 을 선택 합니다.

1. 새 논리 시스템에 대해 **Log.System** identifier 및 짧은 **이름** 설명을 입력 합니다. 변경 내용을 저장합니다.

1. **워크 벤치에 대 한 프롬프트가** 표시 되 면 설명을 제공 하 여 새 요청을 만들거나 요청을 이미 만든 경우이 단계를 건너뜁니다.

1. 워크 벤치 요청을 만든 후 해당 요청을 테이블 업데이트 요청에 연결 합니다. 테이블이 업데이트 되었는지 확인 하려면 변경 내용을 저장 합니다.

#### <a name="create-partner-profiles"></a>파트너 프로필 만들기

프로덕션 환경에서는 두 개의 파트너 프로필을 만들어야 합니다. 첫 번째 프로필은 조직 및 SAP 시스템용 발신자를 위한 것입니다. 두 번째 프로필은 논리 앱 인 받는 사람에 대 한 것입니다.

1. **파트너 프로필** 설정을 열려면 SAP 인터페이스에서 **/n** 접두사와 함께 **we20** 트랜잭션 코드 (t-sql)를 사용 합니다.

1. **파트너 프로필** 에서 **파트너 유형 LS**  >  **만들기** 를 선택 합니다.

1. 다음 설정을 사용 하 여 새 파트너 프로필을 만듭니다.

    * 파트너 번호에 대해 [논리 시스템 파트너의 id](#create-logical-system-partner)를 입력 **합니다.**

    * **Partn의 경우 형식** 으로 **LS** 를 입력 합니다.

    * **에이전트** 의 경우 Azure Logic Apps 또는 다른 비 sap 시스템의 프로그램 식별자를 등록할 때 사용할 sap 사용자 계정에 대 한 식별자를 입력 합니다.

1. 변경 내용을 저장합니다. [논리적 시스템 파트너를 만들지](#create-logical-system-partner)않은 경우 오류가 발생 하 고 **올바른 파트너 번호를 입력** 합니다.

1. 파트너 프로필의 설정에 있는 **아웃 바운드 parmtrs** 에서 **아웃 바운드 매개 변수 만들기** 를 선택 합니다.

1. 다음 설정을 사용 하 여 새 아웃 바운드 매개 변수를 만듭니다.

    * **메시지 유형**(예: **cremas**)을 입력 합니다.

    * [받는 사람 포트의 식별자](#create-receiver-port)를 입력 합니다.

    * Pack에 대 한 IDoc 크기를 입력 **합니다. 크기**. 또는 [SAP에서 한 번에 하나씩 idocs를 보내려면](#receive-idoc-packets-from-sap) **Idocs를 즉시 전달** 을 선택 합니다.

1. 변경 내용을 저장합니다.

#### <a name="test-sending-messages"></a>메시지 보내기 테스트

1. **IDoc 처리 설정에 대 한 테스트 도구** 를 열려면 SAP 인터페이스에서 **/n** 접두사와 함께 **we19** 트랜잭션 코드 (t-sql)를 사용 합니다.

1. **테스트용 템플릿** 에서 **Via 메시지 유형** 을 선택 하 고 메시지 유형 (예: **cremas**)을 입력 합니다. **만들기** 를 선택합니다.

1. [ **계속**]을 선택 하 여 **idoc 유형?** 메시지를 확인 합니다.

1. **편집기** 노드를 선택 합니다. 받는 사람 및 보낸 사람 포트에 적절 한 값을 입력 합니다. **계속** 을 선택합니다.

1. **표준 아웃 바운드 처리** 를 선택 합니다.

1. 아웃 바운드 IDoc 처리를 시작 하려면 **계속** 을 선택 합니다. 처리가 완료 되 면 **SAP 시스템 또는 외부 프로그램으로 전송 된 Idoc** 메시지가 표시 됩니다.

1.  처리 오류를 확인 하려면 **/n** 접두사와 함께 **sm58** 트랜잭션 코드 (t-sql)를 사용 합니다.

## <a name="receive-idoc-packets-from-sap"></a>SAP에서 IDoc 패킷 수신

SAP를 설정 하 여 일괄 처리 또는 IDocs 그룹인 [패킷으로 IDocs를 보낼](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html)수 있습니다. IDoc 패킷, SAP 커넥터 및 특히 트리거를 수신 하려면 추가 구성이 필요 하지 않습니다. 그러나 트리거가 패킷을 받은 후 IDoc 패킷에서 각 항목을 처리 하려면 패킷을 개별 Idoc로 분할 하기 위해 몇 가지 추가 단계가 필요 합니다.

[ `xpath()` 함수](./workflow-definition-language-functions-reference.md#xpath)를 사용 하 여 패킷에서 개별 idocs를 추출 하는 방법을 보여 주는 예제는 다음과 같습니다.

1. 시작 하기 전에 SAP 트리거를 사용 하는 논리 앱이 필요 합니다. 논리 앱에 아직 없는 경우이 항목의 이전 단계를 수행 하 여 [SAP 트리거를 사용 하 여 논리 앱을 설정](#receive-message-from-sap)합니다.

    > [!IMPORTANT]
    > SAP **프로그램 ID** 는 대/소문자를 구분 합니다. 논리 앱과 SAP 서버를 구성할 때 **프로그램 ID** 에 대해 동일한 대/소문자 형식을 일관 되 게 사용 해야 합니다. 그렇지 않으면 IDoc를 SAP로 보내려고 할 때 tRFC 모니터 (T-코드 SM58)에서 다음과 같은 오류가 발생할 수 있습니다.
    >
    > * **함수 IDOC_INBOUND_ASYNCHRONOUS 찾을 수 없음**
    > * **비 ABAP RFC 클라이언트 (파트너 유형)는 지원 되지 않습니다.**
    >
    > SAP에서 자세한 내용은 다음 참고 사항 (로그인 필요) 및를 참조 <https://launchpad.support.sap.com/#/notes/2399329> 하세요 <https://launchpad.support.sap.com/#/notes/353597> .

   예를 들면 다음과 같습니다.

   ![논리 앱에 SAP 트리거 추가](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. [논리 앱에 응답 작업을 추가](/azure/connectors/connectors-native-reqres#add-a-response-action) 하 여 SAP 요청 상태와 즉시 회신할 수 있습니다. SAP 서버와 통신 채널을 해제 하려면 트리거 바로 뒤에이 작업을 추가 하는 것이 가장 좋습니다. 응답 동작에 사용할 다음 상태 코드 () 중 하나를 선택 합니다 `statusCode` .

    * **202 수락** 됨 .이는 요청이 처리를 위해 수락 되었지만 처리가 아직 완료 되지 않았음을 의미 합니다.

    * **204 콘텐츠가 없습니다**. 즉, 서버에서 요청을 성공적으로 처리 했으며 응답 페이로드 본문에 보낼 추가 콘텐츠가 없습니다. 

    * **200 확인**. 서버에서 길이가 0 인 페이로드 본문을 생성 하는 경우에도이 상태 코드는 항상 페이로드를 포함 합니다. 

1. 논리 앱이 SAP에서 받는 XML IDoc에서 루트 네임 스페이스를 가져옵니다. XML 문서에서이 네임 스페이스를 추출 하려면 로컬 문자열 변수를 만드는 단계를 추가 하 고 식을 사용 하 여 해당 네임 스페이스를 저장 합니다 `xpath()` .

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![IDoc에서 루트 네임 스페이스 가져오기](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 개별 IDoc를 추출 하려면 배열 변수를 만들고 다른 식을 사용 하 여 IDoc 컬렉션을 저장 하는 단계를 추가 합니다 `xpath()` .

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![항목의 배열 가져오기](./media/logic-apps-using-sap-connector/get-array.png)

   배열 변수를 사용 하면 논리 앱에서 각 IDoc를 컬렉션을 열거 하 여 개별적으로 처리할 수 있습니다. 이 예제에서 논리 앱은 루프를 사용 하 여 각 IDoc를 SFTP 서버로 전송 합니다.

   ![SFTP 서버에 IDoc 보내기](./media/logic-apps-using-sap-connector/loop-batch.png)

   각 IDoc는 루트 네임 스페이스를 포함 해야 합니다 `<Receive></Receive` .이는 IDoc를 다운스트림 앱으로 보내기 전에 루트 네임 스페이스와 함께 파일 콘텐츠를 요소 내에 래핑한 이유 이며이 경우에는 SFTP 서버입니다.

새 논리 앱을 만들 때 Logic Apps 디자이너에서이 템플릿을 선택 하 여이 패턴에 대 한 빠른 시작 템플릿을 사용할 수 있습니다.

![Batch 논리 앱 템플릿 선택](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>SAP에서 아티팩트에 대한 스키마 생성

이 예제에서는 HTTP 요청으로 트리거할 수 있는 논리 앱을 사용합니다. 지정 된 IDoc 및 BAPI에 대 한 스키마를 생성 하기 위해 SAP action **스키마 생성** 은 sap 시스템으로 요청을 보냅니다.

이 SAP 작업은 xml 문서 자체의 내용이 나 데이터가 아니라 [xml 스키마](#sample-xml-schemas)를 반환 합니다. 응답에서 반환 된 스키마는 Azure Resource Manager 커넥터를 사용 하 여 통합 계정에 업로드 됩니다. 스키마에는 다음과 같은 부분이 포함 됩니다.

* 요청 메시지의 구조입니다. 이 정보를 사용 하 여 BAPI `get` 목록을 구성할 수 있습니다.

* 응답 메시지의 구조입니다. 이 정보를 사용 하 여 응답을 구문 분석 합니다. 

요청 메시지를 보내려면 일반 SAP 작업에서 **sap로 메시지 보내기** 또는 대상 **호출 bapi** 작업을 사용 합니다.

### <a name="sample-xml-schemas"></a>샘플 XML 스키마

샘플 문서를 만드는 데 사용할 XML 스키마를 생성 하는 방법에 대 한 자세한 내용은 다음 샘플을 참조 하세요. 다음 예에서는 다음을 포함 하 여 여러 유형의 페이로드를 사용 하는 방법을 보여 줍니다.

* [RFC 요청](#xml-samples-for-rfc-requests)

* [BAPI 요청](#xml-samples-for-bapi-requests)

* [IDoc 요청](#xml-samples-for-idoc-requests)

* 단순 또는 복잡 한 XML 스키마 데이터 형식

* 테이블 매개 변수

* 선택적 XML 동작

선택적 XML 프롤로그를 사용 하 여 XML 스키마를 시작할 수 있습니다. SAP 커넥터는 XML 프롤로그를 사용 하거나 사용 하지 않고 작동 합니다.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>RFC 요청에 대한 XML 샘플

다음 예제는 기본적인 RFC 호출입니다. RFC 이름은 `STFC_CONNECTION` 입니다. 이 요청은 기본 네임 스페이스 `xmlns=` 를 사용 하지만와 같이 네임 스페이스 별칭을 할당 하 고 사용할 수 있습니다 `xmmlns:exampleAlias=` . 네임 스페이스 값은 Microsoft 서비스에 대 한 SAP의 모든 Rfc에 대 한 네임 스페이스입니다. 요청에는 간단한 입력 매개 변수가 `<REQUTEXT>` 있습니다.

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

다음 예는 테이블 매개 변수가 있는 RFC 호출입니다. 이 예제 호출과 테스트 Rfc 그룹은 모든 SAP 시스템의 일부로 사용할 수 있습니다. 테이블 매개 변수의 이름은 `TCPICDAT` 입니다. 테이블 선 유형은 이며 `ABAPTEXT` 이 요소는 테이블의 각 행에 대해 반복 됩니다. 이 예제에는 라는 단일 줄이 포함 되어 있습니다 `LINE` . 테이블 매개 변수가 있는 요청에는 임의의 수의 필드가 포함 될 수 있습니다. 여기서 숫자는 양의 정수 (*n*)입니다. 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

다음 예는 익명 필드가 있는 테이블 매개 변수가 있는 RFC 호출입니다. 익명 필드는 필드에 이름이 할당 되지 않은 경우입니다. 복합 형식은 선언에서 현재 노드와 모든 자식 요소에 대 한 새 기본값을 설정 하는 별도의 네임 스페이스에서 선언 됩니다. `x002F` */* 이 기호는 SAP 필드 이름에 예약 되어 있으므로이 예제에서는 16 진수 코드를 기호에 대 한 이스케이프 문자로 사용 합니다.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

다음 예제에서는 네임 스페이스에 대 한 접두사를 포함 합니다. 모든 접두사를 한 번에 선언 하거나 임의 개수의 접두사를 노드의 특성으로 선언할 수 있습니다. RFC 네임 스페이스 별칭은 `ns0` 기본 형식에 대 한 루트 및 매개 변수로 사용 됩니다.

> [!NOTE]
> 복합 형식은 별칭을 사용 하 여 `ns3` 일반 rfc 네임 스페이스 대신 별칭을 사용 하는 rfc 형식의 다른 네임 스페이스 아래에 선언 됩니다 `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>BAPI 요청에 대한 XML 샘플

다음 XML 샘플은 [BAPI 메서드를 호출](#call-bapi-action)하는 예제 요청입니다.

> [!NOTE]
> SAP `RPY_BOR_TREE_INIT` 는 입력 필터가 없는 문제를 Logic Apps 하는 RFC에 대 한 응답으로 비즈니스 개체를 외부 시스템에서 사용할 수 있도록 합니다. Logic Apps 출력 테이블을 검사 `BOR_TREE` 합니다. `SHORT_TEXT`필드는 비즈니스 개체의 이름에 사용 됩니다. 출력 테이블에서 SAP에 의해 반환 되지 않은 비즈니스 개체는 Logic Apps에서 액세스할 수 없습니다.
> 
> 사용자 지정 비즈니스 개체를 사용 하는 경우 SAP에서 이러한 비즈니스 개체를 게시 하 고 릴리스 해야 합니다. 그렇지 않으면 SAP는 출력 테이블에 사용자 지정 비즈니스 개체를 나열 하지 않습니다 `BOR_TREE` . SAP에서 비즈니스 개체를 노출할 때까지 Logic Apps에서 사용자 지정 비즈니스 개체에 액세스할 수 없습니다. 

다음 예제에서는 BAPI 메서드를 사용 하 여 뱅크 목록을 가져옵니다 `GETLIST` . 이 샘플에는 bank의 비즈니스 개체가 포함 되어 `BUS1011` 있습니다. 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

다음 예제에서는 메서드를 사용 하 여 bank 개체를 만듭니다 `CREATE` . 이 예제에서는 이전 예제와 동일한 비즈니스 개체를 사용 `BUS1011` 합니다. 메서드를 사용 하 여 `CREATE` bank를 만드는 경우이 메서드는 기본적으로 커밋되지 않으므로 변경 내용을 커밋해야 합니다.

> [!TIP]
> XML 문서가 SAP 시스템에서 구성 된 유효성 검사 규칙을 따르는지 확인 합니다. 예를 들어이 샘플 문서에서 bank 키 ()는 `<BANK_KEY>` 미국의 ABA 수 라고도 하는 은행 라우팅 번호 여야 합니다.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

다음 예에서는에 대 한 값을 사용 하 여 은행 라우팅 번호를 사용 하는 은행에 대 한 세부 정보를 가져옵니다 `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>IDoc 요청에 대한 XML 샘플

일반 SAP IDoc XML 스키마를 생성 하려면 **Sap Logon** 응용 프로그램 및 T-코드를 사용 `WE-60` 합니다. GUI를 통해 SAP 설명서에 액세스 하 고 IDoc 유형 및 확장을 위해 XSD 형식으로 XML 스키마를 생성 합니다. 일반적인 SAP 형식 및 페이로드 및 기본 제공 대화 상자에 대 한 설명은 [sap 설명서](https://help.sap.com/viewer/index)를 참조 하세요.

이 예제에서는 루트 노드 및 네임 스페이스를 선언 합니다. 샘플 코드의 URI는 `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` 다음 구성을 선언 합니다.

* `/IDoc` 모든 IDocs에 대 한 루트 메모입니다.
* `/3` 공통 세그먼트 정의의 레코드 형식 버전입니다.
* `/ORDERS05` 은 (는) IDoc 유형입니다.
* `//` 는 IDoc 확장이 없기 때문에 빈 세그먼트입니다.
* `/700` SAP 버전
* `/Send` SAP로 정보를 보내는 작업입니다.

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

노드를 반복 `idocData` 하 여 단일 호출로 IDocs 일괄 처리를 보낼 수 있습니다. 아래 예제에는 하나의 컨트롤 레코드, `EDI_DC40` 및 여러 데이터 레코드가 있습니다.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

다음 예제는 접두사를 사용 하는 샘플 IDoc 컨트롤 레코드입니다 `EDI_DC` . SAP 설치 및 IDoc 유형과 일치 하도록 값을 업데이트 해야 합니다. 예를 들어 IDoc 클라이언트 코드는이 아닐 수 있습니다 `800` . Sap 팀에 문의 하 여 SAP 설치에 올바른 값을 사용 하 고 있는지 확인 합니다.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

다음 예제는 일반 세그먼트가 있는 샘플 데이터 레코드입니다. 이 예에서는 SAP 날짜 형식을 사용 합니다. 강력한 형식의 문서는와 같은 네이티브 XML 날짜 형식을 사용할 수 있습니다 `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

다음 예는 그룹화 된 세그먼트가 있는 데이터 레코드입니다. 레코드에는 그룹 부모 노드 및 `E2EDKT1002GRP` 및를 포함 한 여러 자식 노드가 포함 되어 있습니다 `E2EDKT1002` `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

TRFC와 함께 사용할 IDoc 식별자를 만드는 것이 좋습니다. `tid`SAP CONNECTOR API에서 [Send idoc 작업](/connectors/sap/#send-idoc) 을 사용 하 여이 트랜잭션 식별자를 설정할 수 있습니다.

다음 예는 트랜잭션 식별자 또는를 설정 하는 다른 방법입니다 `tid` . 이 예에서는 마지막 데이터 레코드 세그먼트 노드와 IDoc 데이터 노드가 닫혀 있습니다. 그런 다음 GUID를 `guid` tRFC 식별자로 사용 하 여 중복 항목을 검색 합니다. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>HTTP 요청 트리거 추가

1. Azure Portal에서 Logic Apps 디자이너를 여는 빈 논리 앱을 만듭니다.

1. 검색 상자에서 필터로 `http request`을 입력합니다. **트리거** 목록에서 **HTTP 요청을 받을 때** 를 선택 합니다.

   ![HTTP 요청 트리거 추가](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 이제 논리 앱을 위한 엔드포인트 URL을 생성할 수 있도록 논리 앱을 저장합니다.
디자이너 도구 모음에서 **저장** 을 선택합니다.

   이제 엔드포인트 URL이 트리거에 표시됩니다. 예를 들면 다음과 같습니다.

   ![엔드포인트 URL 생성](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>SAP 작업을 추가 하 여 스키마 생성

1. Logic Apps 디자이너의 트리거 아래에서 **새 단계** 를 선택 합니다.

   ![논리 앱에 새 단계 추가](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 검색 상자에서 필터로 `sap`을 입력합니다. **작업** 목록에서 **스키마 생성** 을 선택 합니다.
  
   ![논리 앱에 "스키마 생성" 작업 추가](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   또는 **Enterprise** 탭을 선택 하 고 SAP 작업을 선택할 수 있습니다.

   ![Enterprise 탭에서 SAP 보내기 작업 선택](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 연결이 이미 있는 경우 다음 단계를 계속 진행 하 여 SAP 작업을 설정할 수 있습니다. 그러나 연결 정보를 입력 하 라는 메시지가 표시 되 면 온-프레미스 SAP 서버에 대 한 연결을 만들 수 있도록 정보를 제공 합니다.

   1. 연결 이름을 제공합니다.

   1. **데이터 게이트웨이** 섹션의 **구독** 에서 먼저 데이터 게이트웨이 설치에 대 한 Azure Portal에서 만든 데이터 게이트웨이 리소스에 대 한 Azure 구독을 선택 합니다. 
   
   1. **연결 게이트웨이** 아래에서 Azure의 데이터 게이트웨이 리소스를 선택 합니다.

   1. 연결에 대 한 정보를 계속 제공 합니다. **Logon 유형** 속성의 경우 속성이 **응용 프로그램 서버** 또는 **그룹** 으로 설정 되었는지 여부에 따라 단계를 수행 합니다.
   
      * **응용 프로그램 서버의** 경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 애플리케이션 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * **그룹** 의 경우 일반적으로 선택적으로 표시 되는 이러한 속성이 필요 합니다.

        ![SAP 메시지 서버 연결 만들기](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      기본적으로 강력한 형식 지정은 스키마에 대해 XML 유효성 검사를 수행 하 여 잘못 된 값을 확인 하는 데 사용 됩니다. 이 동작은 이전에 발생 한 문제를 검색 하는 데 도움이 될 수 있습니다. **안전 입력** 옵션은 이전 버전과의 호환성을 위해 사용할 수 있으며 문자열 길이만 확인 합니다. [안전 입력 옵션](#safe-typing)에 대해 자세히 알아보세요.

   1. 작업이 완료되면 **만들기** 를 선택합니다.

      Logic Apps는 연결을 설정 하 고 테스트 하 여 연결이 제대로 작동 하는지 확인 합니다.

1. 스키마를 생성할 아티팩트의 경로를 입력합니다.

   파일 선택기에서 SAP 작업을 선택할 수 있습니다.

   ![SAP 작업 선택](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   또는 작업을 수동으로 입력할 수 있습니다.

   ![SAP 작업을 수동으로 입력](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   두 개 이상의 아티팩트에 대한 스키마를 생성하려면 다음과 같이 각 아티팩트에 대한 SAP 작업 세부 정보를 제공합니다.

   ![새 항목 추가 선택](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![두 개의 항목 표시](media/logic-apps-using-sap-connector/schema-generator-example.png)

   SAP 동작에 대 한 자세한 내용은 [IDoc 작업에 대 한 메시지 스키마](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)를 참조 하세요.

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 디자이너 도구 모음에서 **실행** 을 선택 하 여 논리 앱에 대 한 실행을 트리거합니다.

1. 실행을 열고 **스키마 생성** 작업의 출력을 확인 합니다.

   출력은 지정된 메시지 목록에 대해 생성된 스키마를 보여줍니다.

### <a name="upload-schemas-to-an-integration-account"></a>통합 계정에 스키마 업로드

필요에 따라 생성된 스키마를 Blob, 스토리지, 통합 계정 등의 리포지토리에 다운로드하거나 저장할 수 있습니다. 통합 계정은 다른 XML 작업을 통해 최고의 환경을 제공하므로, 이 예제에서는 Azure Resource Manager 커넥터를 사용하여 동일한 논리 앱의 통합 계정에 스키마를 업로드하는 방법을 보여줍니다.

1. Logic Apps 디자이너의 트리거 아래에서 **새 단계** 를 선택 합니다.

1. 검색 상자에서 필터로 `Resource Manager`을 입력합니다. **리소스 만들기 또는 업데이트를** 선택 합니다.

   ![Azure Resource Manager 작업 선택](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Azure 구독, Azure 리소스 그룹 및 통합 계정을 포함 하 여 작업에 대 한 세부 정보를 입력 합니다. 필드에 SAP 토큰을 추가 하려면 해당 필드의 상자 내부를 클릭 하 고 나타나는 동적 콘텐츠 목록에서 선택 합니다.

   1. **새 매개 변수 추가** 목록을 열고 **위치** 및 **속성** 필드를 선택 합니다.

   1. 이 예에 표시 된 대로 이러한 새 필드에 대 한 세부 정보를 제공 합니다.

      ![Azure Resource Manager 작업의 세부 정보 입력](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP **스키마 생성** 작업은 스키마를 컬렉션으로 생성하므로, 디자이너가 자동으로 **For each** 루프를 작업에 추가합니다. 다음은 이 작업이 어떻게 나타나는지 보여주는 예제입니다.

   !["for each" 루프를 사용하는 Azure Resource Manager 작업](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > 스키마에는 base64 인코딩 형식이 사용됩니다. 통합 계정에 스키마를 업로드하려면 `base64ToString()` 함수를 사용하여 디코딩해야 합니다. 다음은 `"properties"` 요소의 코드를 보여주는 예제입니다.
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

### <a name="test-your-logic-app"></a>논리 앱 테스트

1. 디자이너 도구 모음에서 **실행** 을 선택 하 여 논리 앱을 수동으로 트리거합니다.

1. 성공적으로 실행 된 후 통합 계정으로 이동 하 여 생성 된 스키마가 있는지 확인 합니다.

## <a name="enable-secure-network-communications"></a>보안 네트워크 통신 사용

시작 하기 전에, 데이터 게이트웨이를 사용 하 고 논리 앱이 다중 테 넌 트 Azure에서 실행 되는 경우에만 적용 되는 이전에 나열 된 [필수 구성 요소](#prerequisites)를 충족 하는지 확인 합니다.

* SAP 시스템과 동일한 네트워크에 있는 컴퓨터에 온-프레미스 데이터 게이트웨이가 설치 되어 있는지 확인 합니다.

* SSO의 경우 데이터 게이트웨이는 SAP 사용자에 매핑된 사용자로 실행 됩니다.

* 추가 보안 기능을 제공 하는 SNC 라이브러리는 데이터 게이트웨이와 동일한 컴퓨터에 설치 됩니다. 몇 가지 예로는 [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS 및 NTLM이 있습니다.

   SAP 시스템에 대 한 요청에 대해 SNC를 사용 하도록 설정 하려면 SAP 연결에서 **Snc 사용** 확인란을 선택 하 고 다음 속성을 제공 합니다.

   ![연결에서 SAP SNC 구성](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 속성 | 설명 |
   |----------| ------------|
   | **SNC 라이브러리 경로** | NCo 설치 위치나 절대 경로를 기준으로 하는 SNC 라이브러리 이름 또는 경로입니다. 예를 들면 `sapsnc.dll` 또는 `.\security\sapsnc.dll` 또는 `c:\security\sapsnc.dll` 입니다. |
   | **SNC SSO** | SNC를 통해 연결 하는 경우 SNC id는 일반적으로 호출자를 인증 하는 데 사용 됩니다. 또 다른 옵션은를 재정의 하 여 호출자를 인증 하는 데 사용자 및 암호 정보를 사용할 수 있지만 줄은 여전히 암호화 되도록 하는 것입니다. |
   | **SNC 내 이름** | 대부분의 경우이 속성은 생략할 수 있습니다. 설치 된 SNC 솔루션은 일반적으로 자체 SNC 이름을 알고 있습니다. 여러 id를 지 원하는 솔루션에 대해서만이 특정 대상 또는 서버에 사용할 id를 지정 해야 할 수 있습니다. |
   | **SNC 파트너 이름** | 백 엔드 SNC의 이름입니다. |
   | **SNC 보호 품질** | 이 특정 대상 또는 서버의 SNC 통신에 사용할 서비스의 품질입니다. 기본값은 백 엔드 시스템에 의해 정의 됩니다. 최 댓 값은 SNC에 사용 되는 보안 제품에 의해 정의 됩니다. |
   |||

   > [!NOTE]
   > 환경 변수 SNC_LIB 설정 하지 말고 데이터 게이트웨이와 SNC 라이브러리가 있는 컴퓨터에서 SNC_LIB_64 합니다. 설정 되 면 커넥터를 통해 전달 되는 SNC 라이브러리 값 보다 우선적으로 적용 됩니다.

## <a name="safe-typing"></a>안전 입력

기본적으로 SAP 연결을 만들 때 강력한 형식화는 스키마에 대해 XML 유효성 검사를 수행 하 여 잘못 된 값을 확인 하는 데 사용 됩니다. 이 동작은 이전에 발생 한 문제를 검색 하는 데 도움이 될 수 있습니다. **안전 입력** 옵션은 이전 버전과의 호환성을 위해 사용할 수 있으며 문자열 길이만 확인 합니다. **안전 입력** 을 선택 하는 경우 SAP에서 DATS TYPE 및는 해당 하는 XML이 아닌 문자열로 처리 됩니다 (여기서) `xs:date` `xs:time` `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . 안전 형식 지정은 모든 스키마 생성에 대 한 동작, "보낸 사람" 페이로드에 대 한 전송 메시지, "received" 응답 및 트리거 모두에 영향을 줍니다. 

강력한 형식화를 사용 하는 경우 (**안전 입력** 은 사용 하지 않도록 설정 됨) 스키마는 DATS 및 대상 s 형식을 보다 간단한 XML 형식으로 매핑합니다.

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

강력한 형식을 사용 하 여 메시지를 전송 하는 경우 DATS 및의 응답은 일치 하는 XML 형식 형식을 준수 합니다.

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

**안전 입력** 을 사용 하도록 설정 하면 스키마는 DATS 및 대상 s 유형을 길이가 제한 된 XML 문자열 필드에 매핑합니다. 예를 들면 다음과 같습니다.

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

**안전 입력** 을 사용 하도록 설정 하 여 메시지를 보낼 때 DATS 및의 응답은 다음 예제와 같습니다.

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>고급 시나리오

### <a name="change-language-headers"></a>언어 헤더 변경

Logic Apps에서 SAP에 연결 하는 경우 연결에 대 한 기본 언어는 영어입니다. 인바운드 요청과 함께 [표준 HTTP 헤더 `Accept-Language` 를](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) 사용 하 여 연결에 대 한 언어를 설정할 수 있습니다.

> [!TIP]
> 대부분의 웹 브라우저는 `Accept-Language` 사용자의 설정에 따라 헤더를 추가 합니다. Logic Apps designer에서 새 SAP 연결을 만들 때 웹 브라우저에서이 헤더를 적용 합니다. 웹 브라우저의 기본 설정 언어로 SAP 연결을 만들지 않으려면 웹 브라우저의 설정을 업데이트 하 여 기본 언어를 사용 하거나 Logic Apps 디자이너 대신 Azure Resource Manager를 사용 하 여 SAP 연결을 만듭니다. 

예를 들어 `Accept-Language` **HTTP 요청** 트리거를 사용 하 여 헤더가 포함 된 요청을 논리 앱에 보낼 수 있습니다. 논리 앱의 모든 작업은 헤더를 수신 합니다. 그런 다음 SAP는 BAPI 오류 메시지와 같은 시스템 메시지에 지정 된 언어를 사용 합니다.

논리 앱에 대 한 SAP 연결 매개 변수에 language 속성이 없습니다. 따라서 헤더를 사용 하는 경우 `Accept-Language` 다음과 같은 오류가 발생할 수 있습니다. **계정 정보 및/또는 사용 권한을 확인 하 고 다시 시도 하세요.** 이 경우 SAP 구성 요소의 오류 로그를 확인 합니다. 이 오류는 실제로 헤더를 사용 하는 SAP 구성 요소에서 발생 하므로 다음 오류 메시지 중 하나가 나타날 수 있습니다.

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>명시적으로 트랜잭션 확인

Logic Apps에서 SAP로 트랜잭션을 보낼 때이 exchange는 SAP 문서 [트랜잭션 RFC 서버 프로그램](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)에 설명 된 대로 두 단계로 이루어집니다. 기본적으로 **SAP로 보내기** 작업은 한 번의 호출로 함수 전송 및 트랜잭션 확인에 대 한 단계를 모두 처리 합니다. SAP 커넥터는 이러한 단계를 분리 하는 옵션을 제공 합니다. 자동으로 트랜잭션을 확인 하는 대신 IDoc를 보낼 수 있으며, 명시적 **트랜잭션 ID 확인** 작업을 사용할 수 있습니다.

트랜잭션 ID 확인을 분리 하는이 기능은 네트워크 문제와 같은 원인으로 인해 오류가 발생할 수 있는 시나리오와 같이 SAP에서 트랜잭션을 복제 하지 않으려는 경우에 유용 합니다. 트랜잭션 ID를 별도로 확인 하 여 트랜잭션은 SAP 시스템에서 한 번만 완료 됩니다.

이 패턴을 보여 주는 예제는 다음과 같습니다.

1. 빈 논리 앱을 만들고 HTTP 트리거를 추가 합니다.

1. SAP 커넥터에서 **SEND IDOC** 동작을 추가 합니다. SAP 시스템에 보내는 IDoc에 대 한 세부 정보를 제공 합니다.

1. 별도의 단계에서 트랜잭션 ID를 명시적으로 확인 하려면 **TID 확인** 필드에서 **아니요** 를 선택 합니다. 선택적 **트랜잭션 ID GUID** 필드의 경우 값을 수동으로 지정 하거나 커넥터에서이 guid를 자동으로 생성 하 여 SEND idoc 동작의 응답에 반환 하도록 할 수 있습니다.

   ![IDOC 동작 속성 보내기](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 트랜잭션 ID를 명시적으로 확인 하려면 **트랜잭션 Id 확인** 작업을 추가 하 여 중복 된 [IDOCS를 SAP로 보내지 않도록](#avoid-sending-duplicate-idocs)합니다. 동적 콘텐츠 목록이 표시 되도록 **트랜잭션 ID** 상자 내부를 클릭 합니다. 해당 목록에서 **SEND IDOC** 동작에서 반환 된 **트랜잭션 ID** 값을 선택 합니다.

   ![트랜잭션 ID 작업 확인](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   이 단계가 실행 된 후에는 SAP 커넥터 쪽 및 SAP 시스템 쪽에서 현재 트랜잭션이 완료로 표시 됩니다.

#### <a name="avoid-sending-duplicate-idocs"></a>중복 된 IDocs를 보내지 않습니다.

논리 앱에서 SAP로 중복 된 IDocs를 전송 하는 데 문제가 발생 하는 경우 다음 단계를 수행 하 여 Idocs 트랜잭션 식별자로 사용할 문자열 변수를 만듭니다. 이 트랜잭션 식별자를 만들면 일시적인 중단, 네트워크 문제 또는 승인 손실 등의 문제가 발생 하는 경우 네트워크 전송이 중복 되지 않습니다.

> [!NOTE]
> SAP 시스템은 지정 된 시간 이후 트랜잭션 식별자를 잊어버리거나 기본적으로 24 시간을 제거 합니다. 따라서 ID 또는 GUID를 알 수 없는 경우 SAP는 트랜잭션 식별자를 확인 하지 못합니다.
> 트랜잭션 식별자에 대 한 확인이 실패 하는 경우이 오류는 sap 시스템의 통신가 확인을 승인 하기 전에 실패 했음을 나타냅니다.

1. Logic Apps 디자이너에서 작업 **Initialize 변수** 를 논리 앱에 추가 합니다. 

1. Action **Initialize 변수에** 대 한 편집기에서 다음 설정을 구성 합니다. 그런 다음 변경 내용을 저장 합니다.

    1. **이름** 에 변수의 이름을 입력 합니다. 예: `IDOCtransferID`

    1. **유형** 에 대해 **문자열** 을 변수 유형으로 선택 합니다.

    1. **값** 에 대해 텍스트 상자에 **초기 값 입력** 을 선택 하 여 동적 콘텐츠 메뉴를 엽니다. **식** 탭을 선택 합니다. 함수 목록에서 함수를 입력 합니다 `guid()` . 그런 다음 **확인** 을 선택 하 여 변경 내용을 저장 합니다. 이제 **값** 필드가 GUID를 생성 하는 `guid()` 함수로 설정 됩니다.

1. **변수 초기화** 작업 후에 **Send idoc** 작업을 추가 합니다.

1. 작업 **송신 IDOC** 에 대 한 편집기에서 다음 설정을 구성 합니다. 그런 다음 변경 내용을 저장 합니다.

    1. **Idoc 유형에** 서 메시지 유형을 선택 하 고, **입력 idoc 메시지** 에 대해 메시지를 지정 합니다.

    1. **Sap 릴리스 버전** 의 경우 sap 구성 값을 선택 합니다.

    1. **레코드 종류 버전** 에서 SAP 구성 값을 선택 합니다.

    1. **TID 확인** 에 대해 **아니요** 를 선택 합니다.

    1. **새 매개 변수 목록**  >  **트랜잭션 ID GUID** 추가를 선택 합니다. 텍스트 상자를 선택 하 여 동적 콘텐츠 메뉴를 엽니다. **변수** 탭에서 사용자가 만든 변수의 이름을 선택 합니다. 예: `IDOCtransferID`

1. Action **SEND IDOC** 의 제목 표시줄에서 **...**  >  를 선택 합니다. **설정**. **재시도 정책** 에 대해 **기본** 완료를 선택 하는 것이 좋습니다 &gt; . 그러나 특정 요구에 맞게 사용자 지정 정책을 구성할 수 있습니다. 사용자 지정 정책의 경우 일시적인 네트워크 중단을 극복 하기 위해 하나 이상의 재시도를 구성 하는 것이 좋습니다.

1. **IDOC** 작업을 보낸 후 **트랜잭션 ID 확인** 작업을 추가 합니다.

1. **트랜잭션 ID 확인** 작업의 편집기에서 다음 설정을 구성 합니다. 그런 다음 변경 내용을 저장 합니다.

    1. **트랜잭션 ID** 에 변수 이름을 다시 입력 합니다. 예: `IDOCtransferID`

1. 필요에 따라 테스트 환경에서 중복 제거의 유효성을 검사 합니다. 이전 단계에서 사용한 것과 동일한 **트랜잭션 ID** GUID를 사용 하 여 **Send idoc** 작업을 반복 합니다. 동일한 IDoc를 두 번 보내면 SAP에서 tRFC 호출의 중복을 식별할 수 있는지 확인 하 고 단일 인바운드 IDoc 메시지에 대 한 두 호출을 확인할 수 있습니다.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

다음은 관리 되는 (ISE) SAP 커넥터에 대 한 현재 알려진 문제 및 제한 사항입니다. 

* 일반적으로 SAP 트리거는 데이터 게이트웨이 클러스터를 지원 하지 않습니다. 일부 장애 조치 (failover)의 경우 SAP 시스템과 통신 하는 데이터 게이트웨이 노드가 활성 노드와 다를 수 있으며이로 인해 예기치 않은 동작이 발생할 수 있습니다.

  * 보내기 시나리오의 경우 장애 조치 (failover) 모드의 데이터 게이트웨이 클러스터가 지원 됩니다. 

  * 상태 저장 SAP 작업에서는 부하 분산 모드의 데이터 게이트웨이 클러스터가 지원 되지 않습니다. 이러한 작업에는 **상태 저장 세션 만들기**, **커밋 bapi 트랜잭션**, **롤백 bapi 트랜잭션**, **상태 저장 세션 닫기** 및 **세션 ID** 값을 지정 하는 모든 작업이 포함 됩니다. 상태 저장 통신은 동일한 데이터 게이트웨이 클러스터 노드에 유지 되어야 합니다. 

  * 상태 저장 SAP 동작의 경우 클러스터 되지 않은 모드 또는 장애 조치 (failover) 용으로 설정 된 클러스터에서 데이터 게이트웨이를 사용 합니다.

* 현재 SAP 커넥터는 SAP 라우터 문자열을 지원하지 않습니다. 온-프레미스 데이터 게이트웨이가 연결하려는 SAP 시스템과 동일한 LAN에 있어야 합니다.


## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명 된 대로 트리거, 작업 및 제한과 같은이 커넥터에 대 한 자세한 기술 정보는 [커넥터의 참조 페이지](/connectors/sap/)를 참조 하세요. Logic Apps에 대 한 추가 설명서는 다음 작업을 위해 제공 됩니다.

* [BAPI 호출](#call-bapi-action)

* [IDOC 보내기](#send-idoc-action)

> [!NOTE]
> [Ise (통합 서비스 환경](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md))의 논리 앱의 경우이 커넥터의 ise 레이블 버전은 [ise 메시지 제한을](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) 대신 사용 합니다.

### <a name="call-bapi-action"></a>BAPI 작업 호출

[Bapi ( `CallBapi` ) 호출](
https://docs.microsoft.com/connectors/sap/#call-bapi-(preview)) 작업은 SAP 서버에서 bapi 메서드를 호출 합니다. 

호출에서 다음 매개 변수를 사용 해야 합니다. 

* **비즈니스 개체** ( `businessObject` )-검색 가능한 드롭다운 메뉴입니다.

* **메서드** ( `method` )- **비즈니스 개체** 를 선택한 후 사용 가능한 메서드를 채웁니다. 사용 가능한 방법은 선택한 **비즈니스 개체** 에 따라 달라 집니다.

* **입력 bapi 매개** 변수 ( `body` ). 여기서는 호출에 대 한 bapi 메서드 입력 매개 변수 값이 포함 된 XML 문서를 호출 하거나 bapi 매개 변수를 포함 하는 저장소 blob의 URI를 호출 합니다.

BAPI 호출 작업을 사용 하는 방법에 대 한 자세한 예제는 [bapi 요청의 XML 샘플](#xml-samples-for-bapi-requests)을 참조 하세요.

> [!TIP]
> Logic Apps designer를 사용 하 여 BAPI 요청을 편집 하는 경우 다음 검색 함수를 사용할 수 있습니다. 
> 
> * 디자이너에서 개체를 선택 하 여 사용 가능한 메서드의 드롭다운 메뉴를 표시 합니다.
> * BAPI API 호출에서 제공 되는 검색 가능 목록을 사용 하 여 키워드별로 비즈니스 개체 유형을 필터링 합니다.

### <a name="send-idoc-action"></a>IDoc 동작 보내기

[Send idoc ( `SendIDoc` )](/connectors/sap/) 작업은 IDOC 메시지를 SAP 서버에 보냅니다.

호출에서 다음 매개 변수를 사용 해야 합니다. 

* 검색 가능한 드롭다운 메뉴 인 선택적 확장 ()이 **포함 된 Idoc 형식** 입니다 `idocType` .

    * 선택적 매개 변수 **SAP 릴리스 버전** ( `releaseVersion` )은 idoc 유형을 선택한 후 값을 채우고 선택한 idoc 유형에 따라 달라 집니다.

* Idoc 페이로드를 포함 하는 XML 문서를 호출 하는 **입력 idoc 메시지** ( `body` ) 또는 idoc XML 문서가 포함 된 저장소 blob의 URI입니다. 이 문서는 WE60 IDoc 설명서에 따라 SAP IDOC XML 스키마를 준수 해야 하며, 일치 하는 SAP IDoc 동작 URI에 대해 생성 된 스키마를 준수 해야 합니다.

Send IDoc 작업을 사용 하는 방법에 대 한 자세한 예는 [SAP 서버에 idoc 메시지를 보내는 연습](#send-idoc-messages-to-sap-server)을 참조 하세요.

선택적 매개 변수 **확인 TID** ()를 사용 하는 방법에 대해서는 `confirmTid` [트랜잭션을 명시적으로 확인](#confirm-transaction-explicitly)하는 연습을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Logic Apps에서 온 [-프레미스 시스템에 연결](../logic-apps/logic-apps-gateway-connection.md) 합니다.

* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)를 사용 하 여 다른 메시지 작업의 유효성을 검사 하 고 변환 하 고 사용 하는 방법에 대해 알아봅니다.

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.