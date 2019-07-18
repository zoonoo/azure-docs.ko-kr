---
title: 'Azure Blob Storage에서 가져오기: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: 이 항목에서는 machine learning 실험에서에서 데이터를 사용할 수 있도록 Azure blob storage에서 데이터를 읽는 Azure Blob Storage 모듈에서 가져온 Azure Machine Learning 서비스에서 사용 하는 방법을 설명에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029687"
---
# <a name="import-from-azure-blob-storage-module"></a>Azure Blob Storage 모듈 가져오기

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

Machine learning 실험에서에서 데이터를 사용할 수 있도록 Azure blob storage에서 데이터를 읽는이 모듈을 사용 합니다.  

Azure Blob 서비스는 많은 양의 이진 데이터를 포함 한 데이터를 저장입니다. HTTP 또는 HTTPS를 사용 하 여 azure blob은 어디에서 나 액세스할 수 있습니다. Blob 저장소의 형식에 따라 인증 해야 할 수 있습니다. 

- 공용 blob 또는 SAS URL이 있는 사용자를 모든 사용자가 액세스할 수 있습니다.
- 개인 blob에는 로그인 및 자격 증명 필요합니다.

Blob storage에서 가져오는 데이터를 사용 하는 blob에 저장할 수는 필요 합니다 **블록 blob** 형식입니다. Blob에 저장 된 파일에는 쉼표로 구분 된 (CSV) 또는 탭으로 구분 된 (TSV) 형식 중 하나 사용 해야 합니다. 파일을 읽을 때 레코드 및 해당 되는 특성 제목이 행으로 메모리에 로드를 데이터 집합.


프로 파일링 데이터를 가져오기 전에 스키마 예상 대로 인지 확인 하는 것이 좋습니다. 가져오기 프로세스는 몇 가지 스키마를 확인 하려면 헤드 행 개수를 검색 하지만 이후 행 추가 열 또는 오류를 발생 시킨 데이터에 포함 될 수 있습니다.



## <a name="manually-set-properties-in-the-import-data-module"></a>데이터 가져오기 모듈의 속성을 수동으로 설정 합니다.

다음 단계를 수동으로 가져올 소스를 구성 하는 방법에 설명 합니다.

1. 추가 된 **데이터 가져오기** 모듈을 실험 합니다. 인터페이스에서이 모듈을 찾을 수 있습니다는 **데이터 입력 및 출력**

2. 에 대 한 **데이터 원본**를 선택 **Azure Blob Storage**합니다.

3. 에 대 한 **인증 유형**, 선택 **(SAS URL) 공용** 공용 데이터 원본으로 정보를 제공한 된 알고 있는 경우. SAS URL은 Azure storage 유틸리티를 사용 하 여 생성할 수 있는 공용 액세스에 대 한 시간 제한이 URL.

    선택 하 고 그렇지 **계정**합니다.

4. 데이터의 경우는 **공용** blob SAS URL을 사용 하 여 액세스할 수 있는 URL 문자열을 다운로드 및 인증에 필요한 모든 정보가 들어 있으므로 추가 자격 증명이 필요 하지 않습니다.

    에 **URI** 필드를 입력 하거나 계정과 공개 blob를 정의 하는 전체 URI를 붙여 넣습니다.



5. 데이터의 경우는 **개인** 계정의 계정 이름과 키를 포함 하 여 자격 증명을 제공 해야 합니다.

    - 에 대 한 **계정 이름**, 입력 또는 액세스 하려는 blob을 포함 하는 계정의 이름을 붙여 넣습니다.

        예를 들어 저장소 계정의 전체 URL은 `http://myshared.blob.core.windows.net`를 입력 하면 `myshared`합니다.

    - 에 대 한 **계정 키**, 계정과 연결 된 저장소 액세스 키를 붙여 넣습니다.

        액세스 키를 모르는 경우 섹션을 참조 하세요, "이 문서의" Azure storage 계정 관리: [Azure Storage 계정 정보](https://docs.microsoft.com/azure/storage/storage-create-storage-account)

6. 에 대 한 **컨테이너, 디렉터리 또는 blob 경로**를 검색 하려는 특정 blob의 이름을 입력 합니다.

    예를 들어, 라는 파일을 업로드 하는 경우 **data01.csv** 컨테이너로 **trainingdata** 명명 된 계정에 **mymldata**, 파일에 대 한 전체 url: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` .

    필드에 따라서 **컨테이너, 디렉터리 또는 blob 경로**를 입력 합니다. `trainingdata/data01.csv`

    여러 파일을 가져올 와일드 카드 문자를 사용할 수 있습니다 `*` (별표) 또는 `?` (물음표)입니다.

    예를 들어, 컨테이너를 가정 `trainingdata` 여러 파일이 호환 되는 형식 사양을 모든 파일을 읽을 사용할 수 있습니다 `data`, 단일 데이터 집합에 연결 하 고:

    `trainingdata/data*.csv`

    컨테이너 이름에 와일드 카드를 사용할 수 없습니다. 여러 컨테이너에서 파일을 가져올 경우의 별도 인스턴스를 사용 합니다 **데이터 가져오기** 각 컨테이너를 사용 하 여 데이터 집합 병합 모듈을 [행 추가](./add-rows.md) 모듈.

    > [!NOTE]
    > 옵션을 선택한 경우 **캐시 된 결과 사용 하 여**, 컨테이너의 파일에 수행한 모든 변경 내용을 실험에서 데이터 새로 고침을 트리거하지 않습니다.

7. 에 대 한 **Blob 파일 형식**, Azure Machine Learning은 데이터를 적절 하 게 처리할 수 있도록 blob에 저장 된 데이터의 형식을 지정 하는 옵션을 선택 합니다. 다음 형식이 지원 됩니다.

    - **CSV**: 쉼표로 구분 된 값 (CSV)는 Azure Machine Learning에서 파일을 가져오고 내보내는 기본 저장소 형식입니다. 데이터에 머리글 행이 이미 있으면 사용할 옵션을 선택 해야 **파일에 머리글 행**, 또는 헤더는 데이터 행으로 간주 됩니다.

       

    - **TSV**: 탭으로 구분 된 값 (TSV)은 많은 기계 학습 도구에서 사용 되는 형식입니다. 데이터에 머리글 행이 이미 있으면 사용할 옵션을 선택 해야 **파일에 머리글 행**, 또는 헤더는 데이터 행으로 간주 됩니다.

       

    - **ARFF**: 이 형식은 Weka 도구 집합에서 사용 하는 파일을 가져오는 것을 지원 합니다. 

   

8. 실험을 실행합니다.


## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 