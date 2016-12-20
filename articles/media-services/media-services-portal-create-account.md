---
title: " Azure 포털을 사용하여 Azure 미디어 서비스 계정 만들기 | Microsoft Docs"
description: "이 자습서에서는 Azure 포털을 사용하여 Azure 미디어 서비스 계정을 만드는 단계를 안내합니다."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 312231f395f19fde98431c24b71ce7cec3061eaf
ms.openlocfilehash: 4a1d5d706cbfb5c42a36fd03033aa361ef3d4d11


---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Media Services 계정 만들기
> [!div class="op_single_selector"]
> * [포털](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
> 
> 

Azure 포털을 통해 AMS(Azure 미디어 서비스) 계정을 신속하게 만들 수 있습니다. 계정을 사용하여 Azure에서 미디어 콘텐츠를 저장, 암호화, 인코딩, 관리 및 스트리밍할 수 있는 미디어 서비스에 액세스할 수 있습니다. 미디어 서비스 계정을 만들 때 미디어 서비스 계정과 동일한 지역에 관련 저장소 계정도 만들거나 기존 저장소 계정을 사용합니다.

이 문서에서는 몇 가지 일반적인 개념을 설명하고 Azure 포털을 사용하여 미디어 서비스 계정을 만드는 방법을 보여 줍니다.

## <a name="concepts"></a>개념
미디어 서비스에 액세스하려면 다음 두 개의 관련 계정이 필요합니다.

* 미디어 서비스 계정. 계정을 통해 Azure에서 사용할 수 있는 클라우드 기반 미디어 서비스 집합에 액세스할 수 있습니다. 미디어 서비스 계정은 실제 미디어 콘텐츠를 저장하지 않습니다. 대신, 미디어 콘텐츠 및 미디어 처리 작업에 대한 메타데이터를 계정에 저장합니다. 계정을 만들 때 사용 가능한 미디어 서비스 영역을 선택합니다. 선택한 영역은 계정에 대한 메타데이터 레코드를 저장하는 데이터 센터입니다.
  
    사용 가능한 미디어 서비스(AMS) 지역에는 다음이 포함됩니다. 북유럽, 서유럽, 미국 서부, 미국 동부, 동남 아시아, 동아시아, 일본 서부, 일본 동부 미디어 서비스는 선호도 그룹을 사용하지 않습니다.
  
    AMS는 이제 브라질 남부, 인도 서부, 인도 남부 및 인도 중부의 데이터 센터에서도 사용 가능합니다. 이제 Azure Portal을 사용하여 Media Service 계정을 만들고 여기에서 설명한 다양한 작업을 수행할 수 있습니다. 그러나 라이브 인코딩은 이러한 데이터 센터에서 활성화되지 않습니다. 또한 모든 유형의 인코딩 예약 단위를 이러한 데이터 센터에서 사용할 수 없습니다.
  
  * 브라질 남부: 표준 및 기본 인코딩 예약 단위만 사용 가능합니다.
  * 인도 서부, 인도 남부: 
* Azure 저장소 계정. 저장소 계정은 Media Services 계정과 동일한 지역에 있어야 합니다. 미디어 서비스 계정을 만들 때 동일한 지역의 기존 저장소 계정을 선택하거나 동일한 지역에 새 저장소 계정을 만들 수 있습니다. 미디어 서비스 계정을 삭제하는 경우 관련 저장소 계정의 Blob은 삭제되지 않습니다.

## <a name="create-an-ams-account"></a>AMS 계정 만들기
이 섹션의 단계에서는 AMS 계정을 만드는 방법을 보여 줍니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. **+새로 만들기** > **웹 + 모바일** > **Media Services**를 클릭합니다.
   
    ![미디어 서비스 만들기](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. **미디어 서비스 계정 만들기** 에 필요한 값을 입력합니다.
   
    ![미디어 서비스 만들기](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. **계정 이름**에 새 AMS 계정의 이름을 입력합니다. 미디어 서비스 계정 이름은 공백 없이 모두 소문자로 이루어진 3-24자의 숫자 또는 문자입니다.
   2. 구독에서 액세스할 수 있는 다양한 Azure 구독 중에서 선택합니다.
   3. **리소스 그룹**에서 새 또는 기존 리소스를 선택합니다.  리소스 그룹은 수명 주기, 권한 및 정책을 공유하는 리소스의 컬렉션입니다. [여기](../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.
   4. **위치**에서 Media Services 계정에 대한 메타데이터 레코드 및 미디어를 저장하는 데 사용할 지리적 지역을 선택합니다. 이 지역은 미디어를 처리하고 스트림하는 데 사용됩니다. 사용 가능한 미디어 서비스 지역만 드롭다운 목록 상자에 표시됩니다. 
   5. **저장소 계정**에서 미디어 서비스 계정의 미디어 콘텐츠가 포함된 Blob 저장소를 제공할 저장소 계정을 선택합니다. 미디어 서비스 계정과 동일한 지역의 기존 저장소 계정을 선택하거나 저장소 계정을 만들 수 있습니다. 동일한 지역에 새 저장소 계정이 생성됩니다. 저장소 계정 이름에 대한 규칙은 미디어 서비스 계정의 경우와 같습니다.
      
       저장소에 대한 자세한 내용은 [여기](../storage/storage-introduction.md)를 참조하세요.
   6. 계정 배포 진행 상태를 보려면 **대시보드에 고정** 을 선택합니다.
4. 양식 맨 아래에 있는 **만들기** 를 클릭합니다.
   
    계정이 만들어지면 상태가 **실행 중**으로 변경됩니다. 
   
    ![미디어 서비스 설정](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    AMS 계정을 관리(예: 비디오 업로드, 자산 인코딩, 작업 진행 상태 모니터링)하려면 **설정** 창을 사용합니다.

## <a name="manage-keys"></a>키 관리
프로그래밍 방식으로 미디어 서비스 계정에 액세스하려면 계정 이름과 기본 키 정보가 필요합니다.

1. Azure 포털에서 계정을 선택합니다. 
   
    **설정** 창이 오른쪽에 나타납니다. 
2. **설정** 창에서 **키**를 선택합니다. 
   
    **키 관리** 창에 계정 이름과 기본 및 보조 키가 표시됩니다. 
3. 복사 단추를 클릭하여 값을 복사합니다.
   
    ![미디어 서비스 키](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>다음 단계
이제 AMS 계정에 파일을 업로드할 수 있습니다. 자세한 내용은 [파일 업로드](media-services-portal-upload-files.md)를 참조하세요.

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO5-->


