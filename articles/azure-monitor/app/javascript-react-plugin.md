---
title: Application Insights JavaScript SDK 용 플러그 인 반응
description: JavaScript SDK Application Insights에 대 한 응답 플러그 인을 설치 하 고 사용 하는 방법입니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3348654a83b6d0930d10e1f58e07455623b5861d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981088"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 용 플러그 인 반응

Application Insights JavaScript SDK에 대 한 플러그 인에 대응 하 여 다음을 사용 합니다.

- 경로 변경 내용 추적
- 구성 요소 사용 통계에 반응

## <a name="getting-started"></a>시작

Npm 패키지를 설치 합니다.

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>기본 사용법

Application Insights에 대 한 연결을 초기화 합니다.

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

상위 구성 요소 함수로 구성 요소를 래핑하여 Application Insights를 사용 하도록 설정 합니다.

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin, appInsights, MyComponent);
```

## <a name="configuration"></a>Configuration

| 속성    | 기본값 | 설명                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | 라우터 기록에 반응 합니다. 자세한 내용은 [라우터 패키지 응답 설명서](https://reactrouter.com/web/api/history)를 참조 하세요. 구성 요소 외부에서 기록 개체에 액세스 하는 방법을 알아보려면 [라우터 응답 FAQ](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components) 를 참조 하세요.    |

### <a name="react-components-usage-tracking"></a>구성 요소 사용 추적에 대응

다양 한 반응 구성 요소 사용 추적을 계측 하려면 `withAITracking` 고차 구성 요소 기능을 적용 합니다.

이벤트를 통해 이벤트에서 시간을 측정 합니다 `ComponentDidMount` `ComponentWillUnmount` . 그러나이를 보다 정확 하 게 하기 위해 사용자가 유휴 상태 였던 시간을 빼는 것 `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` 입니다.

Azure Portal에서 Application Insights 리소스로 이동 해야 하는이 메트릭을 확인 하려면 "메트릭" 탭을 선택 하 고 "메트릭" 탭을 선택 하 여 빈 차트를 구성 하 고, 사용자 지정 메트릭 이름 "구성 요소에 반응 하는 시간 (초)"을 표시 하 고, 메트릭의 집계 (합계, 평균 등)를 선택 하 고, 분할을 "Component Name"으로 적용

!["구성 요소 참여 시간 (초)" "구성 요소 이름으로 분할"의 사용자 지정 메트릭을 표시 하는 차트의 스크린샷](./media/javascript-react-plugin/chart.png)

사용자 지정 쿼리를 실행 하 여 요구 사항에 따라 보고서 및 시각화를 생성 하 Application Insights 데이터를 분할할 수도 있습니다. Azure Portal에서 Application Insights 리소스로 이동 하 여 개요 탭의 상단 메뉴에서 "분석"을 선택 하 고 쿼리를 실행 합니다.

![사용자 지정 메트릭 쿼리 결과의 스크린샷](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 새 사용자 지정 메트릭이 Azure Portal에 표시 되는 데 최대 10 분이 걸릴 수 있습니다.

## <a name="using-react-hooks"></a>반응 후크 사용

[후크 후크](https://reactjs.org/docs/hooks-reference.html) 는 클래스 기반 반응 구성 요소에 의존 하지 않고 반응 응용 프로그램의 상태 및 수명 주기 관리에 대 한 접근 방식입니다. Application Insights에 반응 하는 플러그 인은 더 높은 순서 구성 요소 방법과 유사한 방식으로 작동 하는 여러 후크 통합을 제공 합니다.

### <a name="using-react-context"></a>반응 컨텍스트 사용

Application Insights에 대 한 반응 후크는이를 포함 하는 측면으로 [반응 컨텍스트](https://reactjs.org/docs/context.html) 를 사용 하도록 설계 되었습니다. 컨텍스트를 사용 하려면 위와 같이 Application Insights를 초기화 하 고 컨텍스트 개체를 가져옵니다.

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

이 컨텍스트 공급자는 Application Insights `useContext` 모든 자식 구성 요소 내에서 후크에 사용할 수 있도록 합니다.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

후크는 구성 요소 `useTrackMetric` `withAITracking` 구조에 추가 구성 요소를 추가 하지 않고 고차 구성 요소의 기능을 복제 합니다. 후크는 두 개의 인수를 사용 합니다. 즉, 후크에서 가져올 수 있는 Application Insights 인스턴스는 `useAppInsightsContext` 추적을 위한 구성 요소 (예: 이름)의 식별자입니다.

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

이는 상위 구성 요소 처럼 작동 하지만 구성 요소 수명 주기 대신 후크 수명 주기 이벤트에 응답 합니다. 특정 상호 작용에서 실행 해야 하는 경우 사용자 이벤트에 후크를 명시적으로 제공 해야 합니다.

### `useTrackEvent`

`useTrackEvent`후크는 응용 프로그램이 추적 해야 할 수 있는 사용자 지정 이벤트 (예: 단추 클릭 또는 기타 API 호출)를 추적 하는 데 사용 됩니다. 두 개의 인수를 사용 합니다. 첫 번째 인수는 후크에서 가져올 수 있는 Application Insights 인스턴스이고 `useAppInsightsContext` 이벤트의 이름입니다.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

후크를 사용 하는 경우 데이터 페이로드를 제공 하 여 Application Insights에 저장 될 때 이벤트에 추가 데이터를 추가할 수 있습니다.

## <a name="react-error-boundaries"></a>오류 경계 반응

[오류 경계](https://reactjs.org/docs/error-boundaries.html) 는 반응 응용 프로그램 내에서 발생 하는 경우 예외를 정상적으로 처리 하는 방법을 제공 하며, 이러한 오류가 발생 하면 예외를 기록해 야 할 가능성이 있습니다. Application Insights에 대 한 응답 플러그 인은 오류가 발생할 때 자동으로 기록 하는 오류 경계 구성 요소를 제공 합니다.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

에는 `AppInsightsErrorBoundary` 두 개의 props를 전달 해야 하며, `ReactPlugin` 응용 프로그램에 대해 생성 된 인스턴스와 오류가 발생할 때 렌더링할 구성 요소가 필요 합니다. 처리 되지 않은 오류가 발생 하면 `trackException` 오류 경계에 제공 된 정보를 사용 하 여이 호출 되 고 `onError` 구성 요소가 표시 됩니다.

## <a name="sample-app"></a>샘플 앱

[Application Insights에 반응](https://github.com/Azure-Samples/application-insights-react-demo)하는 데모를 확인 하세요.

## <a name="next-steps"></a>다음 단계

- JavaScript SDK에 대해 자세히 알아보려면 [Application Insights JAVASCRIPT sdk 설명서](javascript.md)를 참조 하세요.
- Kusto 쿼리 언어에 대해 알아보고 Log Analytics에서 데이터를 쿼리 하는 방법에 대 한 자세한 내용은 [로그 쿼리 개요](../../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.
