---
title: Azure 서비스 패브릭 프로브
description: 응용 프로그램 및 서비스 매니페스트 파일을 사용하여 Azure 서비스 패브릭에서 Liveness 프로브를 모델링하는 방법
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431216"
---
# <a name="liveness-probe"></a>생생도 프로브
7.1 서비스 패브릭부터 시작하여 [컨테이너화된][containers-introduction-link] 애플리케이션을 위한 라이브니스 프로브 메커니즘을 지원합니다. Liveness Probe는 컨테이너화된 응용 프로그램의 생동감을 알리는 데 도움이 되며 적시에 응답하지 않으면 다시 시작됩니다.
이 문서에서는 매니페스트 파일을 통해 Liveness 프로브를 정의하는 방법에 대한 개요를 제공합니다.

이 문서를 진행하기 전에 [서비스 패브릭 응용 프로그램 모델][application-model-link] 및 서비스 패브릭 호스팅 [모델에][hosting-model-link]익숙해지는 것이 좋습니다.

> [!NOTE]
> 라이브니스 프로브는 NAT 네트워킹 모드의 컨테이너에대해서만 지원됩니다.

## <a name="semantics"></a>의미 체계
컨테이너당 1개의 Liveness 프로브만 지정할 수 있으며 다음 필드를 통해 컨테이너의 동작을 제어할 수 있습니다.

* `initialDelaySeconds`: 컨테이너가 시작되면 프로브 실행을 시작하는 초의 초기 지연입니다. 지원되는 값은 int. 기본값은 0입니다. 최소값은 0입니다.

* `timeoutSeconds`: 프로브가 성공적으로 완료되지 않은 경우 프로브가 실패한 것으로 간주하는 몇 초 후의 기간입니다. 지원되는 값은 int. 기본값은 1입니다. 최소값은 1입니다.

* `periodSeconds`: 프로브 빈도를 지정하는 데 몇 초의 기간이 있습니다. 지원되는 값은 int. 기본값은 10입니다. 최소값은 1입니다.

* `failureThreshold`: 실패 임계값에 도달하면 컨테이너가 다시 시작됩니다. 지원되는 값은 int. 기본값은 3입니다. 최소값은 1입니다.

* `successThreshold`: 실패시 프로브가 성공으로 간주되려면 SuccessThreshold에 대해 성공적으로 실행되어야 합니다. 지원되는 값은 int. 기본값은 1입니다. 최소값은 1입니다.

한 번에 컨테이너에 최대 1 개의 프로브가 있습니다. 프로브가 **시간 초과에서** 완료되지 않은 경우초 초 우리는 계속 기다렸다가 **실패임계값으로**계산합니다. 

또한 ServiceFabric은 배포된 서비스 패키지에 대한 다음 프로브 [상태 보고서를][health-introduction-link] 올립니다.

* `Ok`: 프로브가 성공을 위해 **성공하면임계값은** 상태를 확인으로 보고합니다.

* `Error`: 프로브 실패카운트 == **실패임계값,** 컨테이너를 다시 시작하기 전에 오류를 보고합니다.

* `Warning`: 
    1. 프로브가 실패하고 실패카운트가 실패할 **경우<임계값경고를** 보고합니다. 이 상태 보고서는 failureCount가 오류에 도달할 때까지 **유지됩니다.임계값** **.**
    2. 성공 후 실패에, 우리는 여전히 경고를 보고 하지만 업데이트 된 연속 성공.

## <a name="specifying-liveness-probe"></a>라이브 프로브 지정

ServiceManifestImport에서 ApplicationManifest.xml에서 프로브를 지정할 수 있습니다.

프로브는 다음 중 하나를 수행할 수 있습니다.

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>HTTP 프로브

HTTP 프로브의 경우 서비스 패브릭은 지정된 포트 및 경로에 HTTP 요청을 보냅니다. 반환 코드는 200보다 크거나 같고 400 미만은 성공을 나타냅니다.

다음은 HttpGet 프로브를 지정하는 방법의 예입니다.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HttpGet 프로브에는 설정할 수 있는 추가 속성이 있습니다.

* `path`: HTTP 요청에 대한 액세스 경로입니다.

* `port`: 프로브에 액세스할 수 있는 포트입니다. 범위는 1에서 65535입니다. 필수.

* `scheme`: 코드 패키지에 연결하는 데 사용할 구성표입니다. HTTPS로 설정하면 인증서 확인이 건너뜁니다. 기본값HTTP

* `httpHeader`: 요청에 설정할 헤더입니다. 이들 중 여러 를 지정할 수 있습니다.

* `host`: 호스트 IP에 연결합니다.

## <a name="tcp-probe"></a>TCP 프로브

TCP 프로브의 경우 Service Fabric은 지정된 포트가 있는 컨테이너에서 소켓을 열려고 시도합니다. 연결을 설정할 수 있는 경우 프로브는 성공으로 간주됩니다. 다음은 TCP 소켓을 사용하는 프로브를 지정하는 방법의 예입니다.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>임원 프로브

이 프로브는 컨테이너에 임원을 발행하고 명령이 완료될 때까지 기다립니다.

> [!NOTE]
> Exec 명령은 쉼표 분리 된 문자열을 취합니다. 예제의 다음 명령은 Linux 컨테이너에서 작동합니다.
> 윈도우 컨테이너를 사용하려는 경우 <Command>cmd를</Command> 사용하십시오.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>다음 단계
관련 정보는 다음 문서를 참조하십시오.
* [서비스 패브릭 및 컨테이너.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

