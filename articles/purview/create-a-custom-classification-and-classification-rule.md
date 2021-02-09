---
title: 사용자 지정 분류 및 분류 규칙 만들기 (미리 보기)
description: 이 문서에서는 사용자 지정 분류를 만들어 조직에 고유한 데이터 공간에서 데이터 형식을 정의 하는 방법을 설명 합니다. 또한 데이터 공간 전체에서 지정 된 데이터를 찾을 수 있는 사용자 지정 분류 규칙을 만드는 방법을 설명 합니다.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/5/2021
ms.openlocfilehash: 3cc29e0bd806ab76c4980128df5a89761e465fe7
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988375"
---
# <a name="custom-classifications-in-azure-purview"></a>Azure 부서의 범위의 사용자 지정 분류 

이 문서에서는 사용자 지정 분류를 만들어 조직에 고유한 데이터 공간에서 데이터 형식을 정의 하는 방법을 설명 합니다. 또한 데이터 공간 전체에서 지정 된 데이터를 찾을 수 있는 사용자 지정 분류 규칙을 만드는 방법을 설명 합니다.

## <a name="default-classifications"></a>기본 분류

Azure 부서의 범위 Data Catalog는 데이터 공간에 있을 수 있는 일반적인 개인 데이터 형식을 나타내는 기본 분류의 많은 집합을 제공 합니다.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="분류 선택" border="true":::

또한 기본 분류가 사용자의 요구에 맞지 않는 경우 사용자 지정 분류를 만들 수 있습니다.

## <a name="steps-to-create-a-custom-classification"></a>사용자 지정 분류를 만드는 단계

사용자 지정 분류를 만들려면 다음을 수행 합니다.

1. 카탈로그의 왼쪽 메뉴에서 **관리 센터** 를 선택 합니다.

2. **메타 데이터 관리** 에서 **분류** 를 선택 합니다.

3. **+ 새로 만들기** 를 선택 합니다.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="새 분류" border="true":::

분류 이름 및 설명을 제공할 수 있는 **새 분류 추가** 창이 열립니다. 과 같은 이름 간격 규칙을 사용 하는 것이 좋습니다 `your company name.classification name` .
Microsoft 시스템 분류는 예약 된 네임 스페이스에 그룹화 됩니다 `MICROSOFT.` . 예는 **MICROSOFT입니다. 자치. 돕습니다. 주민 \_ 등록 \_ 번호** 입니다.

분류 이름은 문자로 시작 하 고 뒤에 문자, 숫자, 마침표 (.) 또는 밑줄 문자를 사용 해야 합니다.
공백은 허용 되지 않습니다. 사용자가 입력 하면 UX에서 이름을 자동으로 생성 합니다. 이 이름은 카탈로그의 자산에 적용 될 때 사용자에 게 표시 되는 이름입니다.

이름을 짧게 유지 하기 위해 시스템은 다음 논리에 따라 이름을 만듭니다.

- 네임 스페이스의 마지막 두 세그먼트를 제외한 모든 세그먼트는 잘립니다.

- 대/소문자를 조정 하 여 각 단어의 첫 글자를 대문자로 표기 합니다. 다른 모든 문자는 소문자로 변환 됩니다.

- 모든 밑줄 ( \_ )은 공백으로 바뀝니다.

예를 들어 분류 CONTOSO.HR의 이름을 지정 하는 경우 **입니다. 직원 \_ id** 는 이름이 시스템에 **HR. employee id** 로 저장 됩니다.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

**확인** 을 선택 하면 새 분류가 분류 목록에 추가 됩니다.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="사용자 지정 분류" border="true":::

목록에서 분류를 선택 하면 분류 세부 정보 페이지가 열립니다. 여기서 분류에 대 한 모든 세부 정보를 찾을 수 있습니다.
이러한 세부 정보에는 여기에 포함 된 인스턴스의 수, 형식 이름, 연결 된 분류 규칙 (있는 경우) 및 소유자 이름이 포함 됩니다.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="분류 선택" border="true":::

## <a name="custom-classification-rules"></a>사용자 지정 분류 규칙

카탈로그 서비스는 스캐너에서 특정 데이터 형식을 자동으로 검색 하는 데 사용 되는 기본 분류 규칙 집합을 제공 합니다. 사용자 고유의 사용자 지정 분류 규칙을 추가 하 여 데이터 공간에서 찾을 수 있는 다른 유형의 데이터를 검색할 수도 있습니다. 이 기능은 \' 데이터 공간 내에서 데이터를 찾으려고 할 때 매우 유용할 수 있습니다.

예를 들어 \' Contoso 라는 회사는 직원 \" \" {guid}를 만드는 guid를 사용 하 여 회사 전체에서 표준화 되는 직원 id를가지고 있다고 가정해 보겠습니다. 예를 들어 직원 ID의 한 인스턴스는 EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55와 같습니다.

Contoso는 사용자 지정 분류 규칙을 만들어 이러한 Id의 인스턴스를 찾도록 검색 시스템을 구성할 수 있습니다. 데이터 패턴 (이 경우)과 일치 하는 정규식을 제공할 수 있습니다 `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . 경우에 따라 데이터는 직원 ID 또는 EmployeeID와 같이 이름을 알고 있는 열에 있는 경우 \_ 열 패턴 정규식을 추가 하 여 검색을 보다 정확 하 게 만들 수 있습니다. 예제 regex는 직원 \_ ID \| EmployeeID입니다.

그러면 검색 시스템에서이 규칙을 사용 하 여 열의 실제 데이터와 열 이름을 검사 하 여 직원 ID 패턴이 있는 모든 인스턴스를 식별할 수 있습니다.

## <a name="steps-to-create-a-custom-classification-rule"></a>사용자 지정 분류 규칙을 만드는 단계

사용자 지정 분류 규칙을 만들려면 다음을 수행 합니다.

1. 위 섹션의 지침에 따라 사용자 지정 분류를 만듭니다. 분류 규칙 구성에서이 사용자 지정 분류를 추가 하 여 시스템에서 열에 일치 하는 항목이 발견 될 때 해당 분류를 적용 하도록 합니다.

2. **관리 센터** 아이콘을 선택 합니다.

3. **분류 규칙** 섹션을 선택 합니다.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="분류 규칙 타일" border="true":::

4. **새로 만들기** 를 선택합니다.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="새 분류 규칙 추가" border="true":::

5. **새 분류 규칙** 대화 상자가 열립니다. 새 규칙에 대 한 구성 정보를 입력 합니다.

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/createclassificationrule.png" alt-text="새 분류 규칙 만들기" border="true":::

|필드     |설명  |
|---------|---------|
|속성   |    필수 사항입니다. 최대 크기는 100 자입니다.    |
|설명      |선택 사항입니다. 최대 크기는 256 자입니다.    |
|분류 이름    | 필수 요소. 드롭다운 목록에서 분류 이름을 선택 하 여 일치 하는 항목이 있는 경우 스캐너에 적용 하도록 지시 합니다.        |
|시스템 상태   |  필수 요소. 옵션은 사용 또는 사용 안 함입니다. 기본값은 Enabled입니다.    |
|데이터 패턴    |선택 사항입니다. 데이터 필드에 저장 된 데이터를 나타내는 정규식입니다. 한도가 매우 많습니다. 이전 예제에서 데이터 패턴은 단어를 문자 그대로 하는 직원 ID를 테스트 합니다 `Employee{GUID}` .  |
|열 패턴    |선택 사항입니다. 일치 시키려는 열 이름을 나타내는 정규식입니다. 한도가 매우 많습니다.          |

**데이터 패턴** 에는 두 가지 옵션이 있습니다.

- **고유 일치 임계값**: 스캐너가 데이터 패턴을 실행 하기 전에 열에 있어야 하는 고유한 데이터 값의 총 수입니다. 제안 값은 8입니다. 이 값은 2에서 32 사이의 범위로 수동으로 조정할 수 있습니다. 스캐너에서 정확 하 게 분류 하기 위한 충분 한 데이터가 열에 포함 되어 있는지 확인 하려면 시스템에서이 값이 필요 합니다. 예를 들어 값이 1 인 여러 행이 포함 된 열은 분류 되지 않습니다. 한 행에 값이 포함 되 고 나머지 행은 null 값을 포함 하는 열도 분류 되지 않습니다. 여러 패턴을 지정 하는 경우이 값이 각 패턴에 적용 됩니다.

- **최소 일치 임계값**:이 설정을 사용 하 여 스캐너가 적용 되도록 스캐너에서 찾아야 하는 열에 있는 데이터 값의 최소 비율을 설정할 수 있습니다. 제안 값은 60%입니다. 이 설정에 주의 해야 합니다. 60% 미만의 수준을 줄이면 카탈로그에 거짓-긍정 분류를 도입할 수 있습니다. 여러 데이터 패턴을 지정 하는 경우이 설정은 사용 하지 않도록 설정 되 고 값은 60%로 고정 됩니다.

## <a name="next-steps"></a>다음 단계

분류 규칙을 만들었으므로 검색 시 검색에서 규칙을 사용할 수 있도록 검색 규칙 집합에 추가 될 준비가 되었습니다. 자세한 내용은 [스캔 규칙 집합 만들기](create-a-scan-rule-set.md)를 참조 하세요.
