<properties linkid="scripting-center-index" urlDisplayName="index" pageTitle="How to Manage Origins in a Media Services Account" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Origins in a Media Services Account" authors="juliako"  solutions="" writer="juliako" manager="" editor=""  />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <span id="managemediaservicesorigins"></span></a>미디어 서비스 계정에서 원본을 관리하는 방법

미디어 서비스를 사용하여 계정에 여러 스트리밍 원본을 추가하고 원본을 구성할 수 있습니다. 각 미디어 서비스 계정은 **default**라는 하나 이상의 스트리밍 원본이 연결되어 있습니다.

## 원본 추가 및 삭제

1.  [관리 포털][관리 포털]에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.
2.  ORIGINS 페이지를 선택합니다.
3.  페이지 아래쪽에 있는 추가 또는 삭제 단추를 클릭합니다. default 원본은 삭제할 수 없습니다.
4.  시작 단추를 클릭하여 원본을 시작합니다.
5.  원본을 구성하려면 원본의 이름을 클릭합니다.

    ![원본 페이지][원본 페이지]

## 원본 구성

구성 탭에서 다음 구성을 수행할 수 있습니다.

1.  HTTP 응답의 캐시 제어 헤더에서 지정할 최대 캐싱 기간을 설정합니다. 이 값은 Blob 콘텐츠에 명시적으로 설정된 최대 캐시 값을 재정의하지 않습니다.

2.  게시된 스트리밍 끝점에 연결할 수 있는 IP 주소를 지정합니다. IP 주소를 지정하지 않은 경우 모든 IP 주소에서 연결할 수 있습니다.

3.  Akamai 서명 헤더 인증에 대한 구성을 지정합니다.

    ![원본 구성][원본 구성]

    이 페이지의 구성은 하나 이상의 예약 단위가 있는 원본에만 적용됩니다. 주문형 스트리밍 예약 단위를 예약하려면 SCALE 탭으로 이동합니다. 예약 단위에 대한 자세한 내용은 [미디어 서비스 크기 조정][미디어 서비스 크기 조정](영문)을 참조하세요.

  [관리 포털]: https://manage.windowsazure.com/
  [원본 페이지]: ./media/media-services-manage-origins/media-services-origins-page.png
  [원본 구성]: ./media/media-services-manage-origins/media-services-origins-configure.png
  [미디어 서비스 크기 조정]: http://go.microsoft.com/fwlink/?LinkID=275847&clcid=0x409/
