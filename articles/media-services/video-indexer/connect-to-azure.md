---
title: Azure Portal에서 Video Indexer 계정 만들기
titlesuffix: Azure Media Services
description: 이 문서에서는 Azure Portal에서 Video Indexer 계정을 만드는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 49e05047d58cc5b6bb5e0099c24a131a26dc8af1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292011"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Azure에 연결되는 Video Indexer 계정 만들기

Video Indexer 계정을 만들 때 평가판 계정(특정의 체험 인덱싱 시간(분)을 가져오는 경우) 또는 유료 옵션(할당량으로 제한되지 않은 경우)을 선택할 수 있습니다. 평가판을 사용하면 Video Indexer에서 웹 사이트 사용자에게 최대 600분의 체험 인덱싱을 제공하고, API 사용자에게는 최대 2,400분의 체험 인덱싱을 제공합니다. 유료 옵션을 사용하면 Azure 구독 및 Azure Media Services 계정에 연결되는 Video Indexer 계정을 만듭니다. 인덱싱 시간(분) 및 미디어 계정과 관련된 요금을 지불합니다. 

이 문서에서는 Azure 구독 및 Azure Media Services 계정에 연결되는 Video Indexer 계정을 만드는 방법에 대해 설명합니다. 

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 

    아직 Azure 구독이 없는 경우 [Azure 평가판](https://azure.microsoft.com/free/)에 가입합니다.

* Azure AD(Active Directory) 도메인. 

    Azure AD 도메인이 없는 경우 Azure 구독을 사용하여 이 도메인을 만듭니다. 자세한 내용은 [Azure Active Directory에서 사용자 지정 도메인 이름 관리](../../active-directory/users-groups-roles/domains-manage.md)를 참조하세요.

* Azure AD 도메인의 사용자 및 멤버. 이 멤버는 Video Indexer 계정을 Azure에 연결할 때 사용합니다.

    이 사용자는 다음 조건을 충족해야 합니다.

    * outlook.com, live.com 또는 hotmail.com.과 같은 개인 계정이 아닌 회사 또는 학교 계정이 있는 Azure AD 사용자여야 합니다.
        
        ![모든 AAD 사용자](./media/create-account/all-aad-users.png)

    *  소유자 역할이 있거나 기여자와 사용자 액세스 관리자 역할이 모두 있는 Azure 구독의 멤버여야 합니다. 사용자는 두 가지 역할을 사용하여 두 번 추가될 수 있습니다. 기여자 역할과 사용자 액세스 관리자 역할이 각각 있는 멤버로 두 번 추가합니다.

        ![액세스 제어](./media/create-account/access-control-iam.png)

* Azure Portal을 사용하여 EventGrid 리소스 공급자를 등록합니다.

    [Azure Portal](https://portal.azure.com/)에서 **구독** > [구독] > **ResourceProviders** > **Microsoft.EventGrid**로 차례로 이동합니다. "등록됨" 상태가 아닌 경우 **등록**을 클릭합니다. 등록하는 데 몇 분 정도 걸립니다. 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Azure에 연결

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.

2. **Azure에 연결** 단추를 클릭합니다.

    ![Azure에 연결](./media/create-account/connect-to-azure.png)

3. 구독 목록이 표시되면 사용하려는 구독을 선택합니다. 

    ![Video Indexer를 Azure에 연결](./media/create-account/connect-vi-to-azure-subscription.png)

4. 지원되는 위치에서 Azure 지역(미국 서부 2, 북유럽 또는 동아시아)을 선택합니다.
5. **Azure Media Services 계정** 아래에서 다음 옵션 중 하나를 선택합니다.

    * 새 Media Services 계정을 만들려면 **새 리소스 그룹 만들기**를 선택합니다. 리소스 그룹에 대한 이름을 제공합니다.

        Azure에서 새 Azure Storage 계정을 포함한 새 계정을 구독에 만듭니다. 새 Media Services 계정에는 스트리밍 엔드포인트와 10개의 S3 예약 단위가 포함된 기본 초기 구성이 있습니다.
    * 기존 Media Services 계정을 사용하려면 **기존 리소스 사용**을 선택합니다. 계정 목록에서 계정을 선택합니다.

        Media Services 계정에는 Video Indexer 계정과 동일한 지역이 있어야 합니다. 인덱싱 기간과 낮은 처리량을 최소화하려면 Media Services 계정에서 예약 단위의 유형과 수를 **10개의 S3 예약 단위**로 조정합니다.
    * 연결을 수동으로 구성하려면 **수동 구성으로 전환**을 클릭합니다. 
    
        어떤 이유로 자동 옵션이 완료되지 않는 경우 또는 설정과 구성이 일반적인 경우와 다르거나 설정에 대한 완전한 가시성과 제어가 필요한 경우 연결을 수동으로 구성할 수 있습니다. 
        
        **Video Indexer를 Azure 구독에 연결**에서 다음 정보를 제공합니다.

        |설정|설명|
        |---|---|
        |Video Indexer 계정 지역|Video Indexer 계정 지역의 이름입니다. 성능을 향상시키고 비용을 절감하려면 Azure Media Services 리소스와 Azure Storage 계정이 있는 지역의 이름을 지정하는 것이 좋습니다. |
        |AAD(Azure Active Directory) 테넌트|Azure AD 테넌트의 이름입니다(예: "contoso.onmicrosoft.com"). Azure Portal에서 테넌트 정보를 검색할 수 있습니다. 오른쪽 위 모서리에서 로그인한 사용자의 이름 위로 커서를 둡니다.|
        |구독 ID|이 연결을 만들어야 하는 Azure 구독입니다. 구독 ID는 Azure Portal에서 검색할 수 있습니다. 왼쪽 패널에서 **모든 서비스**를 클릭하고 "구독"을 검색합니다. **구독**을 선택하고 구독 목록에서 원하는 ID를 선택합니다.|
        |Azure Media Services 리소스 그룹 이름|Media Services 계정에 있는 리소스 그룹에 대한 이름입니다.|
        |Media Services 리소스 이름|Azure Media Services 리소스에 대한 이름입니다.|
        |응용 프로그램 UI|지정된 Media Services 계정에 대한 권한이 있는 Azure AD 애플리케이션 ID입니다. 자세한 내용은 [서비스 주체 인증 사용](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)을 참조하세요.|
        |응용 프로그램 키|자세한 내용은 [서비스 주체 인증 사용](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)을 참조하세요.|

6. 작업이 완료되면 **연결**을 선택합니다. 이 작업은 최대 몇 분 정도 걸릴 수 있습니다. 

    Azure에 연결되면 새 Video Indexer 계정이 계정 목록에 표시됩니다.

    ![새 계정](./media/create-account/new-account.png)

7. 새 계정으로 이동합니다. 

    ![Video Indexer 계정](./media/create-account/vi-account.png)

## <a name="considerations"></a>고려 사항

다음과 같은 Azure Media Services 관련 고려 사항이 적용됩니다.

* 새 Media Services 계정에 연결한 경우 Azure 구독에 새 리소스 그룹, Media Services 계정 및 Storage 계정이 표시됩니다.
* 새 Media Services 계정에 연결한 경우 Video Indexer에서 미디어 **예약 단위**를 10개의 S3 단위로 설정합니다.

    ![Media Services 예약 단위](./media/create-account/ams-reserved-units.png)

* 기존 Media Services 계정에 연결한 경우 Video Indexer에서 기존 미디어 **예약 단위** 구성을 변경하지 않습니다.

    계획된 로드에 따라 미디어 **예약 단위**의 유형과 수를 조정해야 할 수도 있습니다. 로드가 높고 단위 또는 속도가 충분하지 않으면 비디오 처리로 인해 시간 제한 오류가 발생할 수 있습니다.

* 새 Media Services 계정에 연결한 경우 Video Indexer에서 자동으로 기본 **스트리밍 엔드포인트**를 시작합니다.

    ![Media Services 스트리밍 엔드포인트](./media/create-account/ams-streaming-endpoint.png)

* 기존 Media Services 계정에 연결한 경우 Video Indexer에서 기본 스트리밍 엔드포인트 구성을 변경하지 않습니다. **스트리밍 엔드포인트**가 실행되지 않으면 이 Media Services 계정 또는 Video Indexer에서 비디오를 시청할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[API 사용](video-indexer-use-apis.md)의 지침에 따라 평가판 계정 및/또는 Azure에 연결된 Video Indexer 계정과 프로그래밍 방식으로 상호 작용할 수 있습니다.

Azure에 연결할 때 사용한 것과 동일한 Azure AD 사용자를 사용해야 합니다.


