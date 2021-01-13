---
title: Java에 대 한 Azure Monitor Application Insights 문제 해결
description: Azure Monitor에 대 한 Java 에이전트 문제를 해결 하는 방법을 알아봅니다 Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 54bf2440dff20fc757f37e3f31a53c57ebd59120
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133194"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>문제 해결 가이드: Java 용 Azure Monitor Application Insights

이 문서에서는 Application Insights 용 Java 에이전트를 사용 하 여 Java 응용 프로그램을 계측 하는 동안 발생할 수 있는 일반적인 문제 중 일부를 다룹니다. 이러한 문제를 해결 하는 단계에 대해서도 설명 합니다. Application Insights은 Azure Monitor platform 서비스의 기능입니다.

## <a name="check-the-self-diagnostic-log-file"></a>자체 진단 로그 파일 확인

기본적으로 Application Insights 용 Java 3.0 에이전트는 `applicationinsights.log` 파일을 보관 하는 동일한 디렉터리에 라는 로그 파일을 생성 합니다 `applicationinsights-agent-3.0.0.jar` .

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

로깅은 먼저 로깅 프레임 워크의 구성 된 임계값을 충족 하 고 두 번째는 구성 된 Application Insights 임계값을 충족 하는 경우에만 캡처됩니다.

특정 로깅 문이 로깅 프레임 워크의 구성 된 임계값을 충족 하는지 확인 하는 가장 좋은 방법은 일반 응용 프로그램 로그 (예: 파일 또는 콘솔)에 표시 되는지 확인 하는 것입니다.

자세한 내용은 [자동 수집 된 로깅 구성](./java-standalone-config.md#auto-collected-logging) 을 참조 하세요.

## <a name="import-ssl-certificates"></a>SSL 인증서 가져오기

기본 Java 키 저장소를 사용 하는 경우 모든 CA 루트 인증서가 이미 있는 것입니다. 더 이상 SSL 인증서를 가져올 필요가 없습니다.

사용자 지정 Java 키 저장소를 사용 하는 경우 Application Insights 끝점 SSL 인증서를 가져와야 할 수 있습니다.

### <a name="key-terminology"></a>주요 용어
*키 저장소* 는 인증서, 공개 키 및 개인 키의 리포지토리입니다. 일반적으로 Java Development Kit 배포판에는 관리를 위한 실행 파일이 있습니다 `keytool` .

다음 예제는 키 저장소에 SSL 인증서를 가져오는 간단한 명령입니다.

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>SSL 인증서를 다운로드 하 고 추가 하는 단계

1.  선호 하는 브라우저를 열고 `IngestionEndpoint` 응용 프로그램을 계측 하는 데 사용 되는 연결 문자열에 있는 URL로 이동 합니다.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights 연결 문자열을 보여 주는 스크린샷":::

2.  브라우저에서 **사이트 정보 보기** (잠금) 아이콘을 선택 하 고 **인증서** 옵션을 선택 합니다.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="사이트 정보에 있는 인증서 옵션의 스크린샷":::

3.  **세부 정보** 탭으로 이동 하 고 **파일에 복사** 를 선택 합니다.
4.  **다음** 단추를 선택 하 고 **Base-64로 인코딩된 x.509 (를 선택 합니다. CER)** 형식을 선택한 후 **다음** 을 다시 선택 합니다.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="형식이 선택 된 인증서 내보내기 마법사의 스크린샷":::

5.  SSL 인증서를 저장할 파일을 지정 합니다. 다음 마침 **을 선택**  >  합니다. "내보내기가 성공 했습니다." 라는 메시지가 표시 됩니다.
6.  인증서를 만든 후에는 인증서를 Java 키 저장소 가져올 때입니다. [이전 명령을](#key-terminology) 사용 하 여 인증서를 가져옵니다.

> [!WARNING]
> 현재 인증서가 만료 되기 전에 새 인증서를 가져오려면 이러한 단계를 반복 해야 합니다. **인증서** 대화 상자의 **자세히** 탭에서 만료 정보를 찾을 수 있습니다.
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL 인증서 세부 정보를 보여 주는 스크린샷":::
