---
title: 용어를 만들고, 가져오고, 내보내는 방법
description: Azure 부서의 범위에서 용어를 만들고 가져오고 내보내는 방법에 대해 알아봅니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 6f7f481ae0e0c75b14d894080f791161346cd93f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952408"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>용어를 만들고, 가져오고, 내보내는 방법

이 문서에서는 .csv 파일을 사용 하 여 Azure 부서의 범위 data catalog에 비즈니스 용어집 용어를 만들고 용어집 용어를 가져오고 내보내는 방법을 설명 합니다.

## <a name="create-a-new-term"></a>새 용어 만들기

새 용어집 용어를 만들려면 다음 단계를 수행 합니다.

1. 홈 페이지의 왼쪽 탐색 영역에서 용어집 아이콘을 선택 하 여 용어 목록 페이지로 이동 합니다.

2. **용어 설명 페이지에서** **+ 새 용어** 를 선택 합니다. **시스템 기본** 템플릿이 선택 된 상태로 페이지가 열립니다. 용어집 용어를 만드는 데 사용할 템플릿을 선택 하 고 **계속** 을 선택 합니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="새 용어 만들기의 스크린샷" border="true":::

3. 새 용어에 이름을 지정 합니다 .이 이름은 카탈로그에서 고유 해야 합니다. 용어 이름은 대/소문자를 구분 합니다. 즉, 카탈로그에서 **sample** 및 **sample** 이라는 용어를 사용할 수 있습니다.

4. **정의** 를 추가 합니다.

5. 조건에 대 한 **상태** 를 설정 합니다. 새 용어는 기본 **초안** 상태입니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="상태 선택 항목의 스크린샷" border="true":::

   이러한 상태 표식은 용어와 연결 된 메타 데이터입니다. 현재 각 용어에 대해 다음 상태를 설정할 수 있습니다.

   - **초안**:이 용어는 아직 공식적으로 구현 되지 않았습니다.
   - **승인 됨**:이 용어는 공식/표준/승인입니다.
   - **만료 됨**:이 용어는 더 이상 사용 되지 않습니다.
   - **경고**:이 용어는 주의가 필요 합니다.

6. **리소스** 및 **머리글자어** 를 추가 합니다. 기간이 계층의 일부인 경우 개요 탭에서 부모 용어를 **부모** 에 추가할 수 있습니다.

7. 관련 탭에서 **동의어** 및 **관련 용어** 를 추가 합니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="새 용어 > 관련 탭의 스크린샷" border="true":::

8. 필요에 따라 **연락처** 탭을 선택 하 여 용어에 전문가 및 관리자를 추가 합니다.

9. **만들기** 를 선택 하 여 용어를 만듭니다.

## <a name="import-terms-into-the-glossary"></a>용어를 용어집으로 가져오기

Azure 부서의 범위 Data Catalog는 용어를 용어집으로 가져올 수 있는 템플릿 .csv 파일을 제공 합니다.

카탈로그에서 용어를 가져올 수 있습니다. 파일에서 중복 된 용어를 덮어씁니다.

용어 이름은 대/소문자를 구분 합니다. 예를 들어 `Sample` 및는 `saMple` 모두 동일한 용어집에 있을 수 있습니다.

### <a name="to-import-terms-follow-these-steps"></a>용어를 가져오려면 다음 단계를 수행 합니다.

1. **용어 설명 페이지에서** **용어 가져오기** 를 선택 합니다.

2. 용어 템플릿 페이지가 열립니다. 용어 템플릿을의 종류와 일치 시킵니다. 가져올 CSV입니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="용어 용어 페이지, 용어 가져오기 단추 스크린샷":::

3. Csv 템플릿을 다운로드 하 고 추가 하려는 용어를 입력 하는 데 사용 합니다.

   > [!Important]
   > 시스템에서는 템플릿에서 사용할 수 있는 열을 가져오는 작업만 지원 합니다. "시스템 기본" 템플릿에는 모든 기본 특성이 포함 됩니다.
   > 그러나 사용자 지정 용어 템플릿에서는 템플릿에 정의 된 기본 특성 및 추가 사용자 지정 특성을 사용할 수 있습니다. 따라서입니다. CSV 파일은 선택한 용어 템플릿에 따라 총 열 및 열 이름 수와 다를 수 있습니다. 업로드 후 문제에 대 한 파일을 검토할 수도 있습니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="용어 설명 페이지의 스크린샷에서 가져올 파일을 선택 합니다.":::

4. .Csv 파일을 채운 후에는 가져올 파일을 선택 하 고 **확인** 을 선택 합니다.

5. 시스템이 파일을 업로드 하 고 모든 용어를 카탈로그에 추가 합니다.
 
   > [!Important]
   > 관리자 및 전문가의 메일 주소는 AAD 그룹의 사용자에 대 한 기본 주소 여야 합니다. 대체 메일, 사용자 계정 이름 및 비 AAD 메일은 아직 지원 되지 않습니다. 

## <a name="export-terms-from-glossary-with-custom-attributes"></a>사용자 지정 특성을 사용 하 여 용어집에서 용어 내보내기

선택한 용어가 동일한 용어 템플릿에 속하는 경우 용어집에서 용어를 내보낼 수 있어야 합니다.

1. 용어집에 있는 경우 기본적으로 **내보내기** 단추를 사용할 수 없습니다. 내보내려는 용어를 선택 하면 선택한 용어가 동일한 템플릿에 속할 경우 **내보내기** 단추를 사용할 수 있습니다.

2. 선택한 용어를 다운로드 하려면 **내보내기** 를 선택 합니다.

 > [!Important]
   > 계층 구조에 있는 용어가 다른 용어 템플릿에 속하는 경우이를 다르게 분할 해야 합니다. 가져올 CSV 파일입니다. 또한 용어에 대 한 부모 업데이트는 현재 가져오기 프로세스를 사용 하 여 지원 되지 않습니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [자습서: 용어 만들기 및 가져오기](tutorial-import-create-glossary-terms.md) 를 참조 하세요.
