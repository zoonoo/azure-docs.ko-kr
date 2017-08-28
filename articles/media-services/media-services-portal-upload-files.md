---
title: " Azure 포털을 사용하여 미디어 서비스 계정에 파일 업로드 | Microsoft Docs"
description: "이 자습서에서는 Azure 포털을 사용하여 미디어 서비스 계정에 파일을 업로드하는 단계를 안내합니다."
services: media-services
documentationcenter: 
author: Juliako
manager: SyntaxC4
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 8b9a7a266259077d49a38d51d7f801f420cc6315
ms.contentlocale: ko-kr
ms.lasthandoff: 08/08/2017

---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>Azure 포털을 사용하여 미디어 서비스 계정에 파일 업로드
> [!div class="op_single_selector"]
> * [포털](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST (영문)](media-services-rest-upload-files.md)
> 
> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
> 


미디어 서비스에서 자산에 디지털 파일을 업로드합니다. 자산에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다. 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.


## <a name="upload-files"></a>파일 업로드

>[!NOTE]
>Media Services에서 처리를 위해 지원되는 최대 파일 크기에 제한이 있습니다. 파일 크기 제한에 대한 세부 정보는 [이](media-services-quotas-and-limitations.md) 항목을 참조하세요.
>

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. **설정** 블레이드에서 **자산**을 클릭합니다.
   
    ![파일 업로드](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. **업로드** 단추를 클릭합니다.
   
    **비디오 자산 업로드** 창이 나타납니다.
   
   > [!NOTE]
   > 파일 크기에는 제한이 없습니다.
   > 
   > 
4. 컴퓨터에 원하는 비디오를 찾아 선택하고 확인을 누릅니다.  
   
    업로드가 시작되고 파일 이름 아래에 진행 상태가 표시됩니다.  

업로드가 완료되면 **자산** 창에 새 자산이 나열됩니다. 

## <a name="next-steps"></a>다음 단계
이제 업로드된 자산을 인코딩할 수 있습니다. 자세한 내용은 [자산 인코딩](media-services-portal-encode.md)을 참조하세요.

또한 Azure Functions를 사용하여 구성된 컨테이너에 도착하는 파일에 따라 인코딩 작업을 트리거할 수도 있습니다. 자세한 내용은 [이 샘플](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )을 참조하세요.

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


