---
title: 포털 및 Wirecast를 사용하여 Azure Media Services 라이브 스트림 만들기
description: Azure Media Service 라이브 스트림을 만드는 방법을 알아봅니다.
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/06/2020
ms.openlocfilehash: c0eaf3907cbfcd86424b1d2cbc03930a7af72786
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "78927576"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>포털 및 Wirecast를 사용하여 Azure Media Services 라이브 스트림 만들기

이 시작 가이드에서는 Azure 구독이 있고 Azure Media Services 계정을 만들었다고 가정합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인

먼저 웹 브라우저를 열고 [Microsoft Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

이 빠른 시작에서 설명하는 내용은 다음과 같습니다.

- Telestream Wirecast 평가판을 사용하여 온-프레미스 인코더 설정
- 라이브 스트림 설정
- 라이브 스트림 출력 설정
- 기본 스트리밍 엔드포인트 실행
- Azure Media Player를 사용하여 라이브 스트림 및 주문형 출력 보기

간단히 유지하기 위해 Wirecast의 Azure Media Services, 통과 클라우드 인코딩 및 RTMP에 대한 인코딩 미리 설정을 사용합니다.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Wirecast를 사용하여 온-프레미스 인코더 설정

1. https://www.telestream.net 에서 운영 체제용 Wirecast를 다운로드하여 설치합니다.
1. 애플리케이션을 시작하고, 즐겨찾는 이메일 주소를 사용하여 제품을 등록합니다.  애플리케이션을 열어 둡니다.
1. 이메일 주소를 확인하도록 요청하는 이메일을 받게 되며, 애플리케이션에서 평가판을 시작합니다.
1. 권장 사항: 애플리케이션 열기 화면에서 비디오 자습서를 시청합니다.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Azure Media Services 라이브 스트림 설정

1. 포털 내의 Azure Media Services 계정으로 이동한 후 Media Services 목록에서 **라이브 스트리밍**을 선택합니다.
1. **라이브 이벤트 추가**를 클릭하여 새 라이브 스트리밍 이벤트를 만듭니다.
1. 라이브 이벤트 **이름** 필드에서 새 이벤트의 이름(예: *TestLiveEvent*)을 입력합니다.
1. **설명** 필드에서 이벤트에 대한 설명(선택 사항)을 입력합니다.
1. **통과 – 클라우드 인코딩 없음** 라디오 단추를 선택합니다.
1. **RTMP** 라디오 단추를 선택합니다. 
1. 라이브 이벤트를 준비하기 전에 이 이벤트에 대한 요금이 청구되지 않도록 하려면 시작 라이브 이벤트에 대해 **아니요** 라디오 단추를 선택해야 합니다.  (라이브 이벤트가 시작되면 청구가 시작됩니다.)
1. **검토 + 만들기** 단추를 클릭하여 설정을 검토합니다.
1. **만들기** 단추를 클릭하여 라이브 이벤트를 만듭니다. 그러면 라이브 이벤트 목록 보기로 돌아갑니다.
1. 방금 만든 **라이브 이벤트에 대한 링크**를 클릭합니다. 이벤트가 중지되었습니다.
1. 브라우저에서 이 페이지를 열어 둡니다.  나중에 다시 돌아옵니다.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Wirecast Studio를 사용하여 라이브 스트림 설정

1. Wirecast 애플리케이션이 아직 열려 있다고 가정하고, 주 메뉴에서 **빈 문서 만들기**를 선택한 다음, **계속**을 클릭합니다.
1. 마우스로 Wirecast 계층 영역의 첫 번째 계층 위를 가리킵니다.  표시되는 **추가** 아이콘을 클릭하고, 스트림하려는 비디오 입력을 선택합니다.  마스터 계층 1 대화 상자가 열립니다.
1. 메뉴에서 **비디오 캡처**를 선택한 다음, 사용하려는 카메라를 선택합니다. 카메라의 보기가 미리 보기 영역에 표시됩니다.
1. 마우스로 Wirecast 계층 영역의 두 번째 계층 위를 가리킵니다. 표시되는 **추가** 아이콘을 클릭하고 스트림하려는 오디오 입력을 선택합니다.  마스터 계층 2 대화 상자가 열립니다.
1. 메뉴에서 **오디오 캡처**를 선택한 다음, 사용하려는 오디오 입력을 선택합니다. 
1. 주 메뉴에서 **출력 설정**을 선택합니다.  출력 대화 상자가 표시됩니다.
1. 출력 드롭다운에서 **Azure Media Services**를 선택합니다.  Azure Media Services에 대한 출력 설정은 *대부분*의 출력 설정을 자동으로 채웁니다.
1. 다음 섹션에서는 브라우저의 Azure Media Services로 돌아가서 *입력 URL*을 복사하여 출력 설정에 입력합니다.

### <a name="copy-and-paste-the-input-url"></a>입력 URL 복사 및 붙여넣기

1. 포털의 Azure Media Services 페이지로 돌아가서 **시작**을 클릭하여 라이브 스트림 이벤트를 시작합니다. (이제 청구가 시작됩니다.)
2. **보안/보안되지 않음** 토글을 클릭하여 **보안되지 않음**으로 설정합니다.  그러면 프로토콜이 RTMPS 대신 RTMP로 설정됩니다.
3. **입력 URL**을 클립보드에 복사합니다.
4. Wirecast 애플리케이션으로 전환하고, **입력 URL**을 출력 설정의 **주소** 필드에 붙여넣습니다.
5. **확인**을 클릭합니다.

## <a name="setting-up-outputs"></a>출력 설정

여기서는 출력을 설정하고, 라이브 스트림의 기록을 저장할 수 있도록 합니다.  

> [!NOTE]
> 이 출력을 스트림하려면 스트리밍 엔드포인트가 실행되고 있어야 합니다.  아래의 '기본 스트리밍 엔드포인트 실행' 섹션을 참조하세요.

1. 출력 비디오 뷰어의 아래쪽에 있는 **출력 만들기** 링크를 클릭합니다.
1. 원하는 경우 나중에 쉽게 찾을 수 있도록 **이름** 필드의 출력 이름을 사용자에게 더 친숙한 이름으로 편집합니다.
1. 지금은 필드의 나머지 부분 모두를 그대로 둡니다.
1. **다음**을 클릭하여 스트리밍 로케이터를 추가합니다.
1. 원하는 경우 로케이터 이름을 사용자에게 더 친숙한 이름으로 변경합니다.
1. 지금은 이 화면의 다른 모든 항목을 그대로 둡니다.
1. **만들기**를 클릭합니다.

## <a name="starting-the-broadcast"></a>브로드캐스트 시작

1. Wirecast의 주 메뉴에서 **출력 > 브로드캐스트 시작/중지 > Azure Media Services 시작: Azure Media Services**를 선택합니다.  스트림이 라이브 이벤트로 보내지면 Azure Media Services의 라이브 이벤트 페이지에 있는 라이브 이벤트 비디오 플레이어에 Wirecast의 Live 창이 표시됩니다.

1. 미리 보기 창 아래의 **이동** 단추를 클릭하여 Wirecast 계층에 대해 선택한 비디오 및 오디오 브로드캐스팅을 시작합니다.

> [!TIP]
> 오류가 있으면 플레이어 위쪽에 있는 [플레이어 다시 로드] 링크를 클릭하여 플레이어를 다시 로드해 봅니다.

## <a name="running-the-default-streaming-endpoint"></a>기본 스트리밍 엔드포인트 실행

1. Media Services 목록에서 **스트리밍 엔드포인트**를 선택하여 스트리밍 엔드포인트가 실행되고 있는지 확인합니다. 스트리밍 엔드포인트 페이지로 이동합니다.
1. 기본 스트리밍 엔드포인트 상태가 중지됨인 경우 **기본** 스트리밍 엔드포인트를 클릭합니다. 그러면 해당 엔드포인트의 페이지로 이동합니다.
1. **시작**을 클릭합니다.  그러면 스트리밍 엔드포인트가 시작됩니다.

## <a name="play-the-output-broadcast-with-azure-media-player"></a>Azure Media Player를 사용하여 출력 브로드캐스트 재생

1. 출력 비디오 플레이어 아래에 **스트리밍 URL**을 복사합니다. 
1. 웹 브라우저에서 Azure Media Player 데모(https://ampdemo.azureedge.net/azuremediaplayer.html )를 엽니다.
1. **스트리밍 URL**을 Azure Media Player의 URL 필드에 붙여넣습니다.
1. **플레이어 업데이트** 단추를 클릭합니다.
1. 비디오에서 **재생** 아이콘을 클릭하여 라이브 스트림을 봅니다.

## <a name="stopping-the-broadcast"></a>브로드캐스트 중지

콘텐츠를 충분히 스트림했다고 생각되면 브로드캐스트를 중지합니다.

1. Wirecast에서 **브로드캐스트** 단추를 클릭합니다.  그러면 Wirecast에서 브로드캐스트가 중지됩니다.
1. 포털에서 **중지**를 클릭합니다. 라이브 스트림이 중지되지만 출력이 이제 주문형 자산이 된다는 경고 메시지가 표시됩니다.
1. 경고 메시지에서 **중지**를 클릭합니다. 이제 라이브 스트림을 더 이상 사용할 수 없으므로 Azure Media Player에 오류가 표시됩니다.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Azure Media Player를 사용하여 주문형 출력 재생

이제 스트리밍 엔드포인트가 실행되는 동안에는 사용자가 만든 출력을 주문형 스트리밍에 사용할 수 있습니다.

1. Media Services 목록으로 이동하여 **자산**을 선택합니다.
1. 이전에 만든 이벤트 출력을 찾아서 **자산에 대한 링크**를 클릭합니다. 자산 출력 페이지가 열립니다.
1. 자산에 대한 비디오 플레이어 아래에 **스트리밍 URL**을 복사합니다.
1. 브라우저에서 Azure Media Player로 돌아가서 **스트리밍 URL**을 Azure Media Player의 URL 필드에 붙여넣습니다.
1. **플레이어 업데이트**를 클릭합니다.
1. 비디오에서 **재생** 아이콘을 클릭하여 주문형 자산을 봅니다.

## <a name="clean-up-resources"></a>리소스 정리

  > [!IMPORTANT]
  > 서비스를 중지하세요! 이 빠른 시작 단계가 완료되면 라이브 이벤트 및 스트리밍 엔드포인트를 중지해야 합니다. 그렇지 않으면 실행되고 있는 동안 요금이 계속 청구됩니다. 라이브 이벤트를 중지하려면 위의 '브로드캐스트 중지' 2단계 및 3단계를 참조하세요.

### <a name="stopping-the-streaming-endpoint"></a>스트리밍 엔드포인트 중지

1. Media Services 목록에서 **스트리밍 엔드포인트**를 선택합니다.
2. 이전에 시작한 **기본** 스트리밍 엔드포인트를 클릭합니다. 그러면 엔드포인트의 페이지가 열립니다.
3. **중지**를 클릭합니다.  그러면 스트리밍 엔드포인트가 중지됩니다.

>[!TIP]
>이 이벤트의 자산을 유지하지 않으려면 스토리지에 대한 요금이 청구되지 않도록 해당 자산을 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [순서상의 다음 문서](./live-events-outputs-concept.md)
