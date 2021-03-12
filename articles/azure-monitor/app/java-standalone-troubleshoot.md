---
title: Java에 대 한 Azure Monitor Application Insights 문제 해결
description: Azure Monitor에 대 한 Java 에이전트 문제를 해결 하는 방법을 알아봅니다 Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: f971466f25c2b7a4bd28e5b7eec6268f1b2e8b3d
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225577"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>문제 해결 가이드: Java 용 Azure Monitor Application Insights

이 문서에서는 Application Insights 용 Java 에이전트를 사용 하 여 Java 응용 프로그램을 계측 하는 동안 발생할 수 있는 일반적인 문제 중 일부를 다룹니다. 이러한 문제를 해결 하는 단계에 대해서도 설명 합니다. Application Insights은 Azure Monitor platform 서비스의 기능입니다.

## <a name="check-the-self-diagnostic-log-file"></a>자체 진단 로그 파일 확인

기본적으로 Application Insights 용 Java 3.0 에이전트는 `applicationinsights.log` 파일을 보관 하는 동일한 디렉터리에 라는 로그 파일을 생성 합니다 `applicationinsights-agent-3.0.2.jar` .

이 로그 파일은 발생 했을 수 있는 문제에 대 한 힌트를 확인 하는 첫 번째 장소입니다.

## <a name="jvm-fails-to-start"></a>JVM 시작 실패

JVM을 시작 하지 못한 경우 "zip 파일을 여는 동안 오류가 발생 했습니다." 오류가 발생 하는 경우 파일을 전송 하는 동안 손상 되었을 수 있으므로 에이전트 JAR 파일을 다시 다운로드 해 보세요.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Java 2.x SDK Application Insights에서 업그레이드

응용 프로그램에서 Java 2.x SDK Application Insights을 이미 사용 하 고 있는 경우이를 계속 사용할 수 있습니다. Java 3.0 에이전트가이를 검색 합니다. 자세한 내용은 [Java 2.X SDK에서 업그레이드](./java-standalone-upgrade-from-2x.md)를 참조 하세요.

## <a name="upgrade-from-application-insights-java-30-preview"></a>Application Insights Java 3.0 Preview에서 업그레이드

Java 3.0 Preview 에이전트에서 업그레이드 하는 경우 모든 [구성 옵션](./java-standalone-config.md) 을 신중 하 게 검토 합니다. JSON 구조는 3.0 GA (일반 공급) 릴리스에서 완전히 변경 되었습니다.

이러한 변경 내용은 다음과 같습니다.

-  구성 파일 이름이에서로 변경 되었습니다 `ApplicationInsights.json` `applicationinsights.json` .
-  `instrumentationSettings`노드가 더 이상 존재 하지 않습니다. 의 모든 콘텐츠 `instrumentationSettings` 는 루트 수준으로 이동 됩니다. 
-  ,, 및와 같은 구성 노드 `sampling` `jmxMetrics` `instrumentation` `heartbeat` 는 `preview` 루트 수준으로 이동 됩니다.

## <a name="some-logging-is-not-auto-collected"></a>일부 로깅은 자동으로 수집 되지 않습니다.

로깅은 먼저 로깅 프레임 워크에 대해 구성 된 수준을 충족 하는 경우에만 캡처되고, 두 번째는 Application Insights에 대해 구성 된 수준에도 부합 합니다.

예를 들어 로깅 프레임 워크가 `WARN` 패키지에서 (이상) 로그에 구성 되 `com.example` 고 Application Insights가 (이상) 캡처하도록 구성 된 경우 `INFO` Application Insights는 `WARN` 패키지에서 (이상)만 캡처합니다 `com.example` .

특정 로깅 문이 로깅 프레임 워크의 구성 된 임계값을 충족 하는지 확인 하는 가장 좋은 방법은 일반 응용 프로그램 로그 (예: 파일 또는 콘솔)에 표시 되는지 확인 하는 것입니다.

또한 예외 개체가로 거에 전달 되는 경우 로그 메시지 (및 예외 개체 세부 정보)는 테이블 대신 테이블의 Azure Portal에 표시 됩니다 `exceptions` `traces` .

자세한 내용은 [자동 수집 된 로깅 구성](./java-standalone-config.md#auto-collected-logging) 을 참조 하세요.

## <a name="import-ssl-certificates"></a>SSL 인증서 가져오기

이 섹션에서는 Java 에이전트를 사용 하는 경우 SSL 인증서와 관련 된 예외를 해결 하 고 문제를 해결할 수 있습니다.

이 문제를 해결 하기 위한 두 가지 다른 경로는 다음과 같습니다.
* 기본 Java 키 저장소를 사용 하는 경우
* 사용자 지정 Java 키 저장소를 사용 하는 경우

수행할 경로를 잘 모르는 경우 JVM 인수가 있는지 확인 `-Djavax.net.ssl.trustStore=...` 합니다.
이러한 JVM 인수를 사용 _하지 않는_ 경우 기본 Java 키 저장소를 사용 하는 것입니다.
이러한 JVM 인수를 _사용 하는_ 경우 사용자 지정 키 저장소를 사용 하는 것 이며, JVM 인수는 사용자 지정 키 저장소를 가리킵니다.

### <a name="if-using-the-default-java-keystore"></a>기본 Java 키 저장소를 사용 하는 경우:

일반적으로 기본 Java 키 저장소에는 모든 CA 루트 인증서가 이미 있습니다. 그러나 다른 루트 인증서로 수집 끝점 인증서를 서명할 수 있는 경우와 같이 몇 가지 예외가 있을 수 있습니다. 따라서이 문제를 해결 하려면 다음 세 단계를 수행 하는 것이 좋습니다.

1.  Application Insights 끝점에 서명 하는 데 사용 된 루트 인증서가 기본 키 저장소에 이미 있는지 확인 합니다. 기본적으로 신뢰할 수 있는 CA 인증서는에 저장 됩니다 `$JAVA_HOME/jre/lib/security/cacerts` . Java 키 저장소에서 인증서를 나열 하려면 다음 명령을 사용 합니다.
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    출력을 다음과 같은 임시 파일로 리디렉션할 수 있습니다 (나중에 쉽게 검색할 수 있음).
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. 인증서 목록이 표시 되 면 다음 [단계](#steps-to-download-ssl-certificate) 에 따라 Application Insights 끝점에 서명 하는 데 사용 된 루트 인증서를 다운로드 합니다.

    인증서를 다운로드 한 후에는 다음 명령을 사용 하 여 인증서에 대 한 SHA-1 해시를 생성 합니다.
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    SHA-1 값을 복사 하 고이 값이 이전에 저장 한 "temp.txt" 파일에 있는지 확인 합니다.  임시 파일에서 SHA-1 값을 찾을 수 없는 경우 다운로드 한 루트 인증서가 기본 Java 키 저장소에서 누락 되었음을 나타냅니다.


3. 다음 명령을 사용 하 여 루트 인증서를 기본 Java 키 저장소 가져옵니다.
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    이 경우에는
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>사용자 지정 Java 키 저장소를 사용 하는 경우:

사용자 지정 Java 키 저장소를 사용 하는 경우 Application Insights 끝점 루트 SSL 인증서를 가져와야 할 수 있습니다.
이 문제를 해결 하려면 다음 두 단계를 수행 하는 것이 좋습니다.
1. Application Insights 끝점에서 루트 인증서를 다운로드 하려면 다음 [단계](#steps-to-download-ssl-certificate) 를 수행 합니다.
2. 다음 명령을 사용 하 여 사용자 지정 Java 키 저장소 루트 SSL 인증서를 가져옵니다.
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>SSL 인증서를 다운로드 하는 단계

1.  선호 하는 브라우저를 열고 `IngestionEndpoint` 응용 프로그램을 계측 하는 데 사용 되는 연결 문자열에 있는 URL로 이동 합니다.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Application Insights 연결 문자열을 보여 주는 스크린샷" lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  브라우저에서 **사이트 정보 보기** (잠금) 아이콘을 선택 하 고 **인증서** 옵션을 선택 합니다.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="사이트 정보에 있는 인증서 옵션의 스크린샷" lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  ' 리프 ' 인증서를 다운로드 하는 대신 아래와 같이 ' root ' 인증서를 다운로드 해야 합니다. 나중에 "인증서 경로"를 클릭 하 > 루트 인증서를 선택 하 > ' 인증서 보기 '를 클릭 합니다. 그러면 새 인증서 메뉴가 표시 되 고 새 메뉴에서 인증서를 다운로드할 수 있습니다.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="루트 인증서를 선택 하는 방법의 스크린샷" lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  **세부 정보** 탭으로 이동 하 고 **파일에 복사** 를 선택 합니다.
5.  **다음** 단추를 선택 하 고 **Base-64로 인코딩된 x.509 (를 선택 합니다. CER)** 형식을 선택한 후 **다음** 을 다시 선택 합니다.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="형식이 선택 된 인증서 내보내기 마법사의 스크린샷" lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  SSL 인증서를 저장할 파일을 지정 합니다. 다음 마침 **을 선택**  >  합니다. "내보내기가 성공 했습니다." 라는 메시지가 표시 됩니다.

> [!WARNING]
> 현재 인증서가 만료 되기 전에 새 인증서를 가져오려면 이러한 단계를 반복 해야 합니다. **인증서** 대화 상자의 **자세히** 탭에서 만료 정보를 찾을 수 있습니다.
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL 인증서 세부 정보를 보여 주는 스크린샷" lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
