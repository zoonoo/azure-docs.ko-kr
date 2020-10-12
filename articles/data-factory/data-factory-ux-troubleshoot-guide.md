---
title: UX Azure Data Factory 문제 해결
description: Azure Data Factory UX 문제를 해결 하는 방법을 알아봅니다.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9f23155df6d9e63448b35974c331bf78c3e5f90c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426228"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Azure Data Factory UX 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory UX에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="adf-ux-not-loading"></a>ADF UX 로드 안 함

> [!NOTE]
> Azure Data Factory UX는 Microsoft Edge 및 Google Chrome을 공식적으로 지원 합니다. 다른 웹 브라우저를 사용 하면 예기치 않은 동작이 나 문서화 되지 않은 동작이 발생할 수 있습니다.

### <a name="third-party-cookies-blocked"></a>차단 된 타사 쿠키

ADF UX는 브라우저 쿠키를 사용 하 여 사용자 세션을 유지 하 고 대화형 개발 및 모니터링 환경을 가능 하 게 합니다. Incognito 세션을 사용 하 고 있거나 ad 차단을 사용 하도록 설정 했기 때문에 브라우저에서 타사 쿠키를 차단할 수 있습니다. 타사 쿠키를 차단 하면 빈 페이지로 리디렉션되는 등의 방법으로 포털을 로드 https://adf.azure.com/accesstoken.html 하거나 타사 쿠키가 차단 되었다는 경고 메시지를 받을 때 문제가 발생할 수 있습니다. 이 문제를 해결 하려면 다음 단계를 사용 하 여 브라우저에서 타사 쿠키 옵션을 사용 하도록 설정 합니다.

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>모든 쿠키 허용

1. 브라우저에서 **chrome://settings/cookies** 을 방문 합니다.
1. **모든 쿠키 허용** 옵션을 선택 합니다. 

    ![Chrome의 모든 쿠키 허용](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. ADF UX를 새로 고치고 다시 시도 하세요.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>ADF UX만 쿠키를 사용할 수 있도록 허용
모든 쿠키를 허용 하지 않으려는 경우 ADF UX를 선택적으로 허용할 수 있습니다.
1. **Chrome://settings/cookies**을 방문 하세요.
1. **항상 쿠키를 사용할 수 있는 사이트** 에서 **추가** 를 선택 합니다. 옵션 

    ![Chrome의 허용 된 사이트에 ADF UX 추가](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. **Adf.azure.com** 사이트를 추가 하 고 **모든 쿠키** 옵션을 선택한 다음 저장을 선택 합니다. 

    ![ADF UX 사이트의 모든 쿠키 허용](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. ADF UX를 새로 고치고 다시 시도 하세요.

### <a name="microsoft-edge"></a>Microsoft Edge

1. 브라우저에서 **edge://settings/content/cookies** 을 방문 합니다.
1. **사이트에서 쿠키 데이터를 저장 하 고 읽을 수 있음** 이 사용 하도록 설정 되어 있고 **타사 쿠키 차단** 옵션이 사용 되지 않도록 설정 되어 있는지 확인 합니다. 

    ![Edge의 모든 쿠키 허용](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. ADF UX를 새로 고치고 다시 시도 하세요.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>ADF UX만 쿠키를 사용할 수 있도록 허용

모든 쿠키를 허용 하지 않으려는 경우 ADF UX를 선택적으로 허용할 수 있습니다.

1. **Edge://settings/content/cookies**을 방문 하세요.
1. **허용** 섹션에서 **추가** 를 선택 하 고 **adf.azure.com** site를 추가 합니다. 

    ![Edge의 허용 된 사이트에 ADF UX 추가](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. ADF UX를 새로 고치고 다시 시도 하세요.

## <a name="connection-failed-on-adf-ux"></a>ADF UX에서 연결 실패

경우에 따라 **연결 테스트**, **미리 보기**등을 클릭 한 후 아래 스크린샷 처럼 ADF UX에서 "연결 하지 못했습니다." 오류가 표시 될 수 있습니다.

![연결 실패](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

이 경우 먼저 브라우저에서 InPrivate 브라우징 모드를 사용 하 여 동일한 작업을 시도해 볼 수 있습니다.

여전히 작동 하지 않는 경우 브라우저에서 F12 키를 눌러 **개발자 도구**을 엽니다. **네트워크** 탭으로 이동 하 여 **캐시 사용 안 함**을 선택 하 고 실패 한 작업을 다시 시도 하 여 실패 한 요청 (빨간색)을 찾습니다.

![실패 한 요청](media/data-factory-ux-troubleshoot-guide/failed-request.png)

그런 다음 실패 한 요청의 **요청 URL** 에서 **호스트 이름** (이 경우 **dpnortheurope.svc.datafactory.azure.com**)을 찾습니다.

브라우저의 주소 표시줄에 직접 **호스트 이름을** 입력 합니다. 브라우저에서 404이 표시 되는 경우이는 일반적으로 클라이언트 쪽이 양호 하 고 ADF 서비스 쪽에 문제가 있음을 의미 합니다. ADF UX 오류 메시지의 **활동 ID** 를 사용 하 여 지원 티켓을 제출 합니다.

![리소스를 찾을 수 없음](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

표시 되지 않거나 브라우저에서 유사한 오류가 표시 되 면 일반적으로 클라이언트 쪽 문제가 있음을 의미 합니다. 문제 해결 단계를 추가로 수행 합니다.

![클라이언트 쪽 오류](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

**명령 프롬프트** 를 열고 **nslookup dpnortheurope.svc.datafactory.azure.com**을 입력 합니다. 일반적인 응답은 다음과 같습니다.

![명령 응답 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

일반 DNS 응답이 표시 되는 경우이 호스트 이름에 대 한 HTTPS 연결이 차단 되었는지 여부에 대 한 방화벽 설정을 확인 하려면 로컬 IT 지원 담당자에 게 문의 하세요. 문제를 해결할 수 없는 경우 ADF UX 오류 메시지의 **활동 ID** 를 사용 하 여 지원 티켓을 제출 합니다.

이 외에 다른 항목이 표시 되는 경우이는 일반적으로 dns 이름을 확인할 때 DNS 서버에 문제가 있음을 의미 합니다. 일반적으로 ISP (인터넷 서비스 공급자) 또는 DNS (예: Google DNS 8.8.8.8)를 변경 하는 것은 시도해 볼 수 있는 해결 방법입니다. 문제가 지속 되 면 **nslookup datafactory.azure.com** 및 **nslookup azure.com** 를 추가로 시도 하 여 DNS 확인이 실패 한 수준을 확인 하 고 모든 정보를 로컬 IT 지원 또는 ISP에 게 제출 하 여 문제를 해결할 수 있습니다. 문제가 여전히 Microsoft 쪽에 있다고 생각 되는 경우 ADF UX 오류 메시지에서 **활동 ID** 를 사용 하 여 지원 티켓을 제출 합니다.

![명령 응답 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>데이터 집합에서 연결 된 서비스 유형 변경

모든 파일 기반 커넥터에서 파일 형식 데이터 집합을 사용할 수 있습니다. 예를 들어 Azure Blob 또는 Azure Data Lake Storage Gen2에서 Parquet 데이터 집합을 구성할 수 있습니다. 각 커넥터는 다른 앱 모델을 사용 하 여 작업에 대해 서로 다른 데이터 저장소 관련 설정 집합을 지원 합니다. 

ADF 제작 UI에서 복사, 조회, GetMetadata, 삭제 작업 등의 작업에서 파일 형식 데이터 집합을 사용 하는 경우 현재와 다른 형식의 연결 된 서비스 (예: 파일 시스템에서 ADLS Gen2로 전환)를 가리키는 데이터 집합의 경우 다음과 같은 경고 메시지가 표시 됩니다. 이 스위치가 깨끗 한 스위치 인지 확인 하기 위해이 데이터 집합을 참조 하는 파이프라인 및 활동은 새로운 유형을 사용 하도록 수정 되며 새 유형과 호환 되지 않는 기존 데이터 저장소 설정은 더 이상 적용 되지 않으므로 제거 됩니다.

각 커넥터에 대해 지원 되는 데이터 저장소 설정에 대 한 자세한 내용을 보려면 해당 커넥터 문서 > 복사 작업 속성으로 이동 하 여 자세한 속성 목록을 볼 수 있습니다. [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)및 [SFTP](connector-sftp.md)를 참조 하세요.

![경고 메시지](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

* [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
* [Azure 비디오](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
