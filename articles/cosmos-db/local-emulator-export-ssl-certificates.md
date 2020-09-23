---
title: Azure Cosmos DB 에뮬레이터 인증서 내보내기
description: Java, Python 및 Node.js 앱에서 사용 하기 위해 Azure Cosmos DB 에뮬레이터 인증서를 내보내는 방법에 대해 알아봅니다. Windows 인증서 저장소를 사용 하지 않는 언어 및 런타임 환경에서 인증서를 내보내고 사용 해야 합니다.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java
ms.openlocfilehash: 8f2714c7c49aa5e02747ce726da29a98485b5fbd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90988230"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Java, Python 및 Node.js 앱에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기

Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. Azure Cosmos emulator는 TLS 연결을 통한 보안 통신만 지원 합니다.

Azure Cosmos DB 로컬 에뮬레이터의 인증서는 에뮬레이터를 처음 실행할 때 생성 됩니다. 두 개의 인증서가 있습니다. 그 중 하나는 로컬 에뮬레이터에 연결 하는 데 사용 되 고 다른 하나는 에뮬레이터 내에서 에뮬레이터 데이터의 기본 암호화를 관리 하는 데 사용 됩니다. 내보내려는 인증서는 "DocumentDBEmulatorCertificate"라는 이름의 연결 인증서입니다.

에뮬레이터를 사용 하 여 Java, Python 또는 Node.js 같은 다른 언어로 앱을 개발 하는 경우 에뮬레이터 인증서를 내보내고 필요한 인증서 저장소로 가져와야 합니다.

.NET 언어 및 런타임은 windows 인증서 저장소를 사용 하 여 Windows OS 호스트에서 응용 프로그램을 실행할 때 Azure Cosmos DB 로컬 에뮬레이터에 안전 하 게 연결 합니다. 다른 언어의 경우 해당 언어만의 인증서 관리 및 사용 방법이 있습니다. 예를 들어 Java는 자체 [인증서 저장소](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)를 사용 하 고 Python은 [소켓 래퍼](https://docs.python.org/2/library/ssl.html)를 사용 하며 Node.js는 [tlssocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)을 사용 합니다.

이 문서에서는 Windows 인증서 저장소와 통합 되지 않는 다양 한 언어 및 런타임 환경에서 사용할 TLS/SSL 인증서를 내보내는 방법을 보여 줍니다. 에뮬레이터에 대한 자세한 내용은 [Azure Cosmos DB 에뮬레이터를 사용하여 개발 및 테스트](./local-emulator.md)를 참조하세요.

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Azure Cosmos DB TLS/SSL 인증서 내보내기

Windows 인증서 저장소와 통합 되지 않는 언어 및 런타임 환경의 에뮬레이터 끝점을 성공적으로 사용 하려면 에뮬레이터 인증서를 내보내야 합니다. Windows 인증서 관리자를 사용 하 여 인증서를 내보낼 수 있습니다. 다음 단계별 지침을 사용 하 여 "DocumentDBEmulatorCertificate" 인증서를 64 인코딩된 x.509 (.cer) 파일로 내보냅니다.

1. DocumentDbEmulatorCertificate를 실행 하 여 Windows 인증서 관리자를 시작 하 고 개인->인증서 폴더로 이동 하 여 이름이 **DocumentDbEmulatorCertificate**인 인증서를 엽니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 1단계":::

1. **세부 정보**를 클릭한 다음 **확인**을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 2단계":::

1. **파일에 복사...** 를 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 3단계":::

1. **다음**을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 4단계":::

1. **아니요, 프라이빗 키를 내보내지 않습니다.** 를 클릭한 후, **다음**을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 5단계":::

1. **Base-64 encoded X.509 (.CER)** 를 클릭한 후 **다음**을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 6단계":::

1. 인증서에 이름을 지정합니다. 여기서는 **documentdbemulatorcert**이며, **다음**을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 7단계":::

1. **마침**을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 8단계":::

## <a name="use-the-certificate-with-java-apps"></a>Java 앱에서 인증서 사용

Java 기반 클라이언트를 사용 하는 Java 응용 프로그램이 나 MongoDB 응용 프로그램을 실행 하는 경우에는 플래그를 전달 하는 것 보다 Java 기본 인증서 저장소에 인증서를 설치 하는 것이 더 쉽습니다 `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` . 예를 들어 포함 된 Java Demo 응용 프로그램 ( `https://localhost:8081/_explorer/index.html` )은 기본 인증서 저장소에 따라 달라 집니다.

[Java 인증서 저장소에 인증서 추가](https://docs.microsoft.com/azure/java-add-certificate-ca-store) 의 지침에 따라 x.509 인증서를 기본 java 인증서 저장소로 가져옵니다. Keytool을 실행 하는 경우 *% JAVA_HOME%* 디렉터리에서 작업 하는 것을 염두에 두어야 합니다. 인증서를 인증서 저장소로 가져온 후에는 SQL 용 클라이언트와 MongoDB 용 Azure Cosmos DB API가 Azure Cosmos 에뮬레이터에 연결할 수 있습니다.

또는 다음 bash 스크립트를 실행 하 여 인증서를 가져올 수 있습니다.

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

"CosmosDBEmulatorCertificate" TLS/SSL 인증서가 설치 되 면 응용 프로그램에서 로컬 Azure Cosmos DB 에뮬레이터에 연결 하 여 사용할 수 있어야 합니다. 문제가 있는 경우 [SSL/TLS 연결 디버깅](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) 문서를 따를 수 있습니다. 대부분의 경우 인증서는 *% JAVA_HOME%/jre/lib/security/cacerts* 저장소에 설치 되지 않을 수 있습니다. 예를 들어 여러 버전의 Java가 설치 되어 있는 경우 응용 프로그램은 사용자가 업데이트 한 것과 다른 cacerts 저장소를 사용할 수 있습니다.

## <a name="use-the-certificate-with-python-apps"></a>Python 앱에서 인증서 사용

Python 앱에서 에뮬레이터에 연결 하는 경우 TLS 확인이 사용 하지 않도록 설정 됩니다. 기본적으로 SQL API 용 [PYTHON SDK (버전 2.0.0 이상)](sql-api-sdk-python.md) 는 로컬 에뮬레이터에 연결할 때 TLS/SSL 인증서를 사용 하려고 시도 하지 않습니다. 그러나 TLS 유효성 검사를 사용 하려는 경우 [Python 소켓 래퍼](https://docs.python.org/2/library/ssl.html) 설명서의 예제를 따를 수 있습니다.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Node.js에서 인증서를 사용하는 방법

Node.js Sdk에서 에뮬레이터에 연결 하는 경우 TLS 확인이 사용 하지 않도록 설정 됩니다. 기본적으로 SQL API에 대 한 [Node.js SDK (버전 1.10.1 이상)](sql-api-sdk-node.md) 는 로컬 에뮬레이터에 연결할 때 TLS/SSL 인증서를 사용 하려고 시도 하지 않습니다. 그러나 TLS 유효성 검사를 사용 하려는 경우 [Node.js 설명서](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)의 예제를 따를 수 있습니다.

## <a name="rotate-emulator-certificates"></a>에뮬레이터 인증서 회전

Windows 트레이를 실행 하는 Azure Cosmos DB 에뮬레이터에서 **데이터 재설정** 을 선택 하 여 에뮬레이터 인증서를 강제로 다시 생성할 수 있습니다. 또한이 작업은 에뮬레이터에서 로컬로 저장 된 모든 데이터를 초기화 합니다.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 데이터 다시 설정":::

인증서를 Java 인증서 저장소에 설치 했거나 다른 곳에서 사용한 경우 현재 인증서를 사용 하 여 다시 가져와야 합니다. 응용 프로그램은 인증서를 업데이트할 때까지 로컬 에뮬레이터에 연결할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [명령줄 매개 변수 및 PowerShell 명령을 사용 하 여 에뮬레이터 제어](emulator-command-line-parameters.md)
* [에뮬레이터를 사용 하 여 문제 디버그](troubleshoot-local-emulator.md)

