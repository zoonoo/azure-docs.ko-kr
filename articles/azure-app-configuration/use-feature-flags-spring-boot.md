---
title: Spring Boot 앱에서 기능 플래그 사용에 관한 자습서 - Azure App Configuration | Microsoft Docs
description: 이 자습서에서는 Spring Boot 앱에서 기능 플래그를 구현하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: d924975d852320fcddd5ae988f1d52f10d366f81
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790748"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>자습서: Spring Boot 앱에서 기능 플래그 사용

Spring Boot 핵심 기능 관리 라이브러리는 Spring Boot 애플리케이션에서 기능 플래그를 구현할 수 있는 지원을 제공합니다. 이러한 라이브러리를 사용하여 코드에 기능 플래그를 선언적으로 추가할 수 있습니다.

또한 기능 관리 라이브러리는 배후 상황의 기능 플래그 수명 주기를 관리합니다. 예를 들어 라이브러리에서 플래그 상태를 새로 고치고 캐싱하거나 요청 호출 중에 플래그 상태의 변경이 불가능하도록 보장합니다. 뿐만 아니라 Spring Boot 라이브러리는 MVC 컨트롤러 작업, 경로 및 미들웨어를 비롯한 통합을 제공합니다.

[Spring Boot 앱에 기능 플래그 추가 빠른 시작](./quickstart-feature-flag-spring-boot.md)에서는 Spring Boot 애플리케이션에서 기능 플래그를 추가하는 다양한 방법을 보여줍니다. 이 자습서에서는 해당 방법을 자세히 설명합니다.

이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 애플리케이션의 핵심 부분에 기능 가용성을 제어하는 기능 플래그를 추가합니다.
> * App Configuration과 통합하여 기능 플래그를 관리하는 데 사용합니다.

## <a name="set-up-feature-management"></a>기능 관리 설정

Spring Boot 기능 관리자 `FeatureManager`는 프레임워크의 기본 구성 시스템에서 기능 플래그를 가져옵니다. 따라서 로컬 *bootstrap.yml* 파일 또는 환경 변수를 포함하여 Spring Boot에서 지원하는 구성 원본을 사용하여 애플리케이션의 기능 플래그를 정의할 수 있습니다. `FeatureManager`는 종속성 주입을 사용합니다. 표준 규칙을 사용하여 기능 관리 서비스를 등록할 수 있습니다.

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

기능 플래그를 애플리케이션 외부에 유지하면서 별도로 관리하는 것이 좋습니다. 이렇게 하면 언제든지 플래그 상태를 수정하고 해당 변경 내용을 애플리케이션에 즉시 적용할 수 있습니다. App Configuration은 전용 포털 UI를 통해 모든 기능 플래그를 구성 및 제어하기 위한 중앙 위치를 제공합니다. 또한 App Configuration은 자체의 Spring Boot 클라이언트 라이브러리를 통해 플래그를 애플리케이션에 직접 제공합니다.

Spring Boot 애플리케이션을 App Configuration에 연결하는 가장 쉬운 방법은 구성 공급자를 통하는 것입니다.

### <a name="spring-cloud-11x"></a>Spring Cloud 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Spring Cloud 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>기능 플래그 선언

각 기능 플래그는 기능의 상태가 *on*(즉, 값이 `True`)인지 평가하는 데 사용되는 하나 이상의 필터 이름과 목록으로 구성됩니다. 필터는 기능을 켜야 하는 사용 사례를 정의합니다.

기능 플래그에 여러 필터가 있는 경우 그 중 한 필터가 기능을 사용하도록 설정해야 한다고 확인할 때까지 필터 목록이 순서대로 트래버스됩니다. 해당 시점에서 기능 플래그가 *on*으로 간주되므로 나머지 필터 결과를 건너뜁니다. 기능을 사용하도록 설정해야 한다는 필터가 없으면 기능 플래그가 *off*됩니다.

기능 관리자는 기능 플래그의 구성 원본으로 *application.yml*을 지원합니다. 다음 예제에서는 YAML 파일에서 기능 플래그를 설정하는 방법을 보여줍니다.

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

규칙에 따라 이 YML 문서의 `feature-management` 섹션은 기능 플래그 설정에 사용됩니다. 앞의 예제에서는 `EnabledFor` 속성에 필터가 정의된 다음 세 가지 기능 플래그를 보여줍니다.

* `feature-a`는 *on*입니다.
* `feature-b`는 *off*입니다.
* `feature-c`는 `parameters` 속성을 사용하여 `Percentage`라는 필터를 지정합니다. `Percentage`는 구성 가능한 필터입니다. 이 예제에서 `Percentage`는 `feature-c` 플래그가 *on*이 될 50% 확률을 지정합니다.

## <a name="feature-flag-checks"></a>기능 플래그 확인

먼저 기능 플래그가 *on*으로 설정되었는지 확인하는 것이 기능 관리의 기본 패턴입니다. on으로 설정된 경우 기능 관리자는 해당 기능에 포함된 작업을 실행합니다. 다음은 그 예입니다.

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>종속성 주입

Spring Boot에서 종속성 주입을 통해 기능 관리자 `FeatureManager`에 액세스할 수 있습니다.

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>컨트롤러 작업

MVC 컨트롤러에서 `@FeatureGate` 특성을 사용하여 특정 작업을 사용할 것인지 제어합니다. 다음 `Index` 작업은 `feature-a`가 *on*으로 설정되어야만 실행할 수 있습니다.

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

제어하는 기능 플래그가 *off*로 설정되어 MVC 컨트롤러 또는 작업이 차단되면 등록된 `IDisabledFeaturesHandler` 인터페이스가 호출됩니다. 기본 `IDisabledFeaturesHandler` 인터페이스는 응답 본문 없이 404 상태 코드를 클라이언트에 반환합니다.

## <a name="mvc-filters"></a>MVC 필터

MVC 필터를 기능 플래그의 상태에 따라 활성화되도록 설정할 수 있습니다. 다음 코드는 `FeatureFlagFilter`라는 MVC 필터를 추가합니다. 이 필터는 `feature-a`가 사용되는 경우에만 MVC 파이프라인 내에서 트리거됩니다.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>경로

기능 플래그를 사용하여 경로를 리디렉션할 수 있습니다. `feature-a`에서 사용자를 리디렉션하는 다음 코드를 사용할 수 있습니다.

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Spring Boot 애플리케이션에서 `spring-cloud-azure-feature-management-web` 라이브러리를 사용하여 기능 플래그를 구현하는 방법을 알아보았습니다. Spring Boot 및 App Configuration의 기능 관리 지원에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Spring Boot 기능 플래그 샘플 코드](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [기능 플래그 관리](./manage-feature-flags.md)
