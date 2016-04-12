<properties 
   pageTitle="센서 또는 다른 시스템으로부터 받은 데이터 사용자 알림 | Microsoft Azure"
   description="이벤트 허브를 사용하여 사용자에게 센서 데이터를 알리는 방법을 설명합니다."
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/08/2016"
   ms.author="spyros;sethm" />

# 센서 또는 다른 시스템으로부터 받은 데이터 사용자 알림

데이터를 실시간으로 모니터링하거나 일정에 따라 보고서를 생성하는 응용 프로그램이 있다고 가정합니다. 이러한 실시간 차트 또는 보고서를 표시하는 웹 사이트를 보는 경우 작업이 필요한 것이 표시될 수 있습니다. 웹 사이트를 확인하는 것에 의존하는 대신 해당 상황에 대해 알림을 받으려는 경우 어떻게 합니까? 온실에 생장 촉진 램프가 있고 램프가 꺼지는 경우 즉시 알아야 한다고 가정합니다. 이를 위한 한 가지 방법은 램프가 꺼지는 경우 이메일을 보내도록 온실에 램프 센서를 사용하는 것입니다.

![][1]

또 다른 시나리오에서는 애완동물 숙박 시설을 운영하고 낮은 재고 공급 레벨에 대해 알림을 받아야 한다고 가정합니다. 예를 들어 개 사료의 창고 재고가 심각한 수준으로 떨어진 경우 ERP 시스템에서 문자 메시지를 보내도록 할 수 있습니다.

![][2]

문제는 정적 보고서 확인을 해결할 때가 아니라 특정 조건이 충족되었을 때 중요한 정보를 가져오는 방법입니다. [Dynamics AX][]와 같은 장치 또는 엔터프라이즈 응용 프로그램에서 데이터를 수신하도록 [Azure 이벤트 허브][] 또는 [Azure IoT Hub][]를 사용하는 경우 처리하는 방법에 대한 몇 가지 옵션이 있습니다. 웹 사이트에서 보고 분석하고 저장하고 이를 사용하여 다른 작업을 하도록 명령을 트리거할 수 있습니다. 이를 위해 [Azure 웹 사이트][], [SQL Azure][], [HDInsight][], [Cortana Analytics Suite][], [IoT Suite][], [논리 앱][] 또는 [Azure 알림 허브][]와 같은 강력한 도구를 사용할 수 있습니다. 그러나 경우에 따라 최소 오버헤드로 누군가에게 해당 데이터를 보내고자 합니다. 약간의 코드로 작업을 수행하는 방법을 보여 주기 위해 새로운 샘플, [AppToNotifyUsers][]를 제공합니다. 포함된 옵션은 전자 메일(SMTP), SMS 및 전화입니다.

## 응용 프로그램 구조

응용 프로그램은 C#으로 작성되고 샘플의 추가 정보 파일은 응용 프로그램을 수정, 빌드 및 게시해야 하는 모든 정보를 포함합니다. 다음 섹션에서는 응용 프로그램 동작에 대한 대략적인 개요를 제공합니다.

Azure 이벤트 허브 또는 IoT Hub로 푸시되는 중요한 이벤트가 있다는 가정으로 시작합니다. 액세스하고 연결 문자열을 알면 모든 허브가 동작합니다.

이벤트 허브 또는 IoT Hub가 아직 없는 경우 [Connect The Dots](https://github.com/Azure/connectthedots) 프로젝트의 지침에 따라 Arduino shield 및 Raspberry Pi를 사용하여 테스트 베드를 쉽게 설정할 수 있습니다. Arduino shield의 램프 센서는 Pi를 통해 램프 레벨을 [Azure Event Hub][](**ehdevices**)에 보내고 수신된 램프 레벨이 특정 레벨 이하로 떨어지는 경우 [Azure 스트림 분석](https://azure.microsoft.com/services/stream-analytics/) 작업은 두 번째 이벤트 허브(**ehalerts**)에 알림을 푸시합니다.

**AppToNotify**가 시작할 때 구성 파일(App.config)을 읽어 알림을 받는 이벤트 허브에 대한 URL 및 자격 증명을 가져옵니다. 그런 다음 이를 통해 들어오는 모든 메시지에 대해 해당 이벤트 허브를 지속적으로 모니터링하는 프로세스를 생성합니다. - 이벤트 허브 또는 IoT Hub에 대한 URL 및 유효한 자격 증명에 액세스할 수 있는 한 이 이벤트 허브 판독기 코드는 들어오는 것을 지속적으로 읽습니다. 시작하는 동안 응용 프로그램은 사용하려는 메시징 서비스(전자 메일, SMS, 전화), 발신자 이름/주소 및 수신자 목록에 대한 URL 및 자격 증명을 읽습니다.

이벤트 허브 모니터가 메시지를 감지하면 구성 파일에서 지정된 메서드를 사용하여 해당 메시지를 전송하는 프로세스를 트리거합니다. 감지하는 모든 메시지를 보냅니다. 초당 10개의 메시지를 수신하는 이벤트 허브를 가리키도록 모니터를 설정하는 경우 발신자 초당 10개의 메시지 - 초당 10개의 전자 메일, 초당 10개의 SMS, 초당 10개의 전화 통화를 보냅니다. 해당 이유의 경우 센서 또는 응용 프로그램에서 모든 원시 데이터를 수신하는 이벤트 허브가 아닌 보내야 하는 알림만을 수신하는 이벤트 허브를 모니터링하는지 확인합니다.

## 적용 가능성

이 샘플의 코드는 이벤트 허브를 모니터링하는 방법 및 응용 프로그램에 이 기능을 추가하려는 이벤트의 외부 메시징 서비스를 호출하는 방법을 보여 줍니다. 이 솔루션은 DIY이며 개발자 중심 예제입니다. 중복성, 장애 조치, 실패 시 다시 시작 등과 같은 엔터프라이즈 요구 사항을 해결하지 않습니다. 더 많은 포괄적인 프로덕션 솔루션은 다음 항목을 참조하세요.

- [Azure 논리 앱](../app-service-logic/app-service-logic-connectors-list.md) 서비스를 사용하는 커넥터 또는 푸시 알림 사용.
- [Azure 알림 허브를 사용하여 수백만 개의 모바일 장치에 푸시 알림 브로드캐스트](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs) 블로그에 설명한 대로 [Azure 알림 허브](https://msdn.microsoft.com/library/azure/jj927170.aspx) 사용. 

## 다음 단계

수신자에게 전자 메일 또는 문자 메시지를 보내거나 이벤트 허브 또는 IoT Hub가 수신하는 데이터를 전달하도록 호출하는 간단한 알림 서비스를 만드는 것은 단순합니다. 이러한 허브에서 받은 데이터에 따라 사용자에게 알리는 솔루션을 배포하려면 [AppToNotifyUsers][]를 방문하세요.

해당 허브에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 이벤트 허브]
- [Azure IoT Hub]
- [이벤트 허브 자습서]로 시작합니다.
- [이벤트 허브를 사용하는 샘플 응용 프로그램] 전체.
- 서비스 버스 큐를 사용하는 [큐 메시징 솔루션].

이러한 허브에서 받은 데이터에 따라 사용자에게 알리는 솔루션을 배포하려면 다음을 방문하세요.

- [AppToNotifyUsers][]

[이벤트 허브 자습서]: event-hubs-csharp-ephcs-getstarted.md
[Azure IoT Hub]: https://azure.microsoft.com/services/iot-hub/
[Azure 이벤트 허브]: https://azure.microsoft.com/services/event-hubs/
[Azure Event Hub]: https://azure.microsoft.com/services/event-hubs/
[Azure 이벤트 허브]: https://azure.microsoft.com/services/event-hubs/
[이벤트 허브를 사용하는 샘플 응용 프로그램]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[큐 메시징 솔루션]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Azure 웹 사이트]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana Analytics Suite]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT Suite]: https://azure.microsoft.com/solutions/iot-suite/
[논리 앱]: https://azure.microsoft.com/services/app-service/logic/
[Azure 알림 허브]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png

<!---HONumber=AcomDC_0316_2016-->