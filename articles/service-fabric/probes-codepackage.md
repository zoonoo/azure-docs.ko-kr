---
title: Azure Service Fabric 프로브
description: 응용 프로그램 및 서비스 매니페스트 파일을 사용 하 여 Azure Service Fabric에서 선거의 프로브를 모델링 하는 방법입니다.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82137589"
---
# <a name="liveness-probe"></a>선거의 프로브
7.1 버전부터 Azure Service Fabric는 [컨테이너 화 된][containers-introduction-link] 응용 프로그램에 대 한 선거의 프로브 메커니즘을 지원 합니다. 선거의 프로브는 빠르게 응답 하지 않는 경우 다시 시작 되는 컨테이너 화 된 응용 프로그램의 선거의를 보고 하는 데 도움이 됩니다.
이 문서에서는 매니페스트 파일을 사용 하 여 선거의 프로브를 정의 하는 방법에 대 한 개요를 제공 합니다.

이 문서를 진행 하기 전에 [Service Fabric 응용 프로그램 모델][application-model-link] 및 [Service Fabric 호스팅 모델][hosting-model-link]에 대해 잘 알고 있어야 합니다.

> [!NOTE]
> 선거의 프로브는 NAT 네트워킹 모드의 컨테이너에 대해서만 지원 됩니다.

## <a name="semantics"></a>의미 체계
컨테이너 당 하나의 선거의 프로브를 지정 하 고 다음 필드를 사용 하 여 해당 동작을 제어할 수 있습니다.

* `initialDelaySeconds`: 컨테이너가 시작 된 후 프로브 실행을 시작 하는 초기 지연 시간 (초)입니다. 지원 되는 값은 **int**입니다. 기본값은 0이 고 최소값은 0입니다.

* `timeoutSeconds`: 프로브를 실패 한 것으로 간주 하는 시간 (초)입니다 (성공적으로 완료 되지 않은 경우). 지원 되는 값은 **int**입니다. 기본값은 1이 고 최소값은 1입니다.

* `periodSeconds`: 프로브 빈도를 지정 하는 기간 (초)입니다. 지원 되는 값은 **int**입니다. 기본값은 10이 고 최소값은 1입니다.

* `failureThreshold`:이 값에 도달 하면 컨테이너가 다시 시작 됩니다. 지원 되는 값은 **int**입니다. 기본값은 3이 고 최소값은 1입니다.

* `successThreshold`: 실패 한 경우 프로브를 성공적으로 실행 하려면이 값을 성공적으로 실행 해야 합니다. 지원 되는 값은 **int**입니다. 기본값은 1이 고 최소값은 1입니다.

언제 든 지 하나의 컨테이너에 하나의 프로브가 있을 수 있습니다. 검색이 **Timeoutseconds**로 설정 된 시간 내에 완료 되지 않으면 대기 하 고 **카운터가 failurethreshold**시간을 계산 합니다. 

또한 Service Fabric는 **DeployedServicePackage**에서 다음 프로브 [상태 보고서][health-introduction-link] 를 발생 시킵니다.

* `OK`: **SuccessThreshold**에 설정 된 값에 대해 프로브가 성공 합니다.

* `Error`: 컨테이너를 **failureCount**  ==   다시 시작 하기 전에 프로브 failureCount**카운터가 failurethreshold**입니다.

* `Warning`: 
    * 프로브에 실패 하 고 **failureCount**  <  **카운터가 failurethreshold**입니다. 이 상태 보고서는 **failureCount** 가 **카운터가 failurethreshold** 또는 **successThreshold**에 설정 된 값에 도달할 때까지 유지 됩니다.
    * 오류가 발생 한 후에도 경고는 연속 해 서 연속 해 서 업데이트 된 상태로 유지 됩니다.

## <a name="specifying-a-liveness-probe"></a>선거의 프로브 지정

**ServiceManifestImport**의 ApplicationManifest.xml 파일에서 프로브를 지정할 수 있습니다.

프로브는 다음 중 하나일 수 있습니다.

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>HTTP 프로브

HTTP 프로브의 경우 Service Fabric는 지정 된 포트 및 경로에 HTTP 요청을 보냅니다. 200 보다 크거나 같고 400 보다 작은 반환 코드는 성공을 나타냅니다.

HTTP 프로브를 지정 하는 방법의 예는 다음과 같습니다.

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

HTTP 프로브에는 설정할 수 있는 추가 속성이 있습니다.

* `path`: HTTP 요청에서 사용할 경로입니다.

* `port`: 프로브에 사용할 포트입니다. 이 속성은 필수입니다. 범위는 1 ~ 65535입니다.

* `scheme`: 코드 패키지에 연결 하는 데 사용 하는 체계입니다. 이 속성이 HTTPS로 설정 되어 있으면 인증서 확인을 건너뜁니다. 기본 설정은 HTTP입니다.

* `httpHeader`: 요청에 설정할 헤더입니다. 여러 헤더를 지정할 수 있습니다.

* `host`: 연결할 호스트 IP 주소입니다.

### <a name="tcp-probe"></a>TCP 프로브

TCP 프로브의 경우 Service Fabric는 지정 된 포트를 사용 하 여 컨테이너에서 소켓을 열려고 시도 합니다. 연결을 설정할 수 있는 경우 프로브는 성공으로 간주 됩니다. TCP 소켓을 사용 하는 프로브를 지정 하는 방법의 예는 다음과 같습니다.

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

### <a name="exec-probe"></a>Exec 프로브

이 프로브는 **exec** 명령을 컨테이너에 발행 하 고 명령이 완료 될 때까지 기다립니다.

> [!NOTE]
> **Exec** 명령은 쉼표로 구분 된 문자열을 사용 합니다. 다음 예제의 명령은 Linux 컨테이너에 대해 작동 합니다.
> Windows 컨테이너를 검색 하려는 경우 **cmd**를 사용 합니다.

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
관련 정보는 다음 문서를 참조 하세요.
* [Service Fabric 및 컨테이너][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

