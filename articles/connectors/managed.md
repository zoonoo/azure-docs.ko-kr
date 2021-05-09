---
title: Azure Logic Apps용 관리형 커넥터
description: Microsoft에서 관리하는 트리거 및 작업을 사용하여 Azure Logic Apps를 통해 다른 앱, 데이터, 서비스 및 시스템을 통합하는 자동화된 워크플로를 만듭니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 857c0e41f52c99d83142b3db7471b51b55316b5e
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108316176"
---
# <a name="managed-connectors-for-logic-apps"></a>Logic Apps용 관리형 커넥터

[관리형 커넥터](apis-list.md)는 [내장된 트리거 및 작업](built-in.md)을 사용할 수 없는 다른 서비스 및 시스템에 액세스하는 방법을 제공합니다. 이러한 트리거와 작업을 사용하여 데이터, 앱, 클라우드 기반 서비스 및 온-프레미스 시스템을 통합하는 워크플로를 만들 수 있습니다. 이러한 커넥터는 기본 제공 트리거와 작업에 비해 일반적으로 Azure Blob Storage, Office 365, SQL, Salesforce 또는 SFTP 서버와 같은 특정 서비스 또는 시스템에 연결됩니다. Microsoft에서 관리하고 Azure에서 호스트되는 관리형 커넥터는 일반적으로 먼저 워크플로에서 연결을 만들고 ID를 인증해야 합니다. 반복 기반 트리거와 웹후크 기반 트리거를 모두 사용할 수 있으므로 반복 기반 트리거를 사용하는 경우 [반복 동작 개요](apis-list.md#recurrence-behavior)를 검토하세요.

일부 서비스의 경우에는 Azure Service Bus, Azure Functions, SQL, AS2 Logic Apps 등의 시스템 및 프로토콜에도 기본 제공 버전이 제공됩니다. 개수와 범위는 다중 테넌트 논리 앱을 만드는지 또는 단일 테넌트 논리 앱을 만드는지에 따라 달라집니다. 일부 경우에는 기본 제공 버전과 관리형 커넥터 버전을 모두 사용할 수 있습니다. 대부분의 경우 기본 제공 버전은 더 나은 성능, 기능, 가격 책정 등을 제공합니다. 예를 들어 [AS2 프로토콜을 사용하여 B2B 메시지를 교환](../logic-apps/logic-apps-enterprise-integration-as2.md)하려면 더 이상 사용되지 않는 관리형 커넥터 버전에서만 사용할 수 있는 추적 기능이 필요하지 않다면 기본 제공 버전을 선택합니다.

Logic Apps에 대한 일부 관리형 커넥터는 여러 하위 범주에 속합니다. 예를 들어 SAP 커넥터는 [엔터프라이즈 커넥터](#enterprise-connectors)이면서 [온-프레미스 커넥터](#on-premises-connectors)입니다.

* [표준 커넥터](#standard-connectors)는 Azure Blob Storage, Office 365, SharePoint, Salesforce, Power BI, OneDrive 등과 같은 서비스에 대한 액세스를 제공합니다.
* [온-프레미스 커넥터](#on-premises-connectors)는 SQL Server, SharePoint Server, SAP, Oracle DB, 파일 공유 등과 같은 온-프레미스 시스템에 대한 액세스를 제공합니다.
* [통합 계정 커넥터](#integration-account-connectors)를 사용하면 AS2, EDIFACT 및 X12 프로토콜을 사용하여 XML 변환 및 유효성 검사, 플랫 파일 인코딩 및 디코딩, B2B(Business-to-Business) 메시지 처리를 수행할 수 있습니다. 

## <a name="standard-connectors"></a>표준 커넥터

Azure Logic Apps는 이러한 서비스 및 시스템을 사용하여 자동화된 워크플로를 빌드하기 위한 널리 사용되는 표준 커넥터를 제공합니다. 일부 표준 커넥터는 [온-프레미스 시스템](#on-premises-connectors) 또는 [통합 계정](#integration-account-connectors)도 지원합니다.

일부 Logic Apps Standard 커넥터는 [온-프레미스 시스템](#on-premises-connectors) 또는 [통합 계정](#integration-account-connectors)을 지원합니다.

:::row:::
    :::column:::
        [![Logic Apps의 Azure Service Bus 관리형 커넥터 아이콘][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]
        \
        \
        Logic Apps에서 가장 일반적으로 사용되는 커넥터를 사용하여 비동기 메시지, 세션 및 토픽 구독을 관리합니다.
    :::column-end:::
    :::column:::
        [![Logic Apps의 SQL Server 관리형 커넥터 아이콘][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        온-프레미스의 SQL Server 또는 클라우드의 Azure SQL Database에 연결하여 레코드를 관리하고 저장 프로시저를 실행하거나 쿼리를 수행할 수 있습니다.
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Blog Storage 관리형 커넥터 아이콘][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blog Storage**][azure-blob-storage-doc]
        \
        \
        Azure Storage 계정에 연결하여 Blob 콘텐츠를 만들고 관리할 수 있습니다.
    :::column-end:::
    :::column:::
        [![Logic Apps의 Office 365 Outlook 관리형 커넥터 아이콘][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        회사 또는 학교 이메일 계정에 연결하여 이메일, 작업, 일정 이벤트 및 회의, 연락처, 요청 등을 만들고 관리할 수 있습니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 STFP-SSH 관리형 커넥터 아이콘][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        SSH를 사용하여 인터넷에서 액세스할 수 있는 SFTP 서버에 연결하여 파일 및 폴더를 사용할 수 있습니다.
    :::column-end:::
    :::column:::
        [![Logic Apps의 SharePoint Online 관리형 커넥터 아이콘][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        SharePoint Online에 연결하여 파일, 첨부 파일, 폴더 등을 관리할 수 있습니다.
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Queues 관리형 커넥터 아이콘][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure 큐**][azure-queues-doc]
        \
        \
        Azure Storage 계정에 연결하여 큐와 메시지를 만들고 관리할 수 있습니다.
    :::column-end:::
    :::column:::
        [![Logic Apps의 FTP 관리형 커넥터 아이콘][ftp-icon]][ftp-doc]
        \
        \
        [**FTP**][ftp-doc]
        \
        \
        인터넷에서 액세스할 수 있는 FTP 서버에 연결하여 파일 및 폴더를 사용할 수 있습니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 File System 관리형 커넥터 아이콘][file-system-icon]][file-system-doc]
        \
        \
        [**파일 시스템**][file-system-doc]
        \
        \
        파일을 만들고 관리할 수 있도록 온-프레미스 파일 공유에 연결합니다.
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Event Hubs 관리형 커넥터 아이콘][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs**][azure-event-hubs-doc]
        \
        \
        Event Hub를 통해 이벤트를 사용하고 게시합니다. 예를 들어 Event Hubs를 사용하여 논리 앱에서 출력을 가져온 다음, 해당 출력을 실시간 분석 공급자에게 보냅니다.
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Event Grid 관리형 커넥터 아이콘][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid**][azure-event-grid-doc]
        \
        \
        Azure 리소스 또는 타사 리소스가 변경되는 경우와 같이 Event Grid에서 게시한 이벤트를 모니터링합니다.
    :::column-end:::
    :::column:::
        [![Logic Apps의 Salesforce 관리형 커넥터 아이콘][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        Salesforce 계정에 연결하여 레코드, 작업, 개체 등의 항목을 만들고 관리할 수 있습니다.
    :::column-end:::
:::row-end:::


## <a name="on-premises-connectors"></a>온-프레미스 커넥터

온-프레미스 시스템에 대한 연결을 만들려면 먼저 [온-프레미스 데이터 게이트웨이를 다운로드, 설치 및 설정][gateway-doc]해야 합니다. 이 게이트웨이는 필요한 네트워크 인프라를 설정하지 않고도 보안 통신 채널을 제공합니다. 

다음 커넥터는 Logic Apps가 온-프레미스 시스템의 데이터 및 리소스에 액세스하기 위해 제공하여 일반적으로 사용되는 몇 가지 [표준 커넥터](#standard-connectors)입니다. 온-프레미스 커넥터 목록은 [지원되는 데이터 원본](../logic-apps/logic-apps-gateway-connection.md#supported-connections)을 참조하세요.

:::row:::
    :::column:::
        [![Logic Apps의 Biztalk Server 온-프레미스 커넥터 아이콘][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 File System 온-프레미스 커넥터 아이콘][file-system-icon]][file-system-doc]
        \
        \
        [**파일 시스템**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 IBM Db2 온-프레미스 커넥터 아이콘][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM Db2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 IBM Informix 온-프레미스 커넥터 아이콘][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 MySQL 온-프레미스 커넥터 아이콘][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Oracle DB 온-프레미스 커넥터 아이콘][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 PostgreSQL 온-프레미스 커넥터 아이콘][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 SharePoint Server 온-프레미스 커넥터 아이콘][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 SQL Server 온-프레미스 커넥터 아이콘][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Teradata 온-프레미스 커넥터 아이콘][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-connectors"></a>통합 계정 커넥터

통합 계정 커넥터는 특히 Azure Logic Apps에서 [B2B(기업 간) 통신 시나리오](../logic-apps/logic-apps-enterprise-integration-overview.md)를 지원합니다. [통합 계정을 만들고](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 거래 파트너, 계약, 맵 및 스키마와 같은 B2B 아티팩트를 정의한 후 통합 계정 커넥터를 사용하여 메시지를 인코딩 및 디코딩하고 콘텐츠를 변환할 수 있습니다.

예를 들어 Microsoft BizTalk Server를 사용하는 경우 [BizTalk Server 온-프레미스 커넥터](#on-premises-connectors)를 사용하여 워크플로에서 연결을 만들 수 있습니다. 그런 다음, 통합 계정 커넥터를 사용하여 워크플로에서 BizTalk와 비슷한 작업을 확장하거나 수행할 수 있습니다.

> [!NOTE]
> 통합 계정 커넥터를 사용하려면 먼저 [논리 앱을 통합 계정에 연결](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)해야 합니다.


:::row:::
    :::column:::
        [![Logic Apps의 AS2 디코딩 작업 아이콘][as2-icon]][as2-doc]
        \
        \
        [**AS2 디코딩**][as2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 AS2 인코딩 작업 아이콘][as2-icon]][as2-doc]
        \
        \
        [**AS2 인코딩**][as2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 EDIFACT 디코딩 작업 아이콘][edifact-icon]][edifact-decode-doc]
        \
        \
        [**EDIFACT 디코딩**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 EDIFACT 인코딩 작업 아이콘][edifact-icon]][edifact-encode-doc]
        \
        \
        [**EDIFACT 인코딩**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 플랫 파일 디코딩 작업 아이콘][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**플랫 파일 디코딩**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 플랫 파일 인코딩 작업 아이콘][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**플랫 파일 인코딩**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 통합 계정 작업 아이콘][integration-account-icon]][integration-account-doc]
        \
        \
        [**통합 계정**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Liquid 변환 작업 아이콘][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Liquid 변환**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 X12 디코딩 작업 아이콘][x12-icon]][x12-decode-doc]
        \
        \
        [**X12 디코딩**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 X12 인코딩 작업 아이콘][x12-icon]][x12-encode-doc]
        \
        \
        [**X12 인코딩**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 XML 변환 작업 아이콘][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**XML 변환**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 XML 유효성 검사 작업 아이콘][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML 유효성 검사**][xml-validate-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>엔터프라이즈 커넥터

다음 커넥터는 추가 비용을 위해 엔터프라이즈 시스템에 대한 액세스를 제공합니다.

:::row:::
    :::column:::
        [![Logic Apps의 IBM 3270 엔터프라이즈 커넥터 아이콘][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** 엔터프라이즈 커넥터][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 IBM MQ 엔터프라이즈 커넥터 아이콘][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** 엔터프라이즈 커넥터][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 SAP 엔터프라이즈 커넥터 아이콘][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** 엔터프라이즈 커넥터][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::


## <a name="ise-connectors"></a>ISE 커넥터

ISE(통합 서비스 환경)에서 이러한 관리형 커넥터에는 다중 테넌트 버전과 다른 기능이 있는 [ISE 버전](apis-list.md#ise-and-connectors)도 있습니다.

> [!NOTE]
> ISE와 해당 커넥터에서 실행되는 논리 앱은 이러한 커넥터가 실행되는 위치에 관계 없이 고정 요금제 및 소비 기반 요금제를 따릅니다. 자세한 내용은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md) 및 [Logic Apps 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/logic-apps/)를 참조하세요.

:::row:::
    :::column:::
        [![Logic Apps의 AS2 ISE 커넥터 아이콘][as2-icon]][as2-doc]
        \
        \
        [**AS2** ISE][as2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Automation ISE 커넥터 아이콘][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Automation** ISE][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Blob Storage ISE 커넥터 아이콘][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob Storage** ISE][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Cosmos DB ISE 커넥터 아이콘][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** ISE][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 Azure Event Hubs ISE 커넥터 아이콘][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs** ISE][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Event Grid ISE 커넥터 아이콘][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid** ISE][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure File Storage ISE 커넥터 아이콘][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Azure File Storage** ISE][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Key Vault ISE 커넥터 아이콘][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** ISE][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 Azure Monitor Logs ISE 커넥터 아이콘][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Azure Monitor Logs** ISE][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Service Bus ISE 커넥터 아이콘][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus** ISE][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Synapse Analytics ISE 커넥터 아이콘][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure Synapse Analytics** ISE][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 Azure Table Storage ISE 커넥터 아이콘][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Azure Table Storage** ISE][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 Azure Queues ISE 커넥터 아이콘][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Azure Queues** ISE][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 EDIFACT ISE 커넥터 아이콘][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** ISE][edifact-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 File System ISE 커넥터 아이콘][file-system-icon]][file-system-doc]
        \
        \
        [**File System** ISE][file-system-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 FTP ISE 커넥터 아이콘][ftp-icon]][ftp-doc]
        \
        \
        [**FTP** ISE][ftp-doc]
    :::column-end:::
:::row-end:::   
:::row:::
    :::column:::
        [![Logic Apps의 IBM 3270 ISE 커넥터 아이콘][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** ISE][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 IBM DB2 ISE 커넥터 아이콘][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** ISE][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 IBM MQ ISE 커넥터 아이콘][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** ISE][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 SAP ISE 커넥터 아이콘][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** ISE][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Logic Apps의 SFTP-SSH ISE 커넥터 아이콘][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** ISE][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 SMTP ISE 커넥터 아이콘][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** ISE][smtp-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 SQL Server ISE 커넥터 아이콘][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** ISE][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Logic Apps의 X12 ISE 커넥터 아이콘][x12-icon]][x12-doc]
        \
        \
        [**X12** ISE][x12-doc]
    :::column-end:::
:::row-end:::

자세한 내용은 다음 항목을 참조하세요.

* [Azure Logic Apps에서 Azure 가상 네트워크 리소스에 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Logic Apps에서 호출할 수 있는 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png


<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "클라우드 및 온-프레미스 인프라를 위한 자동화 작업 만들기 및 관리"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage 커넥터와 Blob 컨테이너의 파일 관리"
[azure-cosmos-db-doc]: /connectors/documentdb/ "문서 및 저장 프로시저에 액세스할 수 있도록 Azure Cosmos DB에 연결"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Azure 리소스 또는 타사 리소스가 변경되는 경우와 같이 Event Grid에서 게시한 이벤트 모니터링"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "논리 앱과 Event Hubs간에 이벤트를 수신하고 보낼 수 있도록 Azure Event Hubs에 연결"
[azure-file-storage-doc]: /connectors/azurefile/ "파일을 만들고 업데이트하고 가져오고 삭제할 수 있도록 Azure Storage 계정에 연결"
[azure-key-vault-doc]: /connectors/keyvault/ "비밀 및 키를 관리할 수 ​​있도록 Azure Key Vault에 연결"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Log Analytics 작업 영역 및 Application Insights 구성 요소에서 Azure Monitor 로그에 대해 쿼리 실행"
[azure-queues-doc]: /connectors/azurequeues/ "큐와 메시지를 만들고 관리할 수 있도록 Azure Storage 계정에 연결"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus 큐 및 항목에서 메시지를 보내고 Service Bus 큐 및 구독에서 메시지를 받습니다."
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "데이터를 볼 수 있도록 Azure Synapse Analytics에 연결"
[azure-table-storage-doc]: /connectors/azuretables/ "테이블 등을 만들고 업데이트하고 쿼리할 수 ​​있도록 Azure Storage 계정에 연결"
[biztalk-server-doc]: /connectors/biztalk/ "BizTalk Server에 연결하여 Azure Logic Apps와 나란히 BizTalk 기반 애플리케이션을 실행할 수 있음"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "온-프레미스 파일 시스템에 연결"
[ftp-doc]: ./connectors-create-api-ftp.md "파일 업로드, 가져오기, 삭제 등과 같은 FTP 태스크의 경우 FTP / FTPS 서버에 연결"
[github-doc]: ./connectors-create-api-github.md "GitHub에 연결 및 문제 추적"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Google 달력에 연결하고 달력을 관리할 수 있음"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Google Sheets에 연결하여 시트를 수정할 수 있음"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "작업을 관리할 수 ​​있도록 Google Tasks에 연결"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "IBM 메인프레임에서 3270 앱에 연결"
[ibm-db2-doc]: ./connectors-create-api-db2.md "클라우드 또는 온-프레미스에서 IBM DB2에 연결. 행 업데이트, 테이블 가져오기 등의 작업 수행"
[ibm-informix-doc]: ./connectors-create-api-informix.md "클라우드 또는 온-프레미스에서 Informix에 연결. 행 읽기, 테이블 나열 등의 작업 수행"
[ibm-mq-doc]: ./connectors-create-api-mq.md "온-프레미스 또는 Azure에서 IBM MQ에 연결하여 메시지 송수신"
[instagram-doc]: ./connectors-create-api-instagram.md "Instagram에 연결. 이벤트 트리거 또는 조치"
[mandrill-doc]: ./connectors-create-api-mandrill.md "통신을 위해 Mandrill에 연결"
[mysql-doc]: /connectors/mysql/ "데이터를 읽고 쓸 수 있도록 온-프레미스 MySQL 데이터베이스에 연결"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "직장 또는 학교 계정에 연결하여 이메일을 보내고 받고 캘린더 및 연락처를 관리하는 등의 작업을 할 수 있음"
[onedrive-doc]: ./connectors-create-api-onedrive.md "개인 Microsoft OneDrive에 연결하여 파일을 업로드, 삭제, 나열하는 등의 작업을 수행할 수 있음"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "비즈니스 Microsoft OneDrive에 연결하여 파일을 업로드, 삭제, 나열하는 등의 작업을 수행할 수 있음"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "행 추가, 삽입, 삭제 등을 수행할 수 있도록 Oracle 데이터베이스에 연결"
[outlook.com-doc]: ./connectors-create-api-outlook.md "이메일, 일정, 연락처 등을 관리할 수 ​​있도록 Outlook 사서함에 연결"
[postgre-sql-doc]: /connectors/postgresql/ "테이블에서 데이터를 읽을 수 있도록 PostgreSQL 데이터베이스에 연결"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Salesforce 계정에 연결. 계정, 잠재 고객, 영업 기회 등 관리"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "온-프레미스 SAP 시스템에 연결"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "SendGrid에 연결. 이메일 보내기 및 수신자 목록 관리"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "SSH를 사용하여 SFTP 계정에 연결. 파일 업로드, 가져오기, 삭제 등"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "SharePoint 온-프레미스 서버에 연결. 문서, 목록 항목 등 관리"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "SharePoint Online에 연결. 문서, 목록 항목 등 관리"
[slack-doc]: ./connectors-create-api-slack.md "Slack에 연결하고 Slack 채널에 메시지 게시"
[smtp-doc]: ./connectors-create-api-smtp.md "SMTP 서버에 연결 및 첨부 파일이 포함된 이메일 보내기"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "통신을 위해 SparkPost에 연결"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL Database 또는 SQL Server에 연결. SQL 데이터베이스 테이블에서 항목 생성, 업데이트, 가져오기 및 삭제"
[teradata-doc]: /connectors/teradata/ "Teradata 데이터베이스에 연결하여 테이블에서 데이터 읽기"
[twilio-doc]: ./connectors-create-api-twilio.md "Twilio에 연결. 메시지 보내기 및 받기, 사용 가능한 번호 받기, 수신 전화 번호 관리 등의 작업 수행"
[youtube-doc]: ./connectors-create-api-youtube.md "YouTube에 연결. 비디오 및 채널 관리"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIFACT 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "EDIFACT 프로토콜을 사용하는 메시지 디코딩"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIFACT 프로토콜을 사용하는 메시지 인코딩"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "통합 계정 아티팩트에 대한 메타데이터 관리"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Liquid 템플릿으로 JSON 변환"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "X12 프로토콜을 사용하는 메시지 디코딩"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 프로토콜을 사용하는 메시지 인코딩"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML 메시지 변환"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML 메시지 유효성 검사"


<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "온-프레미스 데이터 게이트웨이를 사용하여 논리 앱에서 온-프레미스 데이터 원본에 연결"



<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIFACT 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "EDIFACT 프로토콜을 사용하는 메시지 디코딩"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIFACT 프로토콜을 사용하는 메시지 인코딩"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "통합 계정 아티팩트에 대한 메타데이터 관리"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Liquid 템플릿으로 JSON 변환"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "X12 프로토콜을 사용하는 메시지 디코딩"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 프로토콜을 사용하는 메시지 인코딩"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML 메시지 변환"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML 메시지 유효성 검사"
