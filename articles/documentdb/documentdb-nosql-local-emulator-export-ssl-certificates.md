---
title: "DocumentDB 에뮬레이터 인증서 내보내기 | Microsoft 문서"
description: "Windows 인증서 저장소를 사용하지 않는 언어와 런타임으로 개발할 때는 SSL 인증서를 내보내고 관리해야 합니다. 이 게시물에서는 단계별 지침을 제공합니다."
services: documentdb
documentationcenter: 
keywords: "DocumentDB 에뮬레이터"
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2017
ms.author: tvoellm
translationtype: Human Translation
ms.sourcegitcommit: 224897c09073fe36c7d4f9f8c965212c25297271
ms.openlocfilehash: 3f89e192052bb633190610a4dffbefb930dbc01c


---

# <a name="export-the-documentdb-emulator-certificates"></a>DocumentDB 에뮬레이터 인증서 내보내기

[**에뮬레이터 다운로드**](https://aka.ms/documentdb-emulator)

Azure DocumentDB 에뮬레이터는 SSL 연결 사용을 포함하여 개발 목적으로 Azure DocumentDB 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 이 게시물에서는 사용자 고유의 [인증서 저장소](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)를 사용하는 Java, [소켓 래퍼](https://docs.python.org/2/library/ssl.html)을 사용하는 Python, [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)을 사용하는 Node.js처럼 Windows 인증서 저장소와 통합되지 않는 언어와 런타임에 사용할 SSL 인증서를 내보내는 방법을 보여 줍니다. [Azure DocumentDB 에뮬레이터를 사용하여 개발 및 테스트](./documentdb-nosql-local-emulator.md)에서 에뮬레이터에 대한 자세한 내용을 알아볼 수 있습니다.

## <a name="certification-rotation"></a>인증 회전

DocumentDB 로컬 에뮬레이터의 인증서는 에뮬레이터가 처음 실행될 때 생성되며, 두 개의 인증서가 있습니다. 하나는 로컬 에뮬레이터에 연결하는 데 사용되고, 다른 하나는 에뮬레이터 내에서 비밀을 관리하는 데 사용됩니다. 내보내려는 인증서는 "DocumentDBEmulatorCertificate"라는 이름의 연결 인증서입니다.

Windows 트레이에서 실행 중인 DocumentDB 에뮬레이터에서 아래와 같이 **데이터 다시 설정**을 클릭하여 두 인증서를 모두 다시 생성할 수 있습니다. 인증서를 다시 생성하여 Java 인증서 저장소에 설치했거나 다른 곳에서 사용한 경우에는 이 인증서를 업데이트해야 합니다. 그렇지 않으면 응용 프로그램이 더 이상 로컬 에뮬레이터에 연결되지 않습니다.

![DocumentDB 로컬 에뮬레이터 데이터 다시 설정](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

## <a name="how-to-export-the-documentdb-ssl-certificate"></a>DocumentDB SSL 인증서를 내보내는 방법

1. certlm.msc를 실행하여 Windows 인증서 관리자를 시작하고, [개인 -> 인증서] 폴더로 이동한 다음 **DocumentDBEmulatorCertificate**라는 이름의 인증서를 엽니다.

    ![DocumentDB 로컬 에뮬레이터 내보내기 1단계](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

2. **세부 정보**를 클릭한 다음 **확인**을 클릭합니다.

    ![DocumentDB 로컬 에뮬레이터 내보내기 2단계](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

3. **파일에 복사... **를 클릭합니다.

    ![DocumentDB 로컬 에뮬레이터 내보내기 3단계](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

4. **다음**을 클릭합니다.

    ![DocumentDB 로컬 에뮬레이터 내보내기 4단계](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

5. **아니요, 개인 키를 내보내지 않습니다.**를 클릭한 후 **다음**을 클릭합니다.

    ![DocumentDB 로컬 에뮬레이터 내보내기 5단계](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

6. **Base-64 encoded X.509 (.CER)**를 클릭한 후 **다음**을 클릭합니다.

    ![DocumentDB 로컬 에뮬레이터 내보내기 6단계](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

7. 인증서에 이름을 지정합니다. 여기서는 **documentdbemulatorcert**이며, **다음**을 클릭합니다.

    ![DocumentDB 로컬 에뮬레이터 내보내기 7단계](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

8. **마침**을 클릭합니다.

    ![DocumentDB 로컬 에뮬레이터 내보내기 8단계](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Java에서 인증서를 사용하는 방법

Java 클라이언트를 사용하는 Java 응용 프로그램 또는 MongoDB 응용 프로그램을 실행하는 경우 "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>" 플래그를 전달하는 것보다 Java 기본 인증서 저장소에 인증서를 설치하는 것이 더 쉽습니다.. 예를 들어 포함된 [Java Demo 응용 프로그램](https://localhost:8081/_explorer/index.html)은 기본 인증서 저장소에 따라 다릅니다.

X.509 인증서를 Java 기본 인증서 저장소로 가져 오려면 [Java CA 인증서 저장소에 인증서 추가](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)의 지침을 따릅니다. keytool을 실행할 때는 %JAVA_HOME% 디렉터리에서 작업하게 됩니다.

"DocumentDBEmulatorCertificate" SSL 인증서가 설치되면 응용 프로그램에서 로컬 DocumentDB 에뮬레이터에 연결하여 사용할 수 있어야 합니다. 문제가 계속 발생하면 [SSL/TLS 연결 디버그](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) 문서의 지침을 따릅니다. 인증서가 %JAVA_HOME%/jre/lib/security/cacerts 저장소에 설치되지 않은 것 같습니다. 예를 들어 설치된 Java 버전이 여러 개 있는 경우 응용 프로그램이 업데이트한 것과는 다른 cacerts 저장소를 사용하고 있을 수도 있습니다.

## <a name="how-to-use-the-certificate-in-python"></a>Python에서 인증서를 사용하는 방법

기본적으로 DocumentDB 용 [Python SDK(버전 2.0.0 이상)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-python)는 로컬 에뮬레이터에 연결할 때 SSL 인증서를 사용하지 않습니다. 그러나 SSL 유효성 검사를 사용하려면 [Python 소켓 래퍼 ](https://docs.python.org/2/library/ssl.html) 설명서의 예제를 따르면 됩니다.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Node.js에서 인증서를 사용하는 방법

기본적으로 DocumentDB 용 [Node.js SDK(버전 1.10.1 이상)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-node)는 로컬 에뮬레이터에 연결할 때 SSL 인증서를 사용하지 않습니다. 그러나 SSL 유효성 검사를 사용하려면 [Node.js 설명서](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)의 예제를 따르면 됩니다.

## <a name="next-steps"></a>다음 단계
* DocumentDB에 대해 자세히 알아보려면 [Azure DocumentDB 소개](documentdb-introduction.md)를 참조하세요.
* DocumentDB 에뮬레이터를 기반으로 개발을 시작하려면 [지원되는 DocumentDB SDK](documentdb-sdk-dotnet.md) 중 하나를 다운로드하세요.



<!--HONumber=Jan17_HO4-->


