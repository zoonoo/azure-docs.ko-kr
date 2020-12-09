---
title: 문제 해결-Azure Monitor Application Insights Java
description: Java Azure Monitor Application Insights 문제 해결
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855663"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Java Azure Monitor Application Insights 문제 해결

이 문서에서는 이러한 문제를 해결 하는 단계와 함께 java 에이전트를 사용 하 여 java 응용 프로그램을 계측 하는 동안 사용자가 직면 하는 일반적인 문제 중 일부에 대해 설명 했습니다.

## <a name="self-diagnostic-log-file"></a>자체 진단 로그 파일

기본적으로 Application Insights Java 3.0는 파일이 있는 디렉터리에 라는 로그 파일을 생성 `applicationinsights.log` `applicationinsights-agent-3.0.0.jar` 합니다.

이 로그 파일은 발생 했을 수 있는 문제에 대 한 힌트를 확인 하는 첫 번째 장소입니다.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Application Insights Java 2.x SDK에서 업그레이드

2.x [SDK에서 업그레이드를](./java-standalone-upgrade-from-2x.md)참조 하세요.

## <a name="upgrade-from-30-preview"></a>3.0 Preview에서 업그레이드

3.0 Preview에서 업그레이드 하는 경우 3.0 GA 릴리스에서 json 구조가 완전히 변경 되었으므로 모든 [구성 옵션](./java-standalone-config.md) 을 신중 하 게 검토 하세요.

이러한 변경 내용은 다음과 같습니다.

1.  구성 파일 이름 자체가에서로 변경 되었습니다 `ApplicationInsights.json` `applicationinsights.json` .
2.  `instrumentationSettings`노드가 더 이상 존재 하지 않습니다. 의 모든 콘텐츠 `instrumentationSettings` 는 루트 수준으로 이동 됩니다. 
3.  , 및와 같은 구성 노드 `sampling` `jmxMetrics` `instrumentation` `heartbeat` 는 `preview` 루트 수준으로 이동 됩니다.

## <a name="ssl-certificate-issues"></a>SSL 인증서 문제

기본 Java 키 저장소를 사용 하는 경우 모든 CA 루트 인증서가 이미 있으므로 추가 SSL 인증서를 가져올 필요가 없습니다.

사용자 지정 Java 키 저장소를 사용 하는 경우 Application Insights 끝점 SSL 인증서를 가져와야 할 수 있습니다.

### <a name="some-key-terminology"></a>몇 가지 주요 용어:
*키 저장소* 는 인증서, 공개 및 개인 키의 리포지토리입니다. 일반적으로 JDK 배포판에는 관리를 위한 실행 파일이 `keytool` 있습니다.

다음 예제는 키 저장소에 SSL 인증서를 가져오는 간단한 명령입니다.

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>SSL 인증서를 다운로드 하 고 추가 하는 단계:

1.  선호 하는 브라우저를 열고 아래와 `IngestionEndpoint` 같이 응용 프로그램을 계측 하는 데 사용 되는 연결 문자열에 있는 url로 이동 합니다.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="연결 문자열 Application Insights":::

2.  브라우저에서 ' 사이트 정보 보기 ' (잠금) 아이콘을 클릭 하 고 아래와 같이 ' 인증서 ' 옵션을 클릭 합니다.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="SSL 인증서 캡처":::

3.  세부 정보 탭으로 이동 하 고 파일에 복사를 클릭 합니다.
4.  다음 단추를 클릭 하 고 "Base-64로 인코딩된 x.509 (를 선택 합니다. "형식을 지정 하 고 다음을 선택 합니다.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="SSL 인증서 내보내기 마법사":::

5.  SSL 인증서를 저장할 파일을 지정 합니다. 마지막으로 다음을 클릭 하 고 마침을 클릭 합니다. "내보내기가 성공 했습니다." 라는 메시지가 표시 됩니다.
6.  인증서가 있으면 해당 인증서를 Java 키 저장소으로 가져올 수 있습니다. 위의 [명령을](#some-key-terminology) 사용 하 여 인증서를 가져옵니다.

> [!WARNING]
> 현재 인증서가 만료 되기 전에 새 인증서를 가져오려면 이러한 단계를 반복 해야 합니다. 아래와 같이 인증서 팝업의 "세부 정보" 탭에서 만료 정보를 찾을 수 있습니다.

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL 인증서 세부 정보":::
