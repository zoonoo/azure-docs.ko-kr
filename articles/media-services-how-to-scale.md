<properties linkid="manage-services-mediaservices-scale-media-service" urlDisplayName="How to scale" pageTitle="How to Scale a media service | Azure Documentation" metaKeywords="" description="Learn how to scale Media Services by specifying the number of On-Demand Streaming Reserved Units and Encoding Reserved Units that you would like your account to be provisioned with." metaCanonical="" services="media-services" documentationCenter="" title="How to Scale a Media Service" authors="migree" solutions="" manager="" editor="" />

미디어 서비스를 확장하는 방법
=============================

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

계정에서 프로비전할 **주문형 스트리밍 예약 단위** 및 **인코딩 예약 단위**의 수를 지정하여 미디어 서비스를 확장할 수 있습니다.

주문형 스트리밍 예약 단위
-------------------------

주문형 스트리밍 예약 단위는 200Mbps의 증분으로 구입할 수 있는 전용 송신 용량 및 [동적 패키징 기능](http://go.microsoft.com/fwlink/?LinkId=276874)(영문)이 현재 포함된 추가 기능을 제공합니다. 기본적으로 주문형 스트리밍은 다른 모든 사용자와 서버 리소스(예: 계산, 송신 기능 등)가 공유되는 공유 인스턴스 모델로 구성되어 있습니다. 주문형 스트리밍 처리량을 개선하려면 주문형 스트리밍 예약 단위를 구입하는 것이 좋습니다.

주문형 스트리밍 예약 단위의 수를 변경하려면 다음을 수행합니다.

1.  [관리 포털](https://manage.windowsazure.com/)에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.

2.  ORIGINS 페이지를 선택합니다. 그런 다음, 수정할 원본을 클릭합니다.

    ![원본 페이지](./media/media-services-how-to-scale/media-services-origin-page.png)

3.  예약 단위 수를 지정하려면 크기 조정 탭을 선택하고 **reserved capacity** 슬라이더를 움직입니다.

    ![크기 조정 페이지](./media/media-services-how-to-scale/media-services-origin-scale.png)

4.  저장 단추를 눌러 변경 내용을 저장합니다.

    새 주문형 스트리밍 단위를 할당하는 작업은 완료하는 데 20분 정도 걸립니다.

    **참고:** 현재, 주문형 스트리밍 단위의 양수 값을 0으로 변경하면 최대 1시간 동안 주문형 스트리밍을 사용하지 않을 수 있습니다.

    **참고:** 24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다. 가격 정보에 대한 자세한 내용은 [미디어 서비스 가격 정보](http://go.microsoft.com/fwlink/?LinkId=275107)를 참조하십시오.

인코딩 예약 단위
----------------

프로비전된 인코딩 예약 단위의 수는 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수와 같습니다. 예를 들어 계정에 5개의 예약 단위가 있는 경우 처리할 미디어 작업이 있다면 5개의 미디어 작업이 동시에 실행됩니다. 나머지 작업은 큐에 대기하다가 실행 중인 작업이 완료되자마자 순차적으로 처리를 위해 선택됩니다. 계정에 프로비전된 예약 단위가 없는 경우에는 작업이 순차적으로 선택됩니다. 이 경우 한 작업 완료와 다음 작업 시작 사이의 대기 시간은 시스템의 리소스 가용성에 따라 다릅니다.

인코딩 예약 단위의 수를 변경하려면 다음을 수행합니다.

1.  [관리 포털](https://manage.windowsazure.com/)에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.

2.  프로세서 페이지를 선택합니다.

    ![프로세서 페이지](./media/media-services-how-to-scale/media-services-encoding-scale.png)

3.  저장 단추를 눌러 변경 내용을 저장합니다.

    새 인코딩 예약 단위가 거의 즉시 할당됩니다.

    **참고:** 24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다.

지원 티켓 열기
--------------

기본적으로 모든 미디어 서비스 계정은 인코딩 예약 단위 최대 25개, 주문형 스트리밍 예약 단위 최대 5개까지 확장할 수 있습니다. 지원 티켓을 열어 더 높은 한도를 요청할 수 있습니다.

지원 티켓을 열려면 다음을 수행합니다.

1.  [관리 포털](http://manage.windowsazure.com)에서 Azure 계정으로 로그인합니다.
2.  [지원](http://www.windowsazure.com/ko-kr/support/contact/)으로 이동합니다.
3.  "지원 받기"를 클릭합니다.
4.  사용 중인 구독을 선택합니다.
5.  지원 유형으로 "기술적"을 선택합니다.
6.  "티켓 만들기"를 클릭합니다.
7.  다음 페이지에 나타나는 제품 목록에서 "Azure 미디어 서비스"를 선택합니다.
8.  "문제 유형"으로 "미디어 처리"를 선택한 후 범주 아래에서 "Reservation Units"를 선택합니다.
9.  계속을 클릭합니다.
10. 다음 페이지의 지침을 따르고 필요한 인코딩 예약 단위 또는 주문형 스트리밍 예약 단위 수를 자세히 입력하십시오.
11. 제출을 클릭하여 티켓을 엽니다.

