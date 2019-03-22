---
title: 서비스로 Azure Stack 유효성 검사에 원래 장비 제조업체 (OEM) 패키지 유효성 검사 | Microsoft Docs
description: 서비스 유효성 검사를 사용 하 여 원래 장비 제조업체 (OEM) 패키지의 유효성을 검사 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aae3ec8ff713959c5cc2485951aba025a6f89a1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113285"
---
# <a name="validate-oem-packages"></a>OEM 패키지 유효성 검사

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

펌웨어 또는 드라이버 유효성 검사를 완료 된 솔루션에 대 한 변경 된 경우 새 OEM 패키지를 테스트할 수 있습니다. 패키지를 테스트에 전달 되는 경우에 Microsoft에서 서명 됩니다. 테스트는 드라이버와 Windows Server 로고 및 PC 테스트를 통과 했거나 펌웨어를 사용 하 여 업데이트 된 OEM 확장 패키지를 포함 해야 합니다.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> 검토를 업로드 하거나 패키지를 제출 하기 전에 [OEM 패키지를 만드는](azure-stack-vaas-create-oem-package.md) 예상된 패키지 형식 및 내용에 대 한 합니다.

## <a name="managing-packages-for-validation"></a>유효성 검사에 대 한 패키지 관리

사용 하는 경우는 **패키지 유효성 검사** 패키지의 유효성을 검사 하는 워크플로 대 한 URL을 제공 해야 합니다는 **Azure Storage blob**합니다. 이 blob은 업데이트 프로세스의 일부로 설치 되는 패키지에 OEM을 서명 하는 테스트입니다. 설치 중에 만든 Azure Storage 계정을 사용 하 여 blob을 만듭니다 (참조 [유효성 검사 서비스 리소스로 설정](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>필수 조건: 저장소 컨테이너를 프로 비전

패키지 blob에 대 한 저장소 계정의 컨테이너를 만듭니다. 모든 패키지 유효성 검사 실행에 대 한이 컨테이너를 사용할 수 있습니다.

1. 에 [Azure portal](https://portal.azure.com)에서 만든 저장소 계정으로 이동 [유효성 검사 서비스 리소스로 설정](azure-stack-vaas-set-up-resources.md)합니다.

2. 아래 왼쪽 블레이드에서 **Blob Service**를 선택 **컨테이너**합니다.

3. 선택 **+ 컨테이너** 메뉴 모음에서.
    1. 예를 들어 컨테이너의 이름을 제공 `vaaspackages`합니다.
    1. VaaS 같은 인증 되지 않은 클라이언트에 대해 원하는 액세스 수준을 선택 합니다. 각 시나리오에서 패키지에 대 한 VaaS 액세스 권한을 부여 하는 방법에 대 한 세부 정보를 참조 하세요 [컨테이너 액세스 수준인 처리](#handling-container-access-level)합니다.

### <a name="upload-package-to-storage-account"></a>저장소 계정에 패키지 업로드

1. 유효성을 검사 하려는 패키지를 준비 합니다. 이 `.zip` 파일 내용이에 설명 된 구조와 일치 해야 합니다 [OEM 패키지를 만드는](azure-stack-vaas-create-oem-package.md)합니다.

    > [!NOTE]
    > 있는지 확인 하십시오는 `.zip` 콘텐츠 루트에 배치 됩니다는 `.zip` 파일입니다. 패키지에는 하위 폴더가 있어야 합니다.

1. 에 [Azure portal](https://portal.azure.com), 패키지 컨테이너를 선택 하 고에서 선택 하 여 패키지를 업로드 **업로드** 메뉴 모음에서.

1. 패키지 선택 `.zip` 파일을 업로드 합니다. 에 대 한 기본값을 유지 **Blob 유형** (즉, **블록 Blob**) 및 **블록 크기**합니다.

### <a name="generate-package-blob-url-for-vaas"></a>VaaS 패키지 blob URL 생성

만들 때를 **패키지 유효성 검사** VaaS 포털에서 워크플로 해야 패키지를 포함 하는 Azure Storage blob에 대 한 URL을 제공 합니다. 일부 *대화형* 테스트를 포함 하 여 **월간 azurestack의 경우 업데이트 확인** 및 **OEM 확장 패키지 확인**, 패키지 blob에 대 한 URL도 필요 합니다.

#### <a name="handling-container-access-level"></a>컨테이너 액세스 수준인 처리

VaaS에 필요한 최소 액세스 수준 패키지 유효성 검사 워크플로 작성 하거나 예약 여부에 따라 달라 집니다를 *대화형* 테스트 합니다.

경우 **개인** 하 고 **Blob** 수준 액세스 VaaS 제공 하 여 패키지 blob에 대 한 액세스를 일시적으로 부여 해야 합니다는 [공유 액세스 서명](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). 합니다 **컨테이너** 액세스 수준 SAS Url을 생성할 수 있습니다 필요 하지 않지만 컨테이너 및 해당 blob에 대 한 인증 되지 않은 액세스를 허용 합니다.

|액세스 수준 | 워크플로 요구 사항 | 테스트 요구 사항 |
|---|---------|---------|
|개인 | 패키지 blob 당 SAS URL 생성 ([옵션 1](#option-1-generate-a-blob-sas-url)). | 계정 수준 SAS URL을 생성 하 고 패키지 blob 이름을 수동으로 추가 ([옵션 2](#option-2-construct-a-container-sas-url)). |
|Blob | Blob URL 속성을 제공 ([옵션 3](#option-3-grant-public-read-access)). | 계정 수준 SAS URL을 생성 하 고 패키지 blob 이름을 수동으로 추가 ([옵션 2](#option-2-construct-a-container-sas-url)). |
|컨테이너 | Blob URL 속성을 제공 ([옵션 3](#option-3-grant-public-read-access)). | Blob URL 속성을 제공 ([옵션 3](#option-3-grant-public-read-access)).

패키지에 대 한 액세스 권한을 부여 하는 것에 대 한 옵션을 가장 큰 액세스에 대 한 최소 액세스에서 정렬 됩니다.

#### <a name="option-1-generate-a-blob-sas-url"></a>옵션 1: Blob SAS URL 생성

저장소 컨테이너의 액세스 수준 설정 된 경우이 옵션을 사용 하 여 **개인**, 컨테이너 컨테이너나 해당 blob에 대 한 공용 읽기 액세스 사용 하지 않습니다.

> [!NOTE]
> 이 메서드에 대해 작동 하지 것입니다 *대화형* 테스트 합니다. 참조 [옵션 2: 컨테이너 SAS URL 생성](#option-2-construct-a-container-sas-url)합니다.

1. 에 [Azure portal](https://portal.azure.com/)에 저장소 계정으로 이동 하 고 패키지에 포함 된.zip으로 이동 합니다.

2. 선택 **SAS 생성** 상황에 맞는 메뉴입니다.

3. 선택 **읽기** 에서 **권한을**합니다.

4. 설정할 **시작 시간** 은 현재 시간 및 **종료 시간** 48 시간 이상 **시작 시간**합니다. 동일한 패키지를 사용 하 여 다른 워크플로 만들기는 늘리는 것이 좋습니다 **종료 시간** 테스트 길이 대 한 합니다.

5. **URL 및 Blob SAS 토큰 생성**을 선택합니다.

사용 된 **Blob SAS URL** 포털로 Url blob 패키지를 제공 합니다.

#### <a name="option-2-construct-a-container-sas-url"></a>옵션 2: 컨테이너 SAS URL 생성

저장소 컨테이너의 액세스 수준 설정 된 경우이 옵션을 사용 하 여 **개인** 패키지 blob URL을 제공 해야 하는 *대화형* 테스트 합니다. 워크플로 수준에서이 URL은 사용할 수도 있습니다.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. 선택 **Blob** 에서 **허용 된 서비스 옵션**합니다. 나머지 옵션을 선택 취소 합니다.

1. 선택 **컨테이너** 하 고 **개체** 에서 **허용 되는 리소스 유형**합니다.

1. 선택 **읽기** 하 고 **목록** 에서 **권한이**합니다. 나머지 옵션을 선택 취소 합니다.

1. 선택 **시작 시간** 현재 시간으로 및 **종료 시간** 에서 14 일 이상 **시작 시간**합니다. 동일한 패키지를 사용 하 여 다른 테스트 실행은 늘리는 것이 좋습니다 **종료 시간** 테스트 길이 대 한 합니다. 후 VaaS 통해 예약 된 테스트가 **종료 시간** 는 실패 하 고 새 SAS를를 생성 해야 합니다.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    형식은 다음과 같이 표시 됩니다. `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. 패키지 컨테이너를 포함 하도록 생성 된 SAS URL을 수정 `{containername}`, 패키지 blob 이름과 `{mypackage.zip}`다음과 같이 합니다.  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    포털 Url blob 패키지를 제공 하는 경우이 값을 사용 합니다.

#### <a name="option-3-grant-public-read-access"></a>옵션 3: 공용 읽기 액세스 권한 부여

인증 되지 않은 클라이언트가 각 blob 또는의 경우에 액세스할 수 있도록 허용 되는 경우이 옵션을 사용 하 여 *대화형* 컨테이너를 테스트 합니다.

> [!CAUTION]
> 이 옵션에 blob을 익명 읽기 전용 액세스를 엽니다.

1. 패키지 컨테이너의 액세스 수준을 설정할 **Blob** 하거나 **컨테이너** 섹션의 지침에 따라 [컨테이너 및 blob익명사용자에게권한을부여](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs).

    > [!NOTE]
    > 패키지 URL을 제공 하는 경우는 *대화형* 부여 해야 테스트 **전체 공개 읽기 액세스** 테스트를 진행 하려면 컨테이너에 있습니다.

1. 패키지 컨테이너에서 속성 창을 열려면 패키지 blob을 선택 합니다.

1. 복사 합니다 **URL**합니다. 포털 Url blob 패키지를 제공 하는 경우이 값을 사용 합니다.

## <a name="create-a-package-validation-workflow"></a>패키지 유효성 검사 워크플로 만들기

1. 에 로그인 합니다 [VaaS 포털](https://azurestackvalidation.com)합니다.

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 선택 **시작** 에 **패키지 유효성 검사** 바둑판식으로 배열 합니다.

    ![패키지 유효성 검사 워크플로 타일](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Azure Storage 입력 OEM 패키지에 Microsoft에서 서명 요구를 서명 하는 blob URL을 테스트 합니다. 자세한 내용은 [VaaS 패키지 blob URL 생성](#generate-package-blob-url-for-vaas)합니다.

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 워크플로 만든 후 환경 매개 변수를 수정할 수 없습니다.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    테스트 요약 페이지로 리디렉션됩니다.

## <a name="required-tests"></a>필요한 테스트

다음 테스트를이 OEM 패키지 유효성 검사가 필요 합니다.

- OEM 확장 패키지 확인
- 클라우드 시뮬레이션 엔진

## <a name="run-package-validation-tests"></a>패키지 유효성 검사 테스트를 실행 합니다.

1. 에 **패키지 유효성 검사 테스트 요약** 페이지에서 나열 된 테스트 시나리오에 적절 한 하위 집합을 실행 합니다.

    유효성 검사 워크플로에서 **예약** 워크플로 만드는 동안 지정 된 워크플로 수준 일반적인 매개 변수를 사용 하는 테스트 (참조 [서비스로AzureStack유효성검사에대한워크플로일반매개변수](azure-stack-vaas-parameters.md)). 테스트 매개 변수 값에 유효 하지 않게 됩니다, 경우 있습니다 해야 재충전할 하에 설명 된 대로 [워크플로 매개 변수를 수정](azure-stack-vaas-monitor-test.md#change-workflow-parameters)합니다.

    > [!NOTE]
    > 기존 인스턴스에 대해 유효성 검사 테스트를 예약 이전 인스턴스 대신 새 인스턴스를 포털에서 만들어집니다. 이전 인스턴스에 대 한 로그는 보존할 되지만 포털에서 액세스할 수 없습니다.  
    > 테스트를 성공적으로 완료 되 면 합니다 **일정** 작업 비활성화 합니다.

2. 테스트를 실행 하는 에이전트를 선택 합니다. 자세한 내용은 추가 하는 방법에 대 한 로컬 테스트 실행 에이전트 참조 하십시오 [로컬 에이전트를 배포](azure-stack-vaas-local-agent.md)합니다.

3. OEM 확장 패키지 확인을 완료 하려면 선택 **일정** 테스트 인스턴스 예약에 대 한 프롬프트를 열려면 상황에 맞는 메뉴에서입니다.

4. 테스트 매개 변수를 검토 하 고 선택한 **제출** 실행의 OEM 확장 패키지 확인을 예약 합니다.

    OEM 확장 패키지 확인 수동 두 단계로 분할 됩니다. Azure Stack Update 및 OEM 업데이트 합니다.

   1. **선택** precheck 스크립트를 실행 하는 UI에서 "실행" 합니다. 이 자동화 된 테스트는 완료 하려면 약 5 분 걸립니다 및 작업이 필요 하지 않습니다.

   1. Precheck 스크립트가 완료 되 면 수동 단계를 수행 합니다. **설치** 최신 사용 가능한 Azure Stack 업데이트를 Azure Stack 포털을 사용 합니다.

   1. **실행** 테스트-AzureStack 스탬프에. 오류가 발생 한 경우 진행 하지 마십시오 테스트 및 연락처 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com)합니다.

       테스트-azurestack의 경우 명령을 실행 하는 방법에 대 한 자세한 내용은 [의 유효성을 검사 하는 Azure Stack 시스템 상태](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)합니다.

   1. **선택** postcheck 스크립트를 실행 하려면 "다음"입니다. 이 자동화 된 테스트 및 Azure Stack 업데이트 프로세스의 끝을 표시 합니다.

   1. **선택** OEM 업데이트용 precheck 스크립트를 실행 하려면 "실행" 합니다.

   1. 사전 검사를 완료 되 면 수동 단계를 수행 합니다. **설치** 포털을 통해 OEM 확장 패키지입니다.

   1. **실행** 테스트-AzureStack 스탬프에.

      > [!NOTE]
      > 이전과 마찬가지로 테스트 및 연락처를 사용 하 여 계속 하지 마세요 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) 실패 한 경우. 이 단계는 중요 한 재배포 절약 될 것입니다.

   1. **선택** postcheck 스크립트를 실행 하려면 "다음"입니다. 이 OEM update 단계의 끝을 표시 합니다.

   1. 테스트의 끝에 나머지 모든 질문에 답변 하 고 **선택** "제출".

   1. 이 대화형 테스트의 끝을 표시 합니다.

5. OEM 확장 패키지 확인에 대 한 결과 검토 합니다. 테스트 성공 되 면 클라우드 시뮬레이션 엔진 실행을 예약 합니다.

패키지 서명 요청을 제출 하려면 송신 [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) 이 실행을 사용 하 여 연결 된 솔루션 이름과 패키지 유효성 검사 합니다.

## <a name="next-steps"></a>다음 단계

- [모니터링 및 VaaS 포털에서 테스트를 관리 합니다.](azure-stack-vaas-monitor-test.md)
