---
title: 템플릿 함수
description: Azure Resource Manager 템플릿에서 값을 검색하고 문자열과 숫자로 작업하며 배포 정보를 검색하는 데 사용하는 함수를 설명합니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: a15e7dfdf01a99cd23b216fafcfb44320a716d16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231291"
---
# <a name="arm-template-functions"></a>ARM 템플릿 함수

이 문서에서는 ARM (Azure Resource Manager) 템플릿에서 사용할 수 있는 모든 기능을 설명 합니다. 템플릿에서 함수를 사용 하는 방법에 대 한 자세한 내용은 [템플릿 구문](template-expressions.md)을 참조 하세요.

함수를 직접 만들려면 [사용자 정의 함수](template-syntax.md#functions)를 참조하세요.

리소스 그룹, 구독, 관리 그룹 또는 테 넌 트에 배포 될 때 대부분의 함수는 동일 하 게 작동 합니다. 일부 함수는 일부 범위에서 사용할 수 없습니다. 아래 목록에 나와 있습니다.

<a id="array" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-functions"></a>배열 함수

리소스 관리자는 배열을 사용 하기 위한 몇 가지 함수를 제공 합니다.

* [배열과](template-functions-array.md#array)
* [사용한](template-functions-array.md#concat)
* [contains](template-functions-array.md#contains)
* [createArray](template-functions-array.md#createarray)
* [비우려면](template-functions-array.md#empty)
* [기본](template-functions-array.md#first)
* [교집합](template-functions-array.md#intersection)
* [최신](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [일별](template-functions-array.md#min)
* [max](template-functions-array.md#max)
* [벗어납니다](template-functions-array.md#range)
* [킵](template-functions-array.md#skip)
* [노트](template-functions-array.md#take)
* [부분](template-functions-array.md#union)

<a id="coalesce" aria-hidden="true" />
<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>비교 함수

Resource Manager는 템플릿에서 비교를 수행하기 위한 몇 가지 함수를 제공합니다.

* [가용](template-functions-comparison.md#coalesce)
* [equals](template-functions-comparison.md#equals)
* [작거나](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>날짜 함수

리소스 관리자는 날짜 작업을 위한 다음 함수를 제공 합니다.

* [dateTimeAdd](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>배포 값 함수

Resource Manager는 템플릿의 섹션에서 값을 가져오고 배포와 관련된 값을 가져오기 위한 다음 함수를 제공합니다.

* [배포가](template-functions-deployment.md#deployment)
* [개발](template-functions-deployment.md#environment)
* [변수의](template-functions-deployment.md#parameters)
* [변수](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>논리 함수

Resource Manager는 논리 조건 사용을 위한 다음 함수를 제공합니다.

* [and](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [나타내지](template-functions-logical.md#not)
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

* [add](template-functions-numeric.md#add)
* [copyIndex](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [float](template-functions-numeric.md#float)
* [int](template-functions-numeric.md#int)
* [일별](template-functions-numeric.md#min)
* [max](template-functions-numeric.md#max)
* [연산을](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [sub](template-functions-numeric.md#sub)

<a id="json" aria-hidden="true" />

## <a name="object-functions"></a>개체 함수

리소스 관리자는 개체 작업을 위한 몇 가지 함수를 제공 합니다.

* [contains](template-functions-object.md#contains)
* [비우려면](template-functions-object.md#empty)
* [교집합](template-functions-object.md#intersection)
* [n](template-functions-object.md#json)
* [length](template-functions-object.md#length)
* [부분](template-functions-object.md#union)

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

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [은](template-functions-resource.md#list)
* [업체가](template-functions-resource.md#providers)
* [참조일](template-functions-resource.md#reference)
* [resourceGroup](template-functions-resource.md#resourcegroup) -리소스 그룹에 대 한 배포에만 사용할 수 있습니다.
* [resourceId](template-functions-resource.md#resourceid) -모든 범위에서 사용할 수 있지만 유효한 매개 변수는 범위에 따라 변경 됩니다.
* [구독](template-functions-resource.md#subscription) -리소스 그룹 또는 구독에 대 한 배포에만 사용할 수 있습니다.
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

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

* [인코딩이](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [사용한](template-functions-string.md#concat)
* [contains](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [비우려면](template-functions-string.md#empty)
* [endsWith](template-functions-string.md#endswith)
* [기본](template-functions-string.md#first)
* [format](template-functions-string.md#format)
* [eid](template-functions-string.md#guid)
* [indexOf](template-functions-string.md#indexof)
* [최신](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [newGuid](template-functions-string.md#newguid)
* [padLeft](template-functions-string.md#padleft)
* [replace](template-functions-string.md#replace)
* [킵](template-functions-string.md#skip)
* [분리할](template-functions-string.md#split)
* [startsWith](template-functions-string.md#startswith)
* [string](template-functions-string.md#string)
* [부분](template-functions-string.md#substring)
* [노트](template-functions-string.md#take)
* [toLower](template-functions-string.md#tolower)
* [toUpper](template-functions-string.md#toupper)
* [trim](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>다음 단계

* ARM 템플릿의 섹션에 대 한 설명은 [arm 템플릿 제작](template-syntax.md) 을 참조 하세요.
* 여러 템플릿을 병합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](linked-templates.md)
* 리소스 형식을 만들 때 지정 된 횟수 만큼 반복 하려면 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](copy-resources.md)를 참조 하세요.
* 만든 템플릿을 배포 하는 방법을 보려면 [ARM 템플릿을 사용 하 여 응용 프로그램 배포](deploy-powershell.md) 를 참조 하세요.
