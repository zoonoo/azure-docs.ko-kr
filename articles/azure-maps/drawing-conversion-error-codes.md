---
title: Azure Maps 그리기 변환 오류 및 경고
description: Azure Maps Conversion 서비스를 사용하는 동안 발생할 수 있는 Conversion 오류 및 경고에 대해 알아봅니다. 몇 가지 예와 함께 오류 및 경고를 해결하는 방법에 대한 추천 사항을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: d79c42f3bdf84efcdf2187741ac270087be05272
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83681965"
---
# <a name="drawing-conversion-errors-and-warnings"></a>그리기 변환 오류 및 경고

[Azure Maps Conversion 서비스](https://docs.microsoft.com/rest/api/maps/conversion)를 사용하여 업로드된 그리기 패키지를 맵 데이터로 변환할 수 있습니다. 그리기 패키지는 [그리기 패키지 요구 사항](drawing-requirements.md)을 따라야 합니다. 하나 이상의 요구 사항이 충족되지 않으면 Conversion 서비스에서 오류 또는 경고를 반환합니다. 이 문서에는 변환 오류 및 경고 코드가 나열되며, 이를 해결하는 방법에 대한 추천 사항이 포함되어 있습니다. 또한 Conversion 서비스에서 이러한 코드를 반환할 수 있는 몇 가지 그리기의 예도 제공합니다.

변환 경고가 있으면 Conversion 서비스가 성공합니다. 그러나 모든 경고를 검토하고 해결하는 것이 좋습니다. 경고는 변환의 일부가 무시되거나 자동으로 수정되었음을 의미합니다. 경고가 해결되지 않으면 후자의 프로세스에서 오류가 발생할 수 있습니다.

## <a name="general-warnings"></a>일반 경고

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*geometryWarning에 대한 설명*

그리기에 잘못된 엔터티가 포함되면 **geometryWarning**이 발생합니다. 잘못된 엔터티는 기하학적 제약 조건을 따르지 않는 엔터티입니다. 잘못된 엔터티의 예로 닫힌 기하 도형만 지원하는 레이어에 자체 교차 다각형 또는 닫히지 않은 폴리라인이 있습니다.

Conversion 서비스는 잘못된 엔터티에서 맵 기능을 만들 수 없으며 대신 이 기능을 무시합니다.

#### <a name="examples-for-geometrywarning"></a>*geometryWarning의 예*

* 아래 두 이미지에서는 자체 교차 다각형의 예를 보여 줍니다.

     ![자체 교차 다각형의 예](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![자체 교차 다각형의 예](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* 다음은 닫히지 않은 폴리라인을 보여주는 이미지입니다. 레이어에서 닫힌 기하 도형만 지원한다고 가정합니다.

    ![닫히지 않은 폴리라인의 예](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*geometryWarning을 해결하는 방법*

각 엔터티에 대해 **geometryWarning**을 검사하여 기하학적 제약 조건을 따르는지 확인합니다.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*unexpectedGeometryInLayer에 대한 설명*

지정된 레이어에 대해 예상되는 기하 도형 유형과 호환되지 않는 기하 도형이 그리기에 포함되면 **unexpectedGeometryInLayer** 경고가 발생합니다. Conversion 서비스에서 **unexpectedGeometryInLayer** 경고를 반환하는 경우 해당 기하 도형이 무시됩니다.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*unexpectedGeometryInLayer의 예*

아래 이미지에서는 닫히지 않은 폴리라인을 보여 줍니다. 레이어에서 닫힌 기하 도형만 지원한다고 가정합니다.

![닫히지 않은 폴리라인의 예](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*unexpectedGeometryInLayer를 해결하는 방법*

각 **unexpectedGeometryInLayer** 경고를 검사하고 호환되지 않는 기하 도형을 호환되는 레이어로 이동합니다. 다른 레이어와 호환되지 않으면 제거해야 합니다.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*unsupportedFeatureRepresentation에 대한 설명*

지원되지 않는 엔터티 형식이 그리기에 포함되면 **unsupportedFeatureRepresentation** 경고가 발생합니다.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*unsupportedFeatureRepresentation의 예*

아래 이미지에서는 레이블 레이어에서 지원되지 않는 엔터티 형식을 여러 줄 텍스트 개체로 보여 줍니다.
  
![레이블 레이어에 있는 여러 줄 텍스트 개체의 예](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*unsupportedFeatureRepresentation을 해결하는 방법*

지원되는 엔터티 형식만 DWG 파일에 포함되어 있는지 확인합니다. 지원되는 형식은 [그리기 패키지 요구 사항 문서의 그리기 파일 요구 사항 섹션](drawing-requirements.md#drawing-package-requirements) 아래에 나와 있습니다.

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*automaticRepairPerformed에 대한 설명*

Conversion 서비스에서 잘못된 기하 도형이 자동으로 복구되면 **automaticRepairPerformed** 경고가 발생합니다.

#### <a name="examples-for-automaticrepairperformed"></a>*automaticRepairPerformed의 예*

* 다음 이미지에서는 Conversion 서비스에서 자체 교차 다각형을 유효한 기하 도형으로 복구한 방법을 보여 줍니다.

    ![복구된 자체 교차 다각형의 예](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* 아래 이미지에서는 Conversion 서비스에서 닫히지 않은 폴리라인의 첫 번째 꼭짓점과 마지막 꼭짓점을 스냅하여 닫힌 폴리라인을 만든 방법을 보여 줍니다. 여기서 첫 번째 및 마지막 꼭짓점은 1mm 미만이었습니다.  

    ![스냅된 폴리라인의 예](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* 아래 이미지에서는 닫힌 폴리라인만 지원하는 레이어에서 Conversion 서비스를 통해 닫히지 않은 여러 폴리라인을 복구한 방법을 보여 줍니다. 닫히지 않은 폴리라인을 삭제하지 않도록 방지하기 위해 서비스에서 하나의 닫힌 폴리라인으로 결합했습니다.

    ![닫히지 않은 폴리라인이 하나의 닫힌 폴리라인으로 결합된 예](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*automaticRepairPerformed를 해결하는 방법*

**automaticRepairPerformed** 경고를 해결하려면 다음 작업을 수행합니다.

1. 각 경고의 기하 도형 및 특정 경고 텍스트를 검사합니다.
2. 자동 복구가 올바른지 확인합니다.
3. 복구가 올바르면 계속합니다. 그렇지 않으면 디자인 파일로 이동하여 경고를 수동으로 해결합니다.

>[!TIP]
>나중에 경고를 표시하지 않으려면 원래 그리기가 복구된 그리기와 일치하도록 원래 그리기를 변경합니다.

## <a name="manifest-warnings"></a>매니페스트 경고

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*redundantAttribution에 대한 설명*

중복되거나 충돌하는 개체 속성이 매니페스트에 포함되면 **redundantAttribution** 경고가 발생합니다.

#### <a name="examples-for-redundantattribution"></a>*redundantAttribution의 예*

* 아래 JSON 코드 조각에는 `name`이 동일한 둘 이상의 `unitProperties` 개체가 포함되어 있습니다.

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* 아래 JSON 코드 조각에서 둘 이상의 `zoneProperties` 개체에는 동일한 `name`이 있습니다.

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*redundantAttribution을 해결하는 방법*

**redundantAttribution* 경고를 해결하려면 중복되거나 충돌하는 개체 속성을 제거합니다.

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*manifestWarning에 대한 설명*

변환 중에 사용되지 않는 unitProperties 또는 zoneProperties 개체가 매니페스트에 포함되면 **manifestWarning**이 발생합니다.

#### <a name="examples-for-manifestwarning"></a>*manifestWarning의 예*

* 일치하는 레이블이 `unitLabel` 레이어에 없는 `unitProperties` 개체(`unitName` 포함)가 매니페스트에 포함되어 있습니다.

* 일치하는 레이블이 `zoneLabel` 레이어에 없는 `zoneProperties` 개체(`zoneName` 포함)가 매니페스트에 포함되어 있습니다.

#### <a name="how-to-fix-manifestwarning"></a>*manifestWarning을 해결하는 방법*

**manifestWarning**을 해결하려면 매니페스트에서 사용되지 않는 `unitProperties` 또는 `zoneProperties` 개체를 제거하거나 변환 중에 속성 개체가 사용되도록 단위/영역 레이블을 그리기에 추가합니다.

## <a name="wall-warnings"></a>벽 경고

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*wallOutsideLevel에 대한 설명*

수준 윤곽선의 범위 밖에 있는 벽 기하 도형이 그리기에 포함되면 **wallOutsideLevel** 경고가 발생합니다.

#### <a name="example-for-walloutsidelevel"></a>*wallOutsideLevel의 예*

* 아래 이미지에서는 노란색 수준 경계 밖에 있는 빨간색 내부 벽을 보여 줍니다.

    ![수준 경계 외부에 있는 내부 벽의 예](./media/drawing-conversion-error-codes/wall-outside-level.png)

* 다음 이미지에서는 노란색 수준 경계 밖에 있는 빨간색 외부 벽을 보여 줍니다.

    ![수준 경계 외부에 있는 외부 벽의 예](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*wallOutsideLevel을 해결하는 방법*

**wallOutsideLevel** 경고를 해결하려면 모든 벽을 포함하도록 수준 기하 도형을 확장합니다. 또는 수준 경계 내에 맞도록 벽 경계를 수정합니다.

## <a name="unit-warnings"></a>단위 경고

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*unitOutsideLevel에 대한 설명*

수준 윤곽선의 범위 밖에 있는 단위 기하 도형이 그리기에 포함되면 **unitOutsideLevel** 경고가 발생합니다.

#### <a name="example-for-unitoutsidelevel"></a>*unitOutsideLevel의 예*

 다음 이미지에서는 빨간색 단위 기하 도형이 노란색 수준 경계의 범위를 벗어납니다.

 ![수준 경계를 벗어나는 단위의 예](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*unitOutsideLevel을 해결하는 방법*

**unitOutsideLevel** 경고를 해결하려면 모든 단위를 포함하도록 수준 경계를 확장합니다. 또는 수준 경계 내에 맞도록 단위 기하 도형을 수정합니다.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*partiallyOverlappingUnit에 대한 설명*

다른 단위 기하 도형에 부분적으로 겹치는 단위 기하 도형이 그리기에 포함되면 **partiallyOverlappingUnit** 경고가 발생합니다. Conversion 서비스에서 겹치는 단위를 삭제합니다.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*partiallyOverlappingUnit 예제 시나리오*

다음 이미지에서는 겹치는 단위가 빨간색으로 강조 표시되어 있습니다. `UNIT110` 및 `HALLWAY`가 삭제됩니다.

![겹치는 단위의 예](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*partiallyOverlappingUnit을 해결하는 방법*

**partiallyOverlappingUnit** 경고를 해결하려면 부분적으로 겹치는 각 단위를 다른 단위와 겹치지 않도록 다시 그립니다.

## <a name="door-warnings"></a>문 경고

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*doorOutsideLevel에 대한 설명*

수준 기하 도형 범위 밖에 있는 문 기하 도형이 그리기에 포함되면 **doorOutsideLevel** 경고가 발생합니다.

#### <a name="example-for-dooroutsidelevel"></a>*doorOutsideLevel의 예*

다음 이미지에서는 빨간색으로 강조 표시된 문 기하 도형이 노란색 수준 경계와 겹칩니다.

![수준 경계와 겹치는 문의 예](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*doorOutsideLevel을 해결하는 방법*

**doorOutsideLevel** 경고를 해결하려면 수준 경계 내에 있도록 문 기하 도형을 다시 그립니다.

## <a name="zone-warnings"></a>영역 경고

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*zoneWarning에 대한 설명*

레이블이 영역에 없으면 **zoneWarning**이 발생합니다. Conversion 서비스에서 레이블이 없는 영역을 삭제합니다.

#### <a name="example-for-zonewarning"></a>*zoneWarning의 예*

다음 이미지에서는 레이블이 포함되지 않은 영역을 보여 줍니다.

![레이블이 포함되지 않은 영역의 예](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*zoneWarning을 해결하는 방법*

**zoneWarning**을 해결하려면 단일 레이블이 각 영역에 있는지 확인합니다.

## <a name="label-warnings"></a>레이블 경고

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*labelWarning에 대한 설명*

모호하거나 모순되는 레이블 기능이 그리기에 포함되면 **labelWarning**이 발생합니다.

다음 이유 중 하나 이상으로 인해 **labelWarning**이 발생합니다.

* 단위 레이블이 단위에 없습니다.
* 영역 레이블이 영역에 없습니다.
* 영역 레이블이 둘 이상의 영역 내에 있습니다.

#### <a name="example-for-labelwarning"></a>*labelWarning의 예*

다음 이미지에서는 두 영역 내에 있는 레이블을 보여 줍니다.

![두 영역 내에 있는 레이블의 예 ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*labelWarning을 해결하는 방법*

**labelWarning**을 해결하려면 다음을 확인합니다.

* 모든 단위 레이블이 단위 내에 있습니다.
* 모든 영역 레이블이 영역 내에 있습니다.
* 모든 영역 레이블이 하나의 영역에만 있습니다.

## <a name="drawing-package-errors"></a>그리기 패키지 오류

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*invalidArchiveFormat에 대한 설명*

그리기 패키지가 GZIP 또는 7-Zip과 같은 잘못된 보관 파일 형식이면 **invalidArchiveFormat** 오류가 발생합니다. ZIP 보관 파일 형식만 지원됩니다.

ZIP 보관 파일이 비어 있는 경우에도 **invalidArchiveFormat** 오류가 발생합니다.

#### <a name="how-to-fix-invalidarchiveformat"></a>*invalidArchiveFormat을 해결하는 방법*

**invalidArchiveFormat** 오류를 해결하려면 다음을 확인합니다.

* 보관 파일 이름이 _.zip_으로 끝납니다.
* 데이터가 ZIP 보관 파일에 포함되어 있습니다.
* ZIP 보관 파일을 열 수 있습니다.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*invalidUserData에 대한 설명*

Conversion 서비스에서 스토리지의 사용자 데이터 개체를 읽을 수 없으면 **invalidUserData** 오류가 발생합니다.

#### <a name="example-scenario-for-invaliduserdata"></a>*invalidUserData 예제 시나리오*

잘못된 `udid` 매개 변수를 사용하여 그리기 패키지를 업로드하려고 했습니다.

#### <a name="how-to-fix-invaliduserdata"></a>*invalidUserData를 해결하는 방법*

**invalidUserData** 오류를 해결하려면 다음을 확인합니다.

* 올바른 `udid`를 업로드된 패키지에 제공했습니다.
* 그리기 패키지를 업로드하는 데 사용한 Azure Maps 계정에 대해 Azure Maps Creator가 사용하도록 설정되었습니다.
* 그리기 패키지를 업로드하는 데 사용한 Azure Maps 계정에 대한 구독 키가 Conversion 서비스에 대한 API 요청에 포함되어 있습니다.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*dwgError에 대한 설명*

**dwgError**는 업로드된 ZIP 보관 파일에 있는 하나 이상의 DWG 파일과 관련된 문제가 그리기 패키지에 있는 경우 발생합니다.

잘못되었거나 손상되어 열 수 없는 DWG 파일이 그리기 패키지에 포함되면 **dwgError**가 발생합니다.

* DWG 파일이 유효한 AutoCAD DWG 파일 형식 그리기가 아닙니다.
* DWG 파일이 손상되었습니다.
* DWG 파일이 _manifest.json_ 파일에 나열되지만 ZIP 보관 파일에는 없습니다.

#### <a name="how-to-fix-dwgerror"></a>*dwgError를 해결하는 방법*

**dwgError**를 해결하려면 _manifest.json_ 파일을 검사하여 다음을 확인합니다.

* ZIP 보관 파일의 모든 DWG 파일은 유효한 AutoCAD DWG 형식 그리기이며, AutoCAD에서 각 DWG 파일을 하나씩 엽니다. 잘못된 그리기를 모두 제거하거나 수정합니다.
* _manifest.json_의 DWG 파일 목록이 ZIP 보관 파일의 DWG 파일과 일치합니다.

## <a name="manifest-errors"></a>매니페스트 오류

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>invalidJsonFormat에 대한 설명

_manifest.json_ 파일을 읽을 수 없으면 **invalidJsonFormat** 오류가 발생합니다.

JSON 형식 또는 구문 오류로 인해 _manifest.json_file을 읽을 수 없습니다. JSON 형식 및 구문에 대한 자세한 내용은 [JSON(JavaScript Object Notation) 데이터 교환 형식](https://tools.ietf.org/html/rfc7159)을 참조하세요.

#### <a name="how-to-fix-invalidjsonformat"></a>*invalidJsonFormat을 해결하는 방법*

**invalidJsonFormat** 오류를 해결하려면 JSON linter를 사용하여 JSON 오류를 검색하고 해결합니다.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*missingRequiredField에 대한 설명*

필요한 데이터가 _manifest.json_ 파일에 없으면 **missingRequiredField** 오류가 발생합니다.

#### <a name="how-to-fix-missingrequiredfield"></a>*missingRequiredField를 해결하는 방법*

**missingRequiredField** 오류를 해결하려면 필요한 속성이 모두 매니페스트에 포함되어 있는지 확인합니다. 필요한 매니페스트 개체의 전체 목록은 [그리기 패키지 요구 사항의 매니페스트 섹션](drawing-requirements.md#manifest-file-requirements)을 참조하세요.  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*missingManifest에 대한 설명*

_manifest.json_ 파일이 ZIP 보관 파일에 없으면 **missingManifest** 오류가 발생합니다.

다음 이유 중 하나 이상으로 인해 **missingManifest** 오류가 발생합니다.

* _manifest.json_ 파일의 철자가 잘못되었습니다.
* _manifest.json_이 없습니다.
* _manifest.json_이 ZIP 보관 파일의 루트 디렉터리 내에 없습니다.

#### <a name="how-to-fix-missingmanifest"></a>*missingManifest를 해결하는 방법*

**missingManifest** 오류를 해결하려면 _manifest.json_이라는 파일이 ZIP 보관 파일의 루트 수준에 있는지 확인합니다.

### <a name="conflict"></a>**conflict**

#### <a name="description-for-conflict"></a>*conflict에 대한 설명*

충돌하는 정보가 _manifest.json_ 파일에 포함되면 **conflict** 오류가 발생합니다.

#### <a name="example-scenario-for-conflict"></a>*conflict 예제 시나리오*

동일한 수준 서수로 둘 이상의 수준이 정의되면 Conversion 서비스에서 **conflict** 오류를 반환합니다. 다음 JSON 코드 조각에서는 동일한 서수로 정의된 두 수준을 보여 줍니다.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*conflict를 해결하는 방법*

**conflict** 오류를 해결하려면 _manifest.json_을 검사하고 충돌하는 모든 정보를 제거합니다.

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*invalidGeoreference에 대한 설명*

잘못된 지리 좌표(georeference)가 _manifest.json_ 파일에 포함되면 **invalidGeoreference** 오류가 발생합니다.

다음 이유 중 하나 이상으로 인해 **invalidGeoreference** 오류가 발생합니다.

* 사용자가 범위를 벗어난 위도 또는 경도 값을 지리 좌표화(georeferencing)하고 있습니다.
* 사용자가 범위를 벗어난 회전 값을 지리 좌표화하고 있습니다.

#### <a name="example-scenario-for-invalidgeoreference"></a>*invalidGeoreference 예제 시나리오*

아래 JSON 조각에서는 위도가 상한보다 높습니다.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*invalidGeoreference를 해결하는 방법*

**invalidGeoreference** 오류를 해결하려면 지리 좌표화된 값이 범위 내에 있는지 확인합니다.

>[!IMPORTANT]
>GeoJSON에서 좌표 순서는 경도 및 위도 순서입니다. 올바른 순서를 사용하지 않는 경우 범위를 벗어난 위도 또는 경도 값을 실수로 참조할 수 있습니다.

## <a name="wall-errors"></a>벽 오류

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*wallError에 대한 설명*

벽 기능을 만드는 중에 오류가 그리기에 포함되면 **wallError**가 발생합니다.

#### <a name="example-scenario-for-wallerror"></a>*wallError 예제 시나리오*

다음 이미지에서는 모든 단위와 겹치지 않는 벽 기능을 보여 줍니다.

![모든 단위와 겹치지 않는 벽 기능의 예](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*wallError를 해결하는 방법*

**wallError** 오류를 해결하려면 하나 이상의 단위와 겹치도록 벽을 다시 그립니다. 또는 벽과 겹치는 새 단위를 만듭니다.

## <a name="vertical-penetration-errors"></a>수직 침투 오류

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*verticalPenetrationError에 대한 설명*

모호한 수직 침투 기능이 그리기에 포함되면 **verticalPenetrationError**가 발생합니다.

다음 이유 중 하나 이상으로 인해 **verticalPenetrationError**가 발생합니다.

* 그리기에 위 또는 아래 수준에서 겹치지 않는 수직 침투 영역이 있는 수직 침투 영역이 포함되어 있습니다.
* 그리기 패키지에 둘 이상의 수직 침투 기능이 있는 수준이 포함되어 있으며, 이 두 기능은 바로 위 또는 아래의 다른 수준에서 단일 수직 침투 기능과 겹칩니다.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*verticalPenetrationError 예제 시나리오*

다음 이미지에서는 수직 침투 영역이 위 또는 아래 수준에서 겹치지 않는 수직 침투 영역을 보여 줍니다.

![수직 침투 1의 예](./media/drawing-conversion-error-codes/vrt-2.png)

다음 이미지에서는 둘 이상의 수직 침투 영역이 인접한 수준에서 겹치는 수직 침투 영역을 보여 줍니다.

![수직 침투 2의 예](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>verticalPenetrationError를 해결하는 방법

**verticalPenetrationError** 오류를 해결하려면 [그리기 패키지 요구 사항](drawing-requirements.md) 문서에서 수직 침투 기능을 사용하는 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Maps 그리기 오류 시각화 도우미를 사용하는 방법](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [실내 맵용 Creator](creator-indoor-maps.md)