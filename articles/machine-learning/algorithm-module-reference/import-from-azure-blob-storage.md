---
title: 'Azure Blob Storage에서 가져오기: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: 이 항목에서는 Machine Learning 실험에서 데이터를 사용할 수 있도록 Azure Machine Learning 서비스의 Azure Blob Storage에서 가져오기 모듈을 사용 하 여 Azure Blob Storage에서 데이터를 읽는 방법에 대해 설명 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fea64070c496379351bb75f2a38aba9b4db70dcd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128713"
---
# <a name="import-from-azure-blob-storage-module"></a>Azure Blob Storage 모듈에서 가져오기

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 Azure blob storage에서 데이터를 읽을 수 있으므로 machine learning 실험에서 데이터를 사용할 수 있습니다.  

Azure Blob Service는 이진 데이터를 포함 하 여 대량의 데이터를 저장 하는 데 사용할 수 있습니다. Azure blob는 HTTP 또는 HTTPS를 사용 하 여 어디에서 나 액세스할 수 있습니다. Blob 저장소의 유형에 따라 인증이 필요할 수 있습니다. 

- 공용 blob은 모든 사용자가 또는 SAS URL을 가진 사용자가 액세스할 수 있습니다.
- 개인 blob에는 로그인 및 자격 증명이 필요 합니다.

Blob 저장소에서 가져오려면 데이터를 **블록 blob** 형식을 사용 하는 blob에 저장 해야 합니다. Blob에 저장 된 파일은 쉼표로 구분 된 (CSV) 또는 탭으로 구분 된 (TSV) 형식을 사용 해야 합니다. 파일을 읽으면 레코드 및 해당 하는 모든 특성 머리글이 메모리의 행으로 데이터 집합으로 로드 됩니다.


스키마가 예상 대로 작동 하려면 가져오기 전에 데이터를 프로 파일링 하는 것이 좋습니다. 가져오기 프로세스에서 일부 헤드 행을 검색 하 여 스키마를 확인 하지만, 이후 행에는 추가 열 또는 오류를 발생 시키는 데이터가 포함 될 수 있습니다.



## <a name="manually-set-properties-in-the-import-data-module"></a>데이터 가져오기 모듈에서 수동으로 속성 설정

다음 단계에서는 가져오기 원본을 수동으로 구성 하는 방법을 설명 합니다.

1. **데이터 가져오기** 모듈을 실험에 추가 합니다. 이 모듈은 인터페이스의 **데이터 입력 및 출력** 에서 찾을 수 있습니다.

2. **데이터 원본**에 대해 **Azure Blob Storage**를 선택 합니다.

3. **인증 유형**으로 정보가 공개 데이터 원본으로 제공 된 것을 알고 있는 경우 **공용 (SAS URL)** 을 선택 합니다. SAS URL은 Azure storage 유틸리티를 사용 하 여 생성할 수 있는 공용 액세스에 대 한 시간 범위 URL입니다.

    그렇지 않으면 **계정**을 선택 합니다.

4. SAS URL을 사용 하 여 액세스할 수 있는 **공용** blob에 데이터가 있는 경우 URL 문자열에 다운로드 및 인증에 필요한 모든 정보가 포함 되어 있으므로 추가 자격 증명이 필요 하지 않습니다.

    **Uri** 필드에 계정 및 공용 blob을 정의 하는 전체 uri를 입력 하거나 붙여 넣습니다.



5. 데이터가 **개인** 계정에 있는 경우 계정 이름 및 키를 포함 한 자격 증명을 제공 해야 합니다.

    - **계정 이름**에 액세스 하려는 blob이 포함 된 계정의 이름을 입력 하거나 붙여 넣습니다.

        예를 들어 저장소 계정의 전체 URL이 인 `http://myshared.blob.core.windows.net`경우을 입력 `myshared`합니다.

    - **계정 키**에 계정에 연결 된 저장소 액세스 키를 붙여넣습니다.

        액세스 키를 모르는 경우이 문서의 "Azure storage 계정 관리" 섹션을 참조 하세요. [Azure Storage 계정 정보](https://docs.microsoft.com/azure/storage/storage-create-storage-account)

6. **컨테이너, 디렉터리 또는 blob에**대 한 경로에 검색 하려는 특정 blob의 이름을 입력 합니다.

    예를 들어 이름이 **data01** 인 파일을 **mymldata**이라는 계정 `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` **trainingdata** 컨테이너에 업로드 한 경우 파일의 전체 URL은입니다.

    따라서 **컨테이너, 디렉터리 또는 blob에 대**한 필드 경로에서 다음을 입력 합니다.`trainingdata/data01.csv`

    여러 파일을 가져오려면 와일드 카드 문자 `*` (별표) 또는 `?` (물음표)를 사용할 수 있습니다.

    예를 들어 컨테이너 `trainingdata` 에 호환 되는 형식의 파일이 여러 개 있는 경우 다음 사양을 사용 하 여로 `data`시작 하는 모든 파일을 읽고 단일 데이터 집합에 연결할 수 있습니다.

    `trainingdata/data*.csv`

    컨테이너 이름에는 와일드 카드를 사용할 수 없습니다. 여러 컨테이너에서 파일을 가져와야 하는 경우에는 각 컨테이너에 대해 **데이터 가져오기** 모듈의 별도 인스턴스를 사용 하 고 [행 추가](./add-rows.md) 모듈을 사용 하 여 데이터 집합을 병합 합니다.

    > [!NOTE]
    > **캐시 된 결과를 사용**하는 옵션을 선택한 경우에는 컨테이너의 파일을 변경한 내용이 실험에서 데이터의 새로 고침을 트리거하지 않습니다.

7. **Blob 파일 형식**에서 Azure Machine Learning 데이터를 적절 하 게 처리할 수 있도록 blob에 저장 된 데이터의 형식을 나타내는 옵션을 선택 합니다. 지원 되는 형식은 다음과 같습니다.

    - **CSV**: CSV (쉼표로 구분 된 값)는 Azure Machine Learning에서 파일을 내보내고 가져오는 데 사용할 수 있는 기본 저장소 형식입니다. 데이터에 머리글 행이 이미 포함 되어 있는 경우 **파일에 머리글 행이**있거나 머리글이 데이터 행으로 처리 되는 옵션을 선택 해야 합니다.

       

    - **TSV**: TSV (탭으로 구분 된 값)는 많은 기계 학습 도구에서 사용 하는 형식입니다. 데이터에 머리글 행이 이미 포함 되어 있는 경우 **파일에 머리글 행이**있거나 머리글이 데이터 행으로 처리 되는 옵션을 선택 해야 합니다.

       

    - **ARFF**: 이 형식은 Weka 도구 집합에서 사용 하는 형식으로 파일을 가져올 수 있도록 지원 합니다. 

   

8. 실험을 실행합니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스에 [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 