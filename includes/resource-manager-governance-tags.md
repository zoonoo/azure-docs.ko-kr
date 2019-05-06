---
title: 포함 파일
description: 포함 파일
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c426df2293cfb2d8ba4dc02e8fc5519c3d822168
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868789"
---
메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 분류로 논리적으로 구성합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

태그를 적용한 후에는 구독에서 태그 이름과 값으로 모든 리소스를 검색할 수 있습니다. 태그를 사용하면 서로 다른 리소스 그룹에서 관련 리소스를 검색할 수 있습니다. 이 방법은 청구 또는 관리에 대한 리소스를 구성해야 하는 경우에 유용할 수 있습니다.

분류는 셀프 서비스 메타데이터 태그 지정 전략 및 사용자의 부담을 줄이고 정확도를 높이는 자동 태그 지정 전략을 고려해야 합니다.

다음 제한 사항이 태그에 적용됩니다.

* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](../articles/azure-resource-manager/tag-support.md)을 참조하세요.
* 각 리소스 또는 리소스 그룹에는 최대 15개의 태그 이름/값 쌍이 포함될 수 있습니다. 이 제한은 리소스 그룹 또는 리소스에 직접 적용되는 태그에만 적용됩니다. 리소스 그룹은 각각 15개의 태그 이름/값 쌍이 있는 여러 리소스를 포함할 수 있습니다. 리소스와 연결해야 하는 값이 15보다 많은 경우 태그 값에 JSON 문자열을 사용합니다. JSON 문자열은 단일 태그 이름에 적용되는 여러 값을 포함할 수 있습니다. 이 문서는 태그에 JSON 문자열을 할당하는 예제를 보여 줍니다.
* 태그 이름은 512자로 제한되며 태그 값은 256자로 제한됩니다. 저장소 계정에서 태그 이름은 128자로 제한되며 태그 값은 256자로 제한됩니다.
* Virtual Machines 및 Virtual Machine Scale Sets는 총 길이 2048 자 모든 태그 이름 및 값에 대 한 제한 됩니다.
* 태그는 해당 리소스 그룹의 리소스에 의해 상속되지 않은 리소스 그룹에 적용됩니다.
* Cloud Services와 같은 클래식 리소스에는 태그를 적용할 수 없습니다.
* 태그 이름에는 다음 문자를 포함할 수 없습니다: `<`, `>`, `%`, `&`, `\`, `?`, `/`
