<properties linkid="service-bus-manage-messaging-entitites" urlDisplayName="Traffic Manager" pageTitle="Manage Service Bus Messaging Entities - Azure" metaKeywords="" description="Learn how to create and manage your Service Bus entities using the Azure Management Portal." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="How to Manage Service Bus Messaging Entities" authors="sethm" solutions="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# 서비스 버스 메시징 엔터티를 관리하는 방법

이 항목에서는 [Azure 관리 포털][Azure 관리 포털]을 사용하여 서비스 버스 엔터티를 만들고 관리하는 방법을 설명합니다. 포털을 사용하여 새 서비스 네임스페이스 또는 메시징 엔터티(큐, 토픽 또는 구독)를 만들 수 있습니다. 엔터티를 삭제하거나 엔터티의 상태를 변경할 수도 있습니다.

## 목차

-   [방법: 서비스 버스 엔터티 만들기][방법: 서비스 버스 엔터티 만들기]
-   [방법: 서비스 버스 삭제][방법: 서비스 버스 삭제]
-   [방법: 서비스 버스 엔터티 사용 안 함 또는 사용][방법: 서비스 버스 엔터티 사용 안 함 또는 사용]
-   [추가 리소스][추가 리소스]

## <span id="create"></span></a>방법: 서비스 버스 엔터티 만들기

Azure 관리 포털에서는 서비스 버스 엔터티를 만드는 두 가지 방법, 즉 *빠른 생성*과 *사용자 지정 만들기*를 제공합니다.

### 빠른 생성

빠른 생성을 사용하면 하나의 간편한 단계로 서비스 버스 큐, 토픽 또는 릴레이 서비스 네임스페이스를 만들 수 있습니다. 다음 단계에 따라 서비스 버스 엔터티를 만드십시오.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그온합니다.
2.  관리 포털의 왼쪽 아래에서 **새로 만들기** 아이콘을 클릭합니다.
3.  **앱 서비스** 아이콘을 클릭한 후 **Service Bus 큐**(토픽 또는 릴레이)를 클릭합니다. **빠른 생성**을 클릭한 후 큐 이름, 지역 및 Azure 구독 ID를 입력합니다.

    a. 선택한 지역의 첫 네임스페이스인 경우 포털에서 [your entity name]-ns 형식의 네임스페이스 큐가 제안됩니다. 이 값을 변경할 수 있습니다.

    b. 이 지역의 서비스 네임스페이스가 이미 하나 이상 있는 경우 자동으로 네임스페이스가 선택됩니다. 선택된 이 네임스페이스를 변경할 수 있습니다.

4.  **새 큐 만들기**(또는 토픽) 옆에 있는 확인 표시를 클릭합니다.
5.  큐 또는 토픽이 만들어지면 **Creation of Queue ‘[Queue Name]’ Completed**라는 메시지가 나타납니다.

    a. 이 지역이나 이 Azure 구독에 네임스페이스가 없는 경우 새 네임스페이스가 자동으로 선택됩니다. 이 경우 두 가지의 성공 메시지, 즉 네임스페이스 만들기 성공과 엔터티 만들기 성공 메시지가 나타납니다.

    ![][0]

왼쪽 탐색 모음에서 **서비스 버스** 아이콘을 클릭하여 네임스페이스 목록을 가져옵니다. 방금 만든 새 네임스페이스가 나타납니다. 목록에서 네임스페이스를 클릭합니다. 방금 만든 엔터티가 네임스페이스 아래에 표시됩니다.

**참고** 나열된 네임스페이스가 즉시 표시되지 않을 수 있습니다. 서비스 네임스페이스를 만들고 포털 인터페이스를 업데이트하는 데 몇 초 정도 걸릴 수 있습니다.

**참고** 릴레이에 **빠른 생성**을 사용하면 새 릴레이 끝점이 만들어지지 않습니다. 프로그래밍 방식으로 릴레이 끝점을 만들 수 있는 네임스페이스만 만들어집니다. 자세한 내용은 [서비스 버스 설명서][서비스 버스 설명서](영문)를 참조하십시오.

### 사용자 지정 만들기

**사용자 지정 만들기**는 만들려는 엔터티(큐 또는 토픽)의 속성 기본값을 변경하는 옵션을 제공하는 더 세부적인 버전입니다. **사용자 지정 만들기**를 사용하여 토픽 또는 엔터티를 만들려면 다음 단계를 따릅니다.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그온합니다.
2.  관리 포털의 왼쪽 아래에서 **새로 만들기**를 클릭합니다.
3.  **앱 서비스** 아이콘을 클릭한 후 **Service Bus 큐**(토픽 또는 릴레이)를 클릭합니다. 그런 다음 사용자 지정 만들기를 클릭합니다.
4.  첫 번째 대화 상자 화면에서 큐 이름, 지역 및 Azure 구독 ID를 입력합니다.

    a. 선택한 지역의 첫 서비스 네임스페이스인 경우 포털에서 [your entity name]-ns 형식의 네임스페이스 큐가 제안됩니다. 이 값을 변경할 수 있습니다.

    b. 이 지역의 네임스페이스가 이미 하나 이상 있는 경우 자동으로 네임스페이스가 선택됩니다. 선택된 이 네임스페이스를 변경할 수 있습니다.

5.  **다음**을 클릭하여 나머지 속성을 삽입합니다.

    ![][1]

6.  확인 표시를 클릭하여 큐를 만듭니다.

    ![][2]

왼쪽 탐색 모음에서 **서비스 버스** 아이콘을 클릭하여 서비스 네임스페이스 목록을 가져옵니다. 방금 만든 새 네임스페이스가 나타납니다. 목록에서 네임스페이스를 클릭합니다. 방금 만든 엔터티가 네임스페이스 아래에 표시됩니다.

## <span id="delete"></span></a>방법: 서비스 버스 삭제

포털을 사용하여 서비스 버스 메시징 엔터티를 삭제할 수 있습니다. 이는 큐, 토픽 및 토픽 구독에 적용됩니다. 큐 또는 토픽을 삭제하려면 다음을 수행합니다.

1.  서비스 네임스페이스 목록 보기로 이동하고 엔터티(큐 또는 토픽)를 만들 때 사용한 네임스페이스를 클릭합니다.
2.  페이지 아래쪽에서 **삭제** 아이콘을 클릭하고 삭제 작업을 확인합니다.

    ![][3]

**참고** 엔터티 삭제는 복구할 수 없습니다. 삭제하고 나면 복구할 수 없습니다. 하지만 같은 이름을 사용하여 다른 엔터티를 만들 수 있습니다.

토픽 구독을 삭제하려면 다음을 수행합니다.

1.  네임스페이스 목록 보기로 이동하고 토픽을 만들 때 사용한 네임스페이스를 클릭합니다.
2.  구독을 만들 때 사용한 토픽을 클릭합니다.
3.  **구독** 탭을 클릭하고 삭제할 구독을 선택합니다.
4.  페이지 아래쪽에서 **삭제** 아이콘을 클릭하고 삭제 작업을 확인합니다.

## <span id="disableenable"></span></a>방법: 서비스 버스 엔터티 사용 안 함 또는 사용

포털을 사용하여 서비스 버스 엔터티의 상태를 변경할 수 있습니다. 이는 큐 및 토픽에 적용됩니다. 큐 또는 토픽을 사용하지 않거나 사용하도록 설정하려면 다음을 수행합니다.

1.  서비스 네임스페이스 목록 보기로 이동하고 엔터티(큐 또는 토픽)를 만들 때 사용한 네임스페이스를 클릭합니다.
2.  페이지 아래쪽에서 **사용 안 함**(또는 **사용**)을 클릭합니다.

    ![][4]

## <span id="seealso"></span></a>추가 리소스

[Azure 서비스 버스][Azure 서비스 버스]

Azure 웹 사이트의 [.NET 개발자 센터][.NET 개발자 센터]

[서비스 버스 토픽 및 구독을 사용하는 응용 프로그램 사용(영문)][서비스 버스 토픽 및 구독을 사용하는 응용 프로그램 사용(영문)]

[큐, 토픽 및 구독][큐, 토픽 및 구독]

  [Azure 관리 포털]: http://manage.windowsazure.com
  [방법: 서비스 버스 엔터티 만들기]: #create
  [방법: 서비스 버스 삭제]: #delete
  [방법: 서비스 버스 엔터티 사용 안 함 또는 사용]: #disableenable
  [추가 리소스]: #seealso
  [0]: ./media/service-bus-manage-message-entities/QueueQuickCreate.png
  [서비스 버스 설명서]: http://www.windowsazure.com/ko-KR/develop/net/how-to-guides/service-bus-relay/
  [1]: ./media/service-bus-manage-message-entities/AddQueue1.png
  [2]: ./media/service-bus-manage-message-entities/ConfigureQueue.png
  [3]: ./media/service-bus-manage-message-entities/DeleteEntity.png
  [4]: ./media/service-bus-manage-message-entities/DisableEnable.png
  [Azure 서비스 버스]: http://go.microsoft.com/fwlink/?LinkId=266834
  [.NET 개발자 센터]: http://go.microsoft.com/fwlink/?LinkID=262187
  [큐, 토픽 및 구독]: http://go.microsoft.com/fwlink/?LinkId=264291
