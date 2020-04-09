---
title: 템플릿 함수
description: Azure Resource Manager 템플릿에서 값을 검색하고 문자열과 숫자로 작업하며 배포 정보를 검색하는 데 사용하는 함수를 설명합니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: fbd82f89ed9a97a3f376a9ed6eaa8ae3760759ff
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982381"
---
# <a name="arm-template-functions"></a>ARM 템플릿 기능

이 문서에서는 ARM(Azure 리소스 관리자) 템플릿에서 사용할 수 있는 모든 기능에 대해 설명합니다. 템플릿의 함수 사용에 대한 자세한 내용은 [템플릿 구문을](template-expressions.md)참조하십시오.

함수를 직접 만들려면 [사용자 정의 함수](template-syntax.md#functions)를 참조하세요.

대부분의 함수는 리소스 그룹, 구독, 관리 그룹 또는 테넌트에 배포될 때 동일하게 작동합니다. 일부 함수는 일부 범위에서 사용할 수 없습니다. 그들은 아래 의 목록에 기록되어 있습니다.

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>배열 및 개체 함수

Resource Manager는 배열 및 개체 작업을 위한 여러 함수를 제공합니다.

* [배열](template-functions-array.md#array)
* [합체](template-functions-array.md#coalesce)
* [Concat](template-functions-array.md#concat)
* [포함](template-functions-array.md#contains)
* [create배열](template-functions-array.md#createarray)
* [빈](template-functions-array.md#empty)
* [첫 번째](template-functions-array.md#first)
* [교차로](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [마지막](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [분](template-functions-array.md#min)
* [최대](template-functions-array.md#max)
* [범위](template-functions-array.md#range)
* [건너뛸](template-functions-array.md#skip)
* [테이크](template-functions-array.md#take)
* [연합](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>비교 함수

Resource Manager는 템플릿에서 비교를 수행하기 위한 몇 가지 함수를 제공합니다.

* [equals](template-functions-comparison.md#equals)
* [덜](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>날짜 함수

리소스 관리자는 날짜 작업에 대해 다음과 같은 기능을 제공합니다.

* [날짜시간 추가](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>배포 값 함수

Resource Manager는 템플릿의 섹션에서 값을 가져오고 배포와 관련된 값을 가져오기 위한 다음 함수를 제공합니다.

* [배포](template-functions-deployment.md#deployment)
* [환경](template-functions-deployment.md#environment)
* [매개 변수](template-functions-deployment.md#parameters)
* [변수](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>논리 함수

Resource Manager는 논리 조건 사용을 위한 다음 함수를 제공합니다.

* [및](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [하지 않습니다.](template-functions-logical.md#not)
* [또는](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>숫자 함수

Resource Manager는 정수 작업을 위한 다음 함수를 제공합니다.

* [추가](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [플 로트](template-functions-numeric.md#float)
* [Int](template-functions-numeric.md#int)
* [분](template-functions-numeric.md#min)
* [최대](template-functions-numeric.md#max)
* [모드](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [sub](template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>리소스 함수

Resource Manager는 리소스 값을 가져오기 위한 다음 함수를 제공합니다.

* [확장리소스 ID](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [목록 키](template-functions-resource.md#listkeys)
* [목록비밀](template-functions-resource.md#list)
* [list*](template-functions-resource.md#list)
* [providers](template-functions-resource.md#providers)
* [참조](template-functions-resource.md#reference)
* [리소스 그룹](template-functions-resource.md#resourcegroup) - 리소스 그룹에 배포에서만 사용할 수 있습니다.
* [resourceId](template-functions-resource.md#resourceid) - 모든 범위에서 사용할 수 있지만 유효한 매개 변수는 범위에 따라 변경됩니다.
* [구독](template-functions-resource.md#subscription) - 리소스 그룹 또는 구독에 대한 배포에서만 사용할 수 있습니다.
* [구독리소스 ID](template-functions-resource.md#subscriptionresourceid)
* [테넌트리소스ID](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>문자열 함수

Resource Manager는 문자열 작업을 위한 다음 함수를 제공합니다.

* [베이스64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [포함](template-functions-string.md#contains)
* [데이터우리](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [빈](template-functions-string.md#empty)
* [Endswith](template-functions-string.md#endswith)
* [첫 번째](template-functions-string.md#first)
* [형식](template-functions-string.md#format)
* [Guid](template-functions-string.md#guid)
* [Indexof](template-functions-string.md#indexof)
* [마지막](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [뉴가드](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [교체](template-functions-string.md#replace)
* [건너뛸](template-functions-string.md#skip)
* [분할](template-functions-string.md#split)
* [시작](template-functions-string.md#startswith)
* [string](template-functions-string.md#string)
* [부분](template-functions-string.md#substring)
* [테이크](template-functions-string.md#take)
* [로어](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [트림](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대한 설명은 [ARM 템플릿 작성을 참조하세요.](template-syntax.md)
* 여러 템플릿을 병합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](linked-templates.md)
* 리소스 형식을 만들 때 지정된 횟수를 반복하려면 [Azure 리소스 관리자에서 여러 리소스 인스턴스 만들기를](copy-resources.md)참조하십시오.
* 만든 템플릿을 배포하는 방법을 보려면 [ARM 템플릿을 사용하여 응용 프로그램 배포를 참조하세요.](deploy-powershell.md)
