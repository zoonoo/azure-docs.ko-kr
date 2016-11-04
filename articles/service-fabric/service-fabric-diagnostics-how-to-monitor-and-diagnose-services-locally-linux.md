---
title: Azure 서비스 패브릭으로 작성된 서비스를 로컬에서 모니터링 및 진단 | Microsoft Docs
description: 로컬 개발 컴퓨터에서 Microsoft Azure 서비스 패브릭을 사용하여 작성된 서비스를 모니터링하고 진단하는 방법에 대해 알아보세요.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2016
ms.author: subramar

---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>로컬 컴퓨터 개발 설정에서의 모니터링 및 진단 서비스
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

모니터링, 감지, 진단 및 문제 해결을 통해 사용자 환경에 미치는 영향을 최소화하면서 서비스를 계속할 수 있습니다. 모니터링 및 진단은 실제 배포된 프로덕션 환경에 중요합니다. 서비스를 개발하는 동안 유사한 모델을 채택하면 프로덕션 환경으로 이동 시 진단 파이프라인이 작동합니다. 서비스 패브릭을 사용하면 서비스 개발자가 단일 컴퓨터 로컬 개발 설정과 실제 사용 프로덕션 클러스터 설정 양쪽에서 매끄럽게 작동하는 진단 기능을 간편하게 구현할 수 있습니다.

## <a name="debugging-service-fabric-java-applications"></a>Service Fabric Java 응용 프로그램 디버깅
Java 응용 프로그램에 대해 [다중 로깅 프레임워크](http://en.wikipedia.org/wiki/Java_logging_framework) 를 사용할 수 있습니다. `java.util.logging` 이 JRE의 기본 옵션이므로 [GitHub의 코드 예제](http://github.com/Azure-Samples/service-fabric-java-getting-started)에도 사용됩니다.  다음 논의에서는 `java.util.logging` 프레임워크를 구성하는 방법을 설명합니다. 

java.util.logging을 사용하면 응용 프로그램 로그를 메모리, 출력 스트림, 콘솔 파일 또는 소켓으로 리디렉션할 수 있습니다. 이들 옵션 각각에 대해 프레임워크에 이미 제공되어 있는 기본 핸들러가 있습니다. `app.properties` 파일을 만들어서 응용 프로그램에 대한 파일 핸들러가 모든 로그를 로컬 파일로 리디렉션하도록 구성할 수 있습니다. 

다음 코드 조각은 예제 구성을 포함합니다. 

```java 
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

`app.properties` 파일이 가리키는 폴더가 반드시 존재해야 합니다. `app.properties` 파일이 생성된 후에는 `<applicationfolder>/<servicePkg>/Code/` 폴더의 진입점 스크립트 `entrypoint.sh`가 속성 `java.util.logging.config.file`을 `app.propertes` 파일로 설정하도록 수정도 해야 합니다. 항목은 다음 코드 조각과 같습니다.

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


이 구성의 결과로 로그가 순환 방식으로 `/tmp/servicefabric/logs/`에 수집됩니다. **%u** 및 **%g**는 mysfapp0.log, mysfapp1.log 등의 파일 이름으로 파일을 더 만들도록 허용합니다. 기본적으로 핸들러가 명시적으로 구성되지 않으면 콘솔 핸들러가 등록됩니다. /var/log/syslog 아래 syslog에서 로그를 볼 수 있습니다.

자세한 내용은 [GitHub의 코드 예제](http://github.com/Azure-Samples/service-fabric-java-getting-started)를 참조하세요.  

## <a name="next-steps"></a>다음 단계
응용 프로그램에 추가된 동일한 추적 코드 역시 Azure 클러스터에서 응용 프로그램의 진단과 함께 작동합니다. 도구에 대한 다양한 옵션과 도구를 설정하는 방법에 대해 설명하는 이러한 문서를 확인합니다.

* [Azure 진단을 사용하여 로그를 수집하는 방법](service-fabric-diagnostics-how-to-setup-lad.md)

<!--HONumber=Oct16_HO2-->


