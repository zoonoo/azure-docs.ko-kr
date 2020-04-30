---
title: Azure Cosmos DB 에뮬레이터 인증서 내보내기
description: Windows 인증서 저장소를 사용 하지 않는 언어 및 런타임으로 개발할 때 TLS/SSL 인증서를 내보내고 관리 해야 합니다. 이 게시물에서는 단계별 지침을 제공합니다.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: c72dbf24df850d8b0f7e5f26a873b78f5664c9e0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200941"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Java, Python 및 Node.js에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기

[**에뮬레이터 다운로드**](https://aka.ms/cosmosdb-emulator)

Azure Cosmos DB 에뮬레이터는 TLS 연결 사용을 포함 하 여 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공 합니다. 이 게시물에서는 Windows 인증서 저장소와 통합 되지 않는 언어와 런타임에 사용 하기 위해 TLS/SSL 인증서를 내보내는 방법을 보여 줍니다. 여기에는 자체 [인증서 저장소](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) 를 사용 하는 Java, [소켓 래퍼](https://docs.python.org/2/library/ssl.html) 를 사용 하는 Python 및 [tlssocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)를 사용 하는 node.js가 포함 됩니다. 에뮬레이터에 대한 자세한 내용은 [Azure Cosmos DB 에뮬레이터를 사용하여 개발 및 테스트](./local-emulator.md)를 참조하세요.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 인증서 순환
> * TLS/SSL 인증서 내보내기
> * Java, Python 및 Node.js에서 인증서를 사용하는 방법 알아보기

## <a name="certification-rotation"></a>인증 회전

Azure Cosmos DB 로컬 에뮬레이터의 인증서는 에뮬레이터를 처음 실행할 때 생성되며, 두 개의 인증서가 있습니다. 하나는 로컬 에뮬레이터에 연결하는 데 사용되고, 다른 하나는 에뮬레이터 내에서 비밀을 관리하는 데 사용됩니다. 내보내려는 인증서는 "DocumentDBEmulatorCertificate"라는 이름의 연결 인증서입니다.

Windows 트레이에서 실행 중인 Azure Cosmos DB 에뮬레이터에서 아래와 같이 **데이터 다시 설정**을 클릭하여 두 인증서를 모두 다시 생성할 수 있습니다. 인증서를 다시 생성하여 Java 인증서 스토리지에 설치했거나 다른 곳에서 사용한 경우에는 이 인증서를 업데이트해야 합니다. 그렇지 않으면 애플리케이션이 더 이상 로컬 에뮬레이터에 연결되지 않습니다.

![Azure Cosmos DB 로컬 에뮬레이터 데이터 다시 설정](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Azure Cosmos DB TLS/SSL 인증서를 내보내는 방법

1. DocumentDbEmulatorCertificate를 실행 하 여 Windows 인증서 관리자를 시작 하 고 개인->인증서 폴더로 이동 하 여 이름이 **DocumentDbEmulatorCertificate**인 인증서를 엽니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 1단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. **세부 정보**를 클릭한 다음 **확인**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 2단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. **파일에 복사...** 를 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 3단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. **다음**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 4단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. **아니요, 프라이빗 키를 내보내지 않습니다.** 를 클릭한 후, **다음**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 5단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. **Base-64 encoded X.509 (.CER)** 를 클릭한 후 **다음**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 6단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. 인증서에 이름을 지정합니다. 여기서는 **documentdbemulatorcert**이며, **다음**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 7단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. **마침**을 클릭합니다.

    ![Azure Cosmos DB 로컬 에뮬레이터 내보내기 8단계](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Java에서 인증서를 사용하는 방법

Java 클라이언트를 사용하는 Java 애플리케이션 또는 MongoDB 애플리케이션을 실행하는 경우 `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` 플래그를 전달하는 것보다 Java 기본 인증서 저장소에 인증서를 설치하는 것이 더 쉽습니다. 예를 들어 포함 된 Java Demo 응용`https://localhost:8081/_explorer/index.html`프로그램 ()은 기본 인증서 저장소에 따라 달라 집니다.

X.509 인증서를 Java 기본 인증서 저장소로 가져 오려면 [Java CA 인증서 저장소에 인증서 추가](https://docs.microsoft.com/azure/java-add-certificate-ca-store)의 지침을 따릅니다. keytool을 실행할 때는 %JAVA_HOME% 디렉터리에서 작업하게 됩니다.

"CosmosDBEmulatorCertificate" TLS/SSL 인증서가 설치 되 면 응용 프로그램에서 로컬 Azure Cosmos DB 에뮬레이터에 연결 하 여 사용할 수 있어야 합니다. 문제가 계속 발생 하는 경우 [SSL/TLS 연결 디버깅](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) 문서를 수행 하는 것이 좋습니다. 인증서가 %JAVA_HOME%/jre/lib/security/cacerts 저장소에 설치되지 않은 것 같습니다. 예를 들어 설치된 Java 버전이 여러 개 있는 경우 애플리케이션이 업데이트한 것과는 다른 cacerts 저장소를 사용하고 있을 수도 있습니다.

## <a name="how-to-use-the-certificate-in-python"></a>Python에서 인증서를 사용하는 방법

기본적으로 SQL API에 대 한 [PYTHON SDK (버전 2.0.0 이상)](sql-api-sdk-python.md) 는 로컬 에뮬레이터에 연결할 때 TLS/SSL 인증서를 사용해 서는 안 됩니다. 그러나 TLS 유효성 검사를 사용 하려는 경우 [Python 소켓 래퍼](https://docs.python.org/2/library/ssl.html) 설명서의 예제를 따를 수 있습니다.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Node.js에서 인증서를 사용하는 방법

기본적으로 SQL API 용 [NODE.JS SDK (버전 1.10.1 이상)](sql-api-sdk-node.md) 는 로컬 에뮬레이터에 연결할 때 TLS/SSL 인증서를 사용해 서는 안 됩니다. 그러나 TLS 유효성 검사를 사용 하려는 경우 [node.js 설명서](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)의 예제를 따를 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * 인증서를 순환했습니다.
> * TLS/SSL 인증서를 내보냈습니다.
> * Java, Python 및 Node.js에서 인증서를 사용하는 방법을 알아보았습니다.

이제 개념 섹션으로 진행하여 Azure Cosmos DB에 대한 자세한 정보를 확인할 수 있습니다. 

> [!div class="nextstepaction"]
>[Azure Cosmos DB의 튜닝 가능한 데이터 일관성 수준](../cosmos-db/consistency-levels.md)
