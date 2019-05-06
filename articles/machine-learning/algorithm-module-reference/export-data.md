---
title: 데이터를 내보냅니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 외부 클라우드 저장소 대상에 실험에서 결과, 중간 데이터 및 작업 데이터를 저장 하려면 Azure Machine Learning 서비스에서 데이터 내보내기 모듈을 사용 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028322"
---
# <a name="export-data-module"></a>내보내기 데이터 모듈

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 Azure Machine Learning 외부 클라우드 저장소 대상에 실험에서 결과, 중간 데이터 및 작업 데이터를 저장 합니다.

이 모듈 내보내거나 다음 클라우드 데이터 서비스에 데이터 저장을 지원 합니다.


- **Azure Blob 저장소로 내보내기**: Azure에서 Blob service에 데이터를 저장합니다. Blob service에서 데이터를 공개적으로 공유 또는 보안 된 응용 프로그램 데이터 저장소에 저장 수 있습니다.

  
## <a name="how-to-configure-export-data"></a>데이터 내보내기 구성 하는 방법

1. 추가 된 **데이터 내보내기** 모듈 인터페이스에서 실험을 합니다. 이 모듈에서 찾을 수 있습니다 합니다 **입력 및 출력** 범주입니다.

2. 연결 **데이터 내보내기** 내보내려면 데이터가 포함 된 모듈입니다.

3. 두 번 클릭 **데이터 내보내기** 열려는 합니다 **속성** 창입니다.

4. 에 대 한 **데이터 대상**, 데이터를 저장할 클라우드 저장소의 유형을 선택 합니다. 이 옵션을 변경 하면 다른 모든 속성이 다시 설정 됩니다. 따라서이 옵션을 먼저 선택 해야!

5. 지정된 된 저장소 계정에 액세스 해야 하는 계정 이름 및 인증 메서드를 제공 합니다.

    **Azure Blob Storage로 내보내기** 비공개 미리 보기에서 유일한 옵션입니다. 아래 모듈을 설정 하는 방법을 보여 줍니다.
    1. Azure blob service는 많은 양의 이진 데이터를 포함 한 데이터를 저장입니다. Blob 저장소의 두 가지가: 공용 blob 및 blob 로그인 자격 증명이 필요 합니다.

    2. 에 대 한 **인증 유형**, 선택 **공용 (SAS)** 저장소 SAS URL을 통해 액세스를 지원 하는지 알고 있는 경우.

          SAS URL에는 Azure storage 유틸리티를 사용 하 여 생성할 수 있습니다 사용할 수 있는 제한 된 시간에 대 한 URL의 특수 형식입니다.  인증 및 다운로드에 필요한 모든 정보를 포함 합니다.

        에 대 한 **URI**를 입력 하거나 계정과 공개 blob를 정의 하는 전체 URI를 붙여 넣습니다.

        파일 형식으로 CSV 및 TSV 지원 됩니다.

    3. 개인 계정에 대 한 선택 **계정**, 실험을 저장소 계정에 쓸 수 있도록 계정 이름과 계정 키를 제공 합니다.

         - **계정 이름**: 입력 하거나 데이터를 저장 하려는 계정의 이름을 붙여 넣습니다. 예를 들어 저장소 계정의 전체 URL은 `http://myshared.blob.core.windows.net`를 입력 하면 `myshared`합니다.

        - **계정 키**: 계정과 연결 된 저장소 액세스 키를 붙여 넣습니다.

        -  **컨테이너, 디렉터리 또는 blob 경로**: 내보낸된 데이터를 저장할 blob의 이름을 입력 합니다. 예를 들어, 새 blob에 실험의 결과 저장 하 라는 **results01.csv** 컨테이너의 **예측** 명명 된 계정에 **mymldata**에 대 한 전체 URL을 blob 수 `http://mymldata.blob.core.windows.net/predictions/results01.csv`입니다.

            필드에 따라서 **컨테이너, 디렉터리 또는 blob 경로**컨테이너를 지정 하는, 및 blob 이름으로 다음과 같습니다. `predictions/results01.csv`

        - 존재 하지 않는 blob의 이름을 지정 하는 경우 존재, Azure 사용자를 위해 blob을 만듭니다.

       -  기존 blob에 쓸 때 속성을 설정 하 여 blob의 현재 내용을 덮어쓸 수 있는지 지정할 수 있습니다 **Azure blob 저장소 쓰기 모드**합니다. 기본적으로이 속성 설정 **오류**, 동일한 이름의 기존 blob 파일은 발견 될 때마다 오류가 발생 하는지 의미 합니다.


    4. 에 대 한 **blob 파일의 파일 형식**, 데이터를 저장 해야 하는 형식을 선택 합니다.

        - **CSV**: 쉼표로 구분 된 값 (CSV)는 기본 저장소 형식입니다. 데이터와 함께 열 머리글을 내보내려면 옵션을 선택 **blob 헤더 행 쓰기**합니다.  Azure Machine Learning에서 사용 되는 쉼표 구분 기호로 분리 된 형식에 대 한 자세한 내용은 참조 하세요. [CSV로 변환](./convert-to-csv.md)합니다.

        - **TSV**: 탭으로 구분 된 값 (TSV) 형식으로 많은 기계 학습 도구와 호환 됩니다. 데이터와 함께 열 머리글을 내보내려면 옵션을 선택 **blob 헤더 행 쓰기**합니다.  

 
    5. **캐시 된 결과 사용 하 여**: 실험을 실행할 때마다 blob 파일에 결과 다시 작성 하지 않으려는 경우이 옵션을 선택 합니다. 실험을 다른 모듈 매개 변수 변경 되지 경우 결과 처음에 모듈 실행 될 때만를 작성 또는 데이터 변경 내용이 있습니다.

    6. 실험을 실행합니다.

## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 