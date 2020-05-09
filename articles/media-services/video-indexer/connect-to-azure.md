---
title: Azure에 연결되는 Video Indexer 계정 만들기
titleSuffix: Azure Media Services
description: Azure에 연결 된 Video Indexer 계정을 만드는 방법에 대해 알아봅니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/08/2020
ms.author: juliako
ms.openlocfilehash: 2093132abaa7aed06113b1856c85ac54fd7ae60d
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995705"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Azure에 연결되는 Video Indexer 계정 만들기

Video Indexer 계정을 만들 때 무료 평가판 계정 (특정 개수의 무료 인덱싱 시간 (분)을 얻을 수 있음) 또는 유료 옵션 (할당량으로 제한 되지 않음)을 선택할 수 있습니다. 무료 평가판을 사용 Video Indexer 하면 웹 사이트 사용자에 게 최대 600 분의 무료 인덱싱이 제공 되며 API 사용자에 대 한 무료 인덱싱이 최대 2400 분까지 제공 됩니다. 유료 옵션을 사용 하면 Azure 구독 및 Azure Media Services 계정에 연결 된 Video Indexer 계정을 만들 수 있습니다. 미디어 계정 관련 요금 뿐만 아니라 인덱싱된 시간 (분)에 대 한 비용을 지불 합니다.

이 문서에서는 Azure 구독 및 Azure Media Services 계정에 연결되는 Video Indexer 계정을 만드는 방법에 대해 설명합니다. 이 토픽에서는 자동(기본값) 흐름을 사용하여 Azure에 연결하는 단계를 설명합니다. Azure에 수동으로 연결하는 방법도 보여줍니다(고급).

*평가판* 에서 *유료* Video Indexer 계정으로 전환 하는 경우 [평가판 계정에서 콘텐츠 가져오기](#import-your-content-from-the-trial-account)에 설명 된 대로 모든 비디오 및 모델 사용자 지정을 새 계정에 복사 하도록 선택할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독

    아직 Azure 구독이 없는 경우 [Azure 평가판](https://azure.microsoft.com/free/)에 가입합니다.

* Azure Active Directory (Azure AD) 도메인입니다.

    Azure AD 도메인이 없는 경우 Azure 구독을 사용하여 이 도메인을 만듭니다. 자세한 내용은 [AZURE AD에서 사용자 지정 도메인 이름 관리](../../active-directory/users-groups-roles/domains-manage.md) 를 참조 하세요.

* **응용 프로그램 관리자** 역할을 사용 하는 Azure AD 도메인의 사용자입니다. 이 멤버는 Video Indexer 계정을 Azure에 연결할 때 사용합니다.

    이 사용자는 회사 또는 학교 계정이 있는 Azure AD 사용자 여야 합니다. Outlook.com, live.com 또는 hotmail.com와 같은 개인 계정을 사용 하지 마세요.

    ![모든 AAD 사용자](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>자동 흐름의 추가 필수 구성 요소

* Azure AD 도메인의 사용자 및 멤버.

    이 멤버는 Video Indexer 계정을 Azure에 연결할 때 사용합니다.

    이 사용자는 **소유자** 역할이 있거나 **기여자**와 **사용자 액세스 관리자** 역할이 모두 있는 Azure 구독의 멤버여야 합니다. 두 개의 역할을 사용 하 여 사용자를 두 번 추가할 수 있습니다. 기여자 역할과 사용자 액세스 관리자 역할이 각각 있는 멤버로 두 번 추가합니다.

    ![액세스 제어](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>수동 흐름의 추가 필수 구성 요소

* Azure Portal을 사용하여 EventGrid 리소스 공급자를 등록합니다.

    [Azure Portal](https://portal.azure.com/)에서 **구독**->[구독]->**ResourceProviders**로 이동합니다.

    **Microsoft.Media** 및 **Microsoft.EventGrid**를 검색합니다. "등록됨" 상태가 아닌 경우 **등록**을 클릭합니다. 등록하는 데 몇 분 정도 걸립니다.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Azure에 연결

> [!NOTE]
> Azure 구독에서 인증서 기반 multi-factor authentication을 사용 하는 경우 필수 인증서가 설치 된 장치에서 다음 단계를 수행 하는 것이 중요 합니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.

2. **새 계정 만들기** 단추를 선택 합니다.

    ![새 Video Indexer 계정 만들기](./media/create-account/connect-to-azure.png)

3. 구독 목록이 표시되면 사용하려는 구독을 선택합니다.

    ![Azure에 Video Indexer 연결](./media/create-account/connect-vi-to-azure-subscription.png)

4. 지원되는 위치에서 Azure 지역(미국 서부 2, 북유럽 또는 동아시아)을 선택합니다.
5. **Azure Media Services 계정** 아래에서 다음 옵션 중 하나를 선택합니다.

    * 새 Media Services 계정을 만들려면 **새 리소스 그룹 만들기**를 선택합니다. 리소스 그룹에 대한 이름을 제공합니다.

        Azure에서 새 Azure Storage 계정을 포함한 새 계정을 구독에 만듭니다. 새 Media Services 계정에는 스트리밍 끝점과 10 개의 S3 예약 단위를 사용 하는 기본 초기 구성이 있습니다.
    * 기존 Media Services 계정을 사용하려면 **기존 리소스 사용**을 선택합니다. 계정 목록에서 계정을 선택합니다.

        Media Services 계정에는 Video Indexer 계정과 동일한 지역이 있어야 합니다.

        > [!NOTE]
        > 인덱싱 기간과 낮은 처리량을 최소화 하려면 Media Services 계정의 [예약 단위](../previous/media-services-scale-media-processing-overview.md ) 유형과 수를 **10 개의 S3 예약 단위**로 조정 하는 것이 좋습니다. [포털을 사용 하 여 예약 단위 변경을](../previous/media-services-portal-scale-media-processing.md)참조 하세요.

    * 수동으로 연결을 구성 하려면 **수동 구성으로 전환** 링크를 선택 합니다.

        자세한 내용은 이어지는 [Azure에 수동으로 연결](#connect-to-azure-manually-advanced-option)(고급 옵션) 섹션을 참조하세요.
6. 작업이 완료되면 **연결**을 선택합니다. 이 작업은 최대 몇 분 정도 걸릴 수 있습니다.

    Azure에 연결되면 새 Video Indexer 계정이 계정 목록에 표시됩니다.

    ![새 계정](./media/create-account/new-account.png)

7. 새 계정으로 이동 합니다.

## <a name="connect-to-azure-manually-advanced-option"></a>Azure에 수동으로 연결(고급 옵션)

Azure 연결이 실패한 경우 수동으로 연결하여 문제를 해결할 수 있습니다.

> [!NOTE]
> 동일한 지역에는 동일한 Media Services 계정에 연결 된 Azure storage 계정 뿐만 아니라 Media Services 계정에 연결 하는 Video Indexer 계정 이라는 세 가지 계정을 사용 하는 것이 좋습니다.

### <a name="create-and-configure-a-media-services-account"></a>Media Services 계정을 만들고 구성

1. [Azure](https://portal.azure.com/) Portal을 사용하여 [계정 만들기](../previous/media-services-portal-create-account.md)에 설명된 대로 Azure Media Services 계정을 만듭니다.

    Media Services 계정에 대 한 저장소 계정을 만들 때 복제 필드의 계정 종류 및 **지역 중복 (GRS)** 에 대해 **StorageV2** 를 선택 합니다.

    ![새 AMS 계정](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Media Services 리소스 및 계정 이름을 기록해 둡니다. 다음 섹션의 단계에 필요 합니다.

2. 만든 Media Services 계정에서 [예약 단위](../previous/media-services-scale-media-processing-overview.md ) 유형 및 수를 **10 S3 예약 단위** 로 조정 합니다. [포털을 사용 하 여 예약 단위 변경을](../previous/media-services-portal-scale-media-processing.md)참조 하세요.
3. Video Indexer 웹 앱에서 비디오를 재생 하려면 먼저 새 Media Services 계정의 기본 **스트리밍 끝점** 을 시작 해야 합니다.

    새 Media Services 계정에서 **스트리밍 끝점**을 선택 합니다. 그런 다음 스트리밍 끝점을 선택 하 고 시작을 누릅니다.

    ![새 AMS 계정](./media/create-account/create-ams-account2.png)

4. Media Services API를 사용 하 여 인증 Video Indexer AD 앱을 만들어야 합니다. 다음 단계는 [Azure Portal을 사용하여 Azure AD 인증 시작](../previous/media-services-portal-get-started-with-aad.md)에 설명된 Azure AD 인증 프로세스를 안내합니다.

    1. 새 Media Services 계정에서 **API 액세스**를 선택합니다.
    2. [서비스 주체 인증 방법](../previous/media-services-portal-get-started-with-aad.md)을 선택합니다.
    3. 클라이언트 ID 및 클라이언트 암호 가져오기

        **설정**->**키**를 선택 하 고, **설명을**추가 하 고, **저장**을 누르고, 키 값이 채워집니다.

        키가 만료 되 면 계정 소유자는 Video Indexer 지원 담당자에 게 문의 하 여 키를 갱신 해야 합니다.

        > [!NOTE]
        > 키 값 및 애플리케이션 ID를 적어 둡니다. 다음 섹션의 단계에 필요 합니다.

### <a name="connect-manually"></a>수동으로 연결

[Video Indexer](https://www.videoindexer.ai/) 페이지의 **Video Indexer를 Azure 구독에 연결** 대화 상자에서 **수동 구성으로 전환** 링크를 선택합니다.

대화 상자에서 다음 정보를 입력합니다.

|설정|Description|
|---|---|
|Video Indexer 계정 지역|Video Indexer 계정 지역의 이름입니다. 성능 및 비용 절감을 위해 Azure Media Services 리소스 및 Azure Storage 계정이 있는 지역의 이름을 지정 하는 것이 좋습니다. |
|Azure AD 테넌트|Azure AD 테넌트의 이름입니다(예: "contoso.onmicrosoft.com"). Azure Portal에서 테넌트 정보를 검색할 수 있습니다. 오른쪽 위 모서리에서 로그인한 사용자의 이름 위로 커서를 이동합니다. **도메인** 오른쪽에서 이름을 찾습니다.|
|구독 ID|이 연결을 만들어야 하는 Azure 구독입니다. 구독 ID는 Azure Portal에서 검색할 수 있습니다. 왼쪽 패널에서 **모든 서비스** 를 선택 하 고 "구독"을 검색 합니다. **구독**을 선택하고 구독 목록에서 원하는 ID를 선택합니다.|
|Azure Media Services 리소스 그룹 이름|Media Services 계정을 만든 리소스 그룹의 이름입니다.|
|Media Services 리소스 이름|이전 섹션에서 만든 Azure Media Services 계정의 이름입니다.|
|애플리케이션 UI|이전 섹션에서 만든 Azure AD 애플리케이션 ID(지정된 Media Services 계정에 대한 권한이 있는)입니다.|
|애플리케이션 키|이전 섹션에서 만든 Azure AD 애플리케이션 키입니다. |

## <a name="import-your-content-from-the-trial-account"></a>*평가판* 계정에서 콘텐츠 가져오기

[새 계정을 만들](#connect-to-azure)때 *평가판* 계정에서 새 계정으로 콘텐츠를 가져오는 옵션이 있습니다. **Azure 구독에서 새 계정 만들기** 대화 상자에서 *가져오기* 옵션을 선택 하면 모든 미디어 및 콘텐츠 모델 사용자 지정이 *평가판* 계정에서 새 계정으로 복사 됩니다.

콘텐츠를 가져오는 기능은 위에 설명 된 자동 및 수동 방법 모두에 유효 합니다.

> [!NOTE]
> 콘텐츠는 각 계정에서 한 번만 가져올 수 있습니다.

## <a name="considerations"></a>고려 사항

다음과 같은 Azure Media Services 관련 고려 사항이 적용됩니다.

* 자동으로 연결하면 Azure 구독에 새 리소스 그룹, Media Services 계정 및 스토리지 계정이 표시됩니다.
* 자동으로 연결하면 Video Indexer가 미디어 **예약 단위**를 S3 단위 10개로 설정합니다.

    ![Media Services 예약 단위](./media/create-account/ams-reserved-units.png)

* 기존 Media Services 계정에 연결 하는 경우 Video Indexer 기존 미디어 **예약 단위** 구성을 변경 하지 않습니다.

   계획 된 부하에 따라 미디어 예약 단위 유형 및 수를 조정 해야 할 수 있습니다. 로드가 높고 단위 또는 속도가 충분하지 않으면 비디오 처리로 인해 시간 제한 오류가 발생할 수 있습니다.

* 새 Media Services 계정에 연결하면 Video Indexer가 그 안에서 자동으로 기본 **스트리밍 엔드포인트**를 시작합니다.

    ![Media Services 스트리밍 엔드포인트](./media/create-account/ams-streaming-endpoint.png)

    스트리밍 끝점은 시작 시간이 상당히 많이 있습니다. 따라서 Video Indexer 웹 앱에서 비디오를 스트리밍 및 조사할 수 있을 때까지 계정을 Azure에 연결한 시간부터 몇 분 정도 걸릴 수 있습니다.

* 기존 Media Services 계정에 연결 하는 경우 Video Indexer 기본 스트리밍 끝점 구성을 변경 하지 않습니다. 실행 중인 **스트리밍 끝점이**없는 경우에는이 Media Services 계정이 나 Video Indexer에서 비디오를 볼 수 없습니다.

## <a name="next-steps"></a>다음 단계

[Api 사용](video-indexer-use-apis.md)의 지침에 따라 Azure에 연결 된 Video Indexer 계정과 평가판 계정 및/또는를 프로그래밍 방식으로 조작할 수 있습니다.

Azure에 연결할 때 사용한 것과 동일한 Azure AD 사용자를 사용해야 합니다.
