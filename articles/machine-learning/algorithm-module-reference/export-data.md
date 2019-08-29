---
title: '데이터 내보내기: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 데이터 내보내기 모듈을 사용 하 여 실험의 결과, 중간 데이터 및 작업 데이터를 Azure Machine Learning 외부의 클라우드 저장소 대상에 저장 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a4fb539f4c86d27813b60964794fc1f398d3f2a4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128772"
---
# <a name="export-data-module"></a>데이터 내보내기 모듈

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 실험의 결과, 중간 데이터 및 작업 데이터를 Azure Machine Learning 외부의 클라우드 저장소 대상에 저장 합니다.

이 모듈은 데이터를 다음 클라우드 데이터 서비스로 내보내거나 저장할 수 있도록 지원 합니다.


- **Azure Blob Storage로 내보내기**: Azure의 Blob service에 데이터를 저장 합니다. Blob service의 데이터는 공개적으로 공유 하거나 보안 된 응용 프로그램 데이터 저장소에 저장할 수 있습니다.

  
## <a name="how-to-configure-export-data"></a>내보내기 데이터를 구성 하는 방법

1. 인터페이스의 실험에 **데이터 내보내기** 모듈을 추가 합니다. 이 모듈은 **입력 및 출력** 범주에서 찾을 수 있습니다.

2. 내보낼 데이터를 포함 하는 모듈에 **내보내기 데이터** 를 연결 합니다.

3. **데이터 내보내기** 를 두 번 클릭 하 여 **속성** 창을 엽니다.

4. **데이터 대상**에서 데이터를 저장할 클라우드 저장소 유형을 선택 합니다. 이 옵션을 변경 하면 다른 모든 속성이 다시 설정 됩니다. 따라서 먼저이 옵션을 선택 해야 합니다.

5. 지정 된 저장소 계정에 액세스 하는 데 필요한 계정 이름 및 인증 방법을 제공 합니다.

    **Azure Blob Storage로 내보내기** 는 비공개 미리 보기의 유일한 옵션입니다. 다음은 모듈을 설정 하는 방법을 보여 줍니다.
    1. Azure blob service는 이진 데이터를 포함 하 여 대량의 데이터를 저장 하는 데 사용할 수 있습니다. Blob 저장소에는 공용 blob 및 로그인 자격 증명이 필요한 blob의 두 가지 유형이 있습니다.

    2. **인증 유형**에 대해 저장소에서 sas URL을 통해 액세스를 지원함을 알고 있는 경우 **공용 (sas)** 을 선택 합니다.

          SAS URL은 Azure storage 유틸리티를 사용 하 여 생성할 수 있는 특별 한 유형의 URL 이며 제한 된 시간 동안만 사용할 수 있습니다.  여기에는 인증 및 다운로드에 필요한 모든 정보가 포함 됩니다.

        **Uri**에 대해 계정과 공용 blob을 정의 하는 전체 uri를 입력 하거나 붙여 넣습니다.

        파일 형식의 경우 CSV 및 TSV가 지원 됩니다.

    3. 개인 계정의 경우, **계정**을 선택 하 고, 실험에서 저장소 계정에 쓸 수 있도록 계정 이름과 계정 키를 제공 합니다.

         - **계정 이름**: 데이터를 저장 하려는 계정 이름을 입력 하거나 붙여 넣습니다. 예를 들어 저장소 계정의 전체 URL이 인 `http://myshared.blob.core.windows.net`경우을 입력 `myshared`합니다.

        - **계정 키**: 계정과 연결 된 저장소 액세스 키를 붙여넣습니다.

        -  **컨테이너, 디렉터리 또는 blob의 경로**: 내보낸 데이터를 저장할 blob의 이름을 입력 합니다. 예를 들어 **mymldata**라는 계정의 컨테이너 `http://mymldata.blob.core.windows.net/predictions/results01.csv` **예측** 에서 **results01** 라는 새 blob에 실험 결과를 저장 하기 위해 blob의 전체 URL은가 됩니다.

            따라서 **컨테이너, 디렉터리 또는 blob에 대**한 필드 경로에서 다음과 같이 컨테이너와 blob 이름을 지정 합니다.`predictions/results01.csv`

        - 아직 존재 하지 않는 blob의 이름을 지정 하면 Azure에서 blob을 만듭니다.

       -  기존 blob에 쓰는 경우 **Azure blob storage 쓰기 모드**속성을 설정 하 여 blob의 현재 콘텐츠를 덮어쓰도록 지정할 수 있습니다. 기본적으로이 속성은 **오류**로 설정 됩니다. 즉, 같은 이름의 기존 blob 파일을 찾을 때마다 오류가 발생 합니다.


    4. **Blob 파일의 파일 형식**에 대해 데이터를 저장할 형식을 선택 합니다.

        - **CSV**: CSV (쉼표로 구분 된 값)는 기본 저장소 형식입니다. 열 머리글을 데이터와 함께 내보내려면 **blob 헤더 행 쓰기**옵션을 선택 합니다.  Azure Machine Learning에서 사용 되는 쉼표로 구분 된 형식에 대 한 자세한 내용은 [CSV로 변환](./convert-to-csv.md)을 참조 하세요.

        - **TSV**: TSV (탭으로 구분 된 값) 형식은 많은 기계 학습 도구와 호환 됩니다. 열 머리글을 데이터와 함께 내보내려면 **blob 헤더 행 쓰기**옵션을 선택 합니다.  

 
    5. **캐시 된 결과 사용**: 실험을 실행할 때마다 blob 파일에 결과를 다시 작성 하지 않으려면이 옵션을 선택 합니다. 모듈 매개 변수에 대 한 다른 변경 내용이 없는 경우 실험은 모듈이 처음 실행 될 때 또는 데이터가 변경 된 경우에만 결과를 기록 합니다.

    6. 실험을 실행합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스에 [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 