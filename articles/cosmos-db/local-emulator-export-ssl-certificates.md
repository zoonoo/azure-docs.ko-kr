---
title: Azure Cosmos DB 에뮬레이터 인증서 내보내기
description: Java, Python, Node.js에서 사용할 Azure Cosmos DB 에뮬레이터 인증서를 내보내는 방법을 알아봅니다. Windows 인증서 저장소를 사용하지 않는 언어 및 런타임 환경을 위한 인증서를 내보내고 사용해야 합니다.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
ms.author: esarroyo
author: StefArroyo
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 669361fcc5ba5b79f0ec014b60fe4aa630b7b017
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361426"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Java, Python 및 Node.js 앱에서 사용할 Azure Cosmos DB 에뮬레이터 인증서 내보내기
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 에뮬레이터는 개발 목적으로 Azure Cosmos DB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. Azure Cosmos DB 에뮬레이터는 TLS 연결을 통한 보안 통신만 지원합니다.

Azure Cosmos DB 로컬 에뮬레이터의 인증서는 에뮬레이터를 처음 실행할 때 생성되며, 두 개의 인증서가 있습니다. 그중 하나는 로컬 에뮬레이터에 연결하는 데 사용되고 다른 하나는 에뮬레이터 내에서 에뮬레이터 데이터의 기본 암호화를 관리하는 데 사용됩니다. 내보내려는 인증서는 "DocumentDBEmulatorCertificate"라는 이름의 연결 인증서입니다.

에뮬레이터를 사용하여 Java, Python, Node.js 같은 다른 언어로 앱을 개발하는 경우 에뮬레이터 인증서를 내보내고 필요한 인증서 저장소로 가져와야 합니다.

.NET 언어 및 런타임은 Windows 인증서 저장소를 사용하여 애플리케이션이 Windows OS 호스트에서 실행될 때 Azure Cosmos DB 로컬 에뮬레이터에 안전하게 연결합니다. 다른 언어의 경우 해당 언어만의 인증서 관리 및 사용 방법이 있습니다. 예를 들어 Java는 자체 [인증서 저장소](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)를 사용하고 Python은 [소켓 래퍼](https://docs.python.org/2/library/ssl.html)를 사용하며 Node.js는 [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)을 사용합니다.

이 문서에서는 Windows 인증서 저장소와 통합되지 않는 다양한 언어 및 런타임 환경에서 사용할 TLS/SSL 인증서를 내보내는 방법을 보여 줍니다. 에뮬레이터에 대한 자세한 내용은 [Azure Cosmos DB 에뮬레이터를 사용하여 개발 및 테스트](./local-emulator.md)를 참조하세요.

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Azure Cosmos DB TLS/SSL 인증서 내보내기

Windows 인증서 저장소와 통합되지 않는 언어 및 런타임 환경의 에뮬레이터 엔드포인트를 성공적으로 활용하려면 에뮬레이터 인증서를 내보내야 합니다. Windows 인증서 관리자를 사용하여 인증서를 내보낼 수 있습니다. 다음 단계별 지침을 따라 “DocumentDBEmulatorCertificate” 인증서를 BASE-64로 인코딩된 X.509(.cer) 파일로 내보냅니다.

1. certlm.msc를 실행하여 Windows 인증서 관리자를 시작하고, 개인 -> 인증서 폴더로 이동한 다음 **DocumentDbEmulatorCertificate** 라는 친숙한 이름의 인증서를 엽니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 1단계":::

1. **세부 정보** 를 클릭한 다음 **확인** 을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 2단계":::

1. **파일에 복사...** 를 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 3단계":::

1. **다음** 을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 4단계":::

1. **아니요, 프라이빗 키를 내보내지 않습니다.** 를 클릭한 후, **다음** 을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 5단계":::

1. **Base-64 encoded X.509 (.CER)** 를 클릭한 후 **다음** 을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 6단계":::

1. 인증서에 이름을 지정합니다. 여기서는 **documentdbemulatorcert** 이며, **다음** 을 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 7단계":::

1. **Finish** 를 클릭합니다.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 내보내기 8단계":::

## <a name="use-the-certificate-with-java-apps"></a>Java 앱에서 인증서 사용

Java 기반 클라이언트를 사용하는 Java 애플리케이션 또는 MongoDB 애플리케이션을 실행하는 경우 `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` 플래그를 전달하는 것보다 Java 기본 인증서 저장소에 인증서를 설치하기가 더 쉽습니다. 예를 들어 포함된 Java Demo 애플리케이션(`https://localhost:8081/_explorer/index.html`)은 기본 인증서 저장소에 따라 다릅니다.

X.509 인증서를 기본 Java 인증서 저장소로 가져오려면 [Java 인증서 저장소에 인증서 추가](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm)의 지침을 따릅니다. keytool을 실행할 때는 *%JAVA_HOME%* 디렉터리에서 작업하게 됩니다. 인증서를 인증서 저장소에 가져온 후, MongoDB API용 SQL 및 Azure Cosmos DB 클라이언트에서 Azure Cosmos DB 에뮬레이터에 연결할 수 있습니다.

또는 다음 Bash 스크립트를 실행하여 인증서를 가져올 수 있습니다.

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

“CosmosDBEmulatorCertificate”
TLS/SSL 인증서가 설치되면 애플리케이션에서 로컬 Azure Cosmos DB 에뮬레이터에 연결하여 사용할 수 있어야 합니다. 문제가 있는 경우 [SSL/TLS 연결 디버깅](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) 문서를 따라 해결할 수 있습니다. 대부분의 경우 인증서는 *%JAVA_HOME%/jre/lib/security/cacerts* 저장소에 설치되지 않을 수 있습니다. 예를 들어 설치된 Java 버전이 여러 개 있는 경우 애플리케이션이 업데이트한 것과는 다른 cacerts 저장소를 사용하고 있을 수도 있습니다.

## <a name="use-the-certificate-with-python-apps"></a>Python 앱에서 인증서 사용

Python 앱에서 에뮬레이터에 연결하면 TLS 인증이 비활성화됩니다. 기본적으로 SQL API용 [Python SDK(버전 2.0.0 이상)](sql-api-sdk-python.md)는 로컬 에뮬레이터에 연결할 때 TLS/SSL 인증서를 사용하지 않습니다. 그러나 TLS 유효성 검사를 사용하려면 [Python 소켓 래퍼](https://docs.python.org/2/library/ssl.html) 설명서의 예제를 따르면 됩니다.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Node.js에서 인증서를 사용하는 방법

Node.js SDK에서 에뮬레이터에 연결하면 TLS 인증이 비활성화됩니다. 기본적으로 SQL API용 [Node.js SDK(버전 1.10.1 이상)](sql-api-sdk-node.md)는 로컬 에뮬레이터에 연결할 때 TLS/SSL 인증서를 사용하지 않습니다. 그러나 TLS 유효성 검사를 사용하려면 [Node.js 설명서](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)의 예제를 따르면 됩니다.

## <a name="rotate-emulator-certificates"></a>에뮬레이터 인증서 회전

Windows 트레이에서 실행되는 Azure Cosmos DB 에뮬레이터에서 **데이터 다시 설정** 을 선택하여 에뮬레이터 인증서를 강제로 다시 생성할 수 있습니다. 참고로 이 작업은 에뮬레이터에서 로컬로 저장된 모든 데이터를 초기화합니다.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Azure Cosmos DB 로컬 에뮬레이터 데이터 다시 설정":::

인증서를 Java 인증서 저장소에 설치했거나 다른 곳에서 사용한 경우 현재 인증서를 사용하여 다시 가져와야 합니다. 애플리케이션은 인증서를 업데이트할 때까지 로컬 에뮬레이터에 연결할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [명령줄 매개 변수 및 PowerShell 명령을 사용하여 에뮬레이터 제어](emulator-command-line-parameters.md)
* [에뮬레이터를 사용하여 이슈 디버그](troubleshoot-local-emulator.md)
