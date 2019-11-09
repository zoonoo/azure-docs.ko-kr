---
title: 드 론 이미지 가져오기
description: 파트너에서 드 론 이미지를 가져오는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 19d709a2eb0af5bdb9d40a2bce96c5716ce3c6f6
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847422"
---
# <a name="get-drone-imagery-from-drone-partners"></a>드 론 파트너에서 드 론 이미지 가져오기

이 문서에서는 드 론 이미지 파트너의 orthomosaic 데이터를 Azure FarmBeats data hub에 가져오는 방법을 설명 합니다. Orthomosaic은 drone에서 수집 된 데이터에서 기하학적으로 수정 되 고 연결 되어는 항공 그림/이미지입니다.

현재 지원 되는 이미지 파트너는 다음과 같습니다.

  ![프로젝트 팜 비트](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

드 론 이미지 데이터를 Azure FarmBeats와 통합 하면 팜의 드 론 항공편에서 데이터 허브로 데이터를 orthomosaic 수 있습니다. 데이터를 사용할 수 있게 되 면 FarmBeats Accelerator에서 볼 수 있으며 데이터 fusion 및 AI/ML 모델 빌드에 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

  - Azure FarmBeats를 배포 했는지 확인 합니다. 배포 하려면 [FarmBeats 배포](prepare-for-deployment.md)를 참조 하세요.
  - FarmBeats 시스템에 정의 된 팜 (드 론 이미지)이 있는지 확인 합니다.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>FarmBeats와 드 론 이미지 통합 사용   

FarmBeats와의 통합을 사용 하도록 설정 하려면 장치 공급자에 게 다음 정보를 제공 해야 합니다.  
 - API 엔드포인트  
 - 테넌트 ID  
 - 클라이언트 ID  
 - 클라이언트 암호  

다음 단계를 사용하세요.

1. 이 [스크립트](https://aka.ms/farmbeatspartnerscript) 를 다운로드 하 고 로컬 드라이브의에서 압축을 풉니다. 이 ZIP 파일 내에서 두 개의 파일을 찾을 수 있습니다.  
2. [Azure Portal](https://portal.azure.com/) 에 로그인 하 여 Cloud Shell를 엽니다 (이 옵션은 포털의 오른쪽 상단 모음에서 사용할 수 있음).   

    ![프로젝트 팜 비트](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 환경이 **PowerShell** 로 설정 되었는지 확인

    ![프로젝트 팜 비트](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Cloud Shell에서 다운로드 한 두 파일 (위의 1 단계)을 업로드 합니다.  

    ![프로젝트 팜 비트](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 파일이 업로드 된 디렉터리로 이동 합니다. 기본적으로는 홈 디렉터리 > username에 업로드 됩니다.  
6. 다음 스크립트를 실행합니다.

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```

7. 화면의 지시에 따라 API 끝점, 테 넌 트 ID, 클라이언트 ID, 클라이언트 암호 및 EventHub 연결 문자열의 값을 캡처합니다.

    파트너의 드 론 소프트웨어 시스템에 필요한 자격 증명을 입력 하면 FarmBeats 시스템에서 모든 팜을 가져올 수 있으며 팜 세부 정보를 사용 하 여 비행 경로 계획 및 드 론 이미지 컬렉션을 수행할 수 있습니다.

    드 론 공급자 소프트웨어에서 원시 이미지를 처리 한 후 드 one 소프트웨어 시스템은 연결 되어 orthomosaic 및 기타 처리 된 이미지를 데이터 허브에 업로드 합니다.

## <a name="view-drone-imagery"></a>드 론 이미지 보기

데이터가 FarmBeats 데이터 허브로 전송 되 면 FarmBeats Data hub Api를 사용 하 여 장면 저장소를 쿼리할 수 있습니다.

또는 **팜 세부 정보** 페이지에서 최신 드 론 이미지를 볼 수 있어야 합니다. 표시 하려면 다음 단계를 수행 합니다.  

1. 이미지를 업로드 한 팜을 선택 합니다. **팜** 정보 페이지가 표시 됩니다.
2. 최신 **전체 자릿수 맵** 섹션까지 아래로 스크롤합니다.
3. **Drone** 이미지 섹션에서 이미지를 볼 수 있습니다.

    ![프로젝트 팜 비트](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>드 론 이미지 다운로드

드 론 이미지 섹션을 선택 하면 드 론 orthomosaic의 고해상도 이미지를 표시 하는 팝업이 열립니다.

![프로젝트 팜 비트](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>모든 드 론 맵 보기

드 론 공급자가 업로드 한 파일 및 이미지가 Maps 섹션에 표시 됩니다. **Maps** 섹션을 선택 하 고 **팜으로** 필터링 한 다음 보고 다운로드할 적절 한 파일을 선택 합니다.

  ![프로젝트 팜 비트](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>다음 단계

FarmBeats data hub [api](references-for-farmbeats.md#rest-api) 를 사용 하 여 드 론 이미지를 가져오는 방법을 알아봅니다.
