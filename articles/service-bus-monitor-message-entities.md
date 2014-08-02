<properties linkid="service-bus-monitor-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Monitor Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to monitor your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Monitor Service Bus Messaging Entities" authors="" solutions="" />

서비스 버스 메시징 엔터티를 모니터링하는 방법
=============================================

이 항목에서는 [Azure 관리 포털](http://manage.windowsazure.com)을 사용하여 서비스 버스 엔터티를 관리하고 모니터링하는 방법을 설명합니다. 포털에서는 큐와 토픽의 상태를 전체적으로 볼 수 있습니다. 또한 사용 상황을 모니터링할 수도 있습니다.

서비스 버스 큐에 대한 활동을 모니터링하는 방법
----------------------------------------------

서비스 버스 큐를 모니터링하려면 다음을 수행합니다.

1.  [Azure(Preview) 관리 포털](http://manage.windowsazure.com)에 로그온합니다.
2.  왼쪽 탐색 모음에서 **서비스 버스** 아이콘을 클릭하여 서비스 네임스페이스 목록을 가져옵니다.
3.  모니터링할 큐가 포함된 네임스페이스를 클릭합니다.
4.  페이지의 상단에 있는 피벗 모음에서 **큐**를 클릭합니다.
5.  모니터링할 큐의 이름을 클릭합니다. 큐 대시보드가 나타납니다.
6.  만든 큐에 대한 활동을 볼 수 있습니다. 이 정보를 여러 기간에 걸쳐 확인할 수 있습니다. 기본값은 1시간이지만, 시간 옆에 있는 드롭다운을 클릭하여 다른 기간을 최근 24시간, 7일 또는 최근 30일 등으로 선택할 수 있습니다. 1시간 내에 5분 측정점, 24시간 내에 1시간, 7일 및 30일 내에 1일 등의 높은 정밀도로 데이터를 표시할 수 있습니다.

큐의 경우 다음 차트를 표시할 수 있습니다.

-   **들어오는 메시지**: 이 시간 간격 동안 큐에 들어오는 메시지 수입니다.
-   **보내는 메시지**: 이 시간 간격 동안 큐에서 제거되는 메시지 수입니다.
-   **길이**: 이 시간 간격 끝에 엔터티에 있는 메시지 수입니다.
-   **크기**: 이 시간 간격 끝에 이 엔터티가 사용 중인 저장소 공간(MB)입니다.

### 간략 상태

대시보드의 **간략 상태**는 큐의 현재 크기를 **큐 길이**로 반영합니다. 큐 또는 토픽의 다른 속성도 표시합니다. 이 정보는 10초마다 한 번씩 새로 고쳐집니다.

![](./media/service-bus-monitor-message-entities/QueueDashboard.png)

서비스 버스 토픽에 대한 활동을 모니터링하는 방법
------------------------------------------------

서비스 버스 토픽을 모니터링하려면 다음을 수행합니다.

1.  [Azure(Preview) 관리 포털](http://manage.windowsazure.com)에 로그온합니다.
2.  왼쪽 탐색 모음에서 **서비스 버스** 아이콘을 클릭하여 서비스 네임스페이스 목록을 가져옵니다.
3.  모니터링할 토픽이 포함된 네임스페이스를 클릭합니다.
4.  페이지의 상단에 있는 피벗 모음에서 **토픽**을 클릭합니다.
5.  모니터링할 토픽의 이름을 클릭합니다. 토픽 대시보드가 나타납니다.

토픽 대시보드는 사용량 메트릭을 제외하고는 큐 대시보드와 비슷합니다. 보내는 메시지 및 길이는 토픽의 구독별로 다르기 때문에 토픽 대시보드에 나타나지 않습니다. **모니터** 탭에서는 토픽 구독별 사용량 메트릭(보내는 메시지 수 및 길이)을 추가할 수 있습니다. 이 메트릭을 추가하려면 **모니터** 탭을 클릭합니다. 그런 다음 페이지 맨 아래에서 **메트릭 추가**를 클릭한 다음 토픽 아래에 있는 구독 중에서 선택합니다.

![](./media/service-bus-monitor-message-entities/AddMetrics.png)

