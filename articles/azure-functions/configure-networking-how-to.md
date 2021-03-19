---
title: 가상 네트워크를 사용 하 여 Azure Functions를 구성 하는 방법
description: Azure Functions에 대 한 특정 가상 네트워킹 작업을 수행 하는 방법을 보여 주는 문서입니다.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: a28a59a0de40bba7914d1920b42034fbbc223ddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609538"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>가상 네트워크를 사용 하 여 Azure Functions를 구성 하는 방법

이 문서에서는 가상 네트워크에 연결 하 고 실행 하도록 함수 앱을 구성 하는 작업과 관련 된 작업을 수행 하는 방법을 보여 줍니다. Azure Functions 및 네트워킹에 대 한 자세한 내용은 [네트워킹 옵션 Azure Functions](functions-networking-options.md)를 참조 하세요.

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>가상 네트워크에 대한 스토리지 계정 제한 

함수 앱을 만들 때 Blob, 큐 및 Table Storage을 지원하는 범용 Azure Storage 계정을 만들거나 연결해야 합니다. 이 저장소 계정은 서비스 끝점이 나 개인 끝점으로 보안이 유지 되는 계정으로 바꿀 수 있습니다. 

> [!NOTE]  
> 이 기능은 현재 전용 (App Service) 계획의 모든 Windows 가상 네트워크 지원 Sku와 프리미엄 계획에 대해 작동 합니다. 소비 계획은 지원 되지 않습니다. 

개인 네트워크로 제한 된 저장소 계정을 사용 하 여 함수를 설정 하려면 다음을 수행 합니다.

1. 서비스 끝점이 사용 하도록 설정 되지 않은 저장소 계정을 사용 하 여 함수를 만듭니다.

1. 가상 네트워크에 연결 하도록 함수를 구성 합니다.

1. 다른 저장소 계정을 만들거나 구성 합니다.  서비스 끝점을 사용 하 여 보안을 설정 하 고 함수를 연결 하는 저장소 계정입니다.

1. 보안 저장소 계정에서 [파일 공유를 만듭니다](../storage/files/storage-how-to-create-file-share.md#create-file-share) .

1. 저장소 계정에 대 한 서비스 끝점 또는 개인 끝점을 사용 하도록 설정 합니다.  
    * 개인 끝점 연결을 사용 하는 경우 저장소 계정에는 및 하위 리소스에 대 한 개인 끝점이 필요 `file` `blob` 합니다.  Durable Functions와 같은 특정 기능을 사용 하는 경우 `queue` `table` 개인 끝점 연결을 통해 액세스 하 고 액세스할 수도 있습니다.
    * 서비스 끝점을 사용 하는 경우 저장소 계정에 대 한 함수 앱 전용 서브넷을 사용 하도록 설정 합니다.

1. 함수 앱 저장소 계정에서 보안 저장소 계정 및 파일 공유로 파일 및 blob 콘텐츠를 복사 합니다.

1. 이 저장소 계정에 대 한 연결 문자열을 복사 합니다.

1. 함수 앱에 대 한 **구성** 아래의 **응용 프로그램 설정을** 다음으로 업데이트 합니다.

    | 설정 이름 | 값 | 의견 |
    |----|----|----|
    | `AzureWebJobsStorage`| 저장소 연결 문자열 | 보안 저장소 계정에 대 한 연결 문자열입니다. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  저장소 연결 문자열 | 보안 저장소 계정에 대 한 연결 문자열입니다. |
    | `WEBSITE_CONTENTSHARE` | 파일 공유 | 프로젝트 배포 파일이 상주 하는 보안 저장소 계정에 생성 된 파일 공유의 이름입니다. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | 새 설정 |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | 가상 네트워크를 통해 모든 아웃 바운드 트래픽을 강제로 적용 합니다. 저장소 계정에서 개인 끝점 연결을 사용 하는 경우에 필요 합니다. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | 앱에서 사용 하는 DNS 서버입니다. 저장소 계정에서 개인 끝점 연결을 사용 하는 경우에 필요 합니다. |

1. **저장** 을 선택 하 여 응용 프로그램 설정을 저장 합니다. 앱 설정을 변경 하면 앱이 다시 시작 됩니다.  

함수 앱이 다시 시작 되 면 이제 보안 저장소 계정에 연결 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 네트워킹 옵션](functions-networking-options.md)

