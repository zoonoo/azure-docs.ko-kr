---
title: Knowledge Exploration Service 명령줄 인터페이스 | Microsoft Docs
description: KES 명령줄 인터페이스를 사용하여 구조화된 데이터에서 인덱스 및 문법 파일을 빌드한 다음, Microsoft Cognitive Services에서 웹 서비스로 배포합니다.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: ffa42ac73b42a8271004d2d45d7a80f3307ef059
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373295"
---
# <a name="command-line-interface"></a>명령줄 인터페이스
KES 명령줄 인터페이스는 구조화된 데이터에서 인덱스 및 문법 파일을 빌드하고 웹 서비스로 배포하는 기능을 제공합니다.  일반적인 구문 `kes.exe <command> <required_args> [<optional_args>]`를 사용합니다.  인수 없이 `kes.exe`를 실행하여 명령 목록을 표시하거나 `kes.exe <command>`를 실행하여 지정된 명령에 사용할 수 있는 인수 목록을 표시할 수 있습니다.  다음은 사용할 수 있는 명령의 목록입니다.
* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>
## <a name="buildindex-command"></a>build_index 명령
**build_index** 명령은 인덱싱할 개체의 데이터 파일 및 스키마 정의 파일의 이진 인덱스 파일을 빌드합니다.  구조화된 쿼리 식을 평가하거나 컴파일된 문법 파일과 함께 자연어 쿼리의 해석을 생성하는 데 결과 인덱스 파일을 사용할 수 있습니다.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| 매개 변수      | 설명               |
|----------------|---------------------------|
| `<schemaFile>` | 입력 스키마 경로 |
| `<dataFile>`   | 입력 데이터 경로   |
| `<indexFile>`  | 출력 인덱스 경로 |
| `--description <description>` | 설명 문자열 |
| `--remote <vmSize>`           | 원격 빌드에 대한 VM의 크기 |

이러한 파일은 로컬 파일 경로 또는 Azure blob에 대한 URL 경로로 지정할 수 있습니다.  스키마 파일은 지원해야 하는 작업뿐만 아니라 인덱싱 중인 개체의 구조를 설명합니다([스키마 형식](SchemaFormat.md) 참조).  데이터 파일은 인덱싱할 개체 및 특성 값을 열거합니다([데이터 형식](DataFormat.md) 참조).  빌드에 성공하면 출력 인덱스 파일에는 원하는 작업을 지원하는 입력 데이터의 압축된 표현이 포함됩니다.  

나중에 **describe_index** 명령을 사용하여 이진 인덱스를 식별하도록 필요에 따라 설명 문자열을 지정할 수 있습니다.  

기본적으로 인덱스는 로컬 컴퓨터를 기반으로 합니다.  Azure 환경 외부에서 로컬 빌드는 최대 10,000개의 개체가 포함된 데이터 파일로 제한됩니다.  --remote 플래그가 지정되어 있는 경우 일시적으로 만든 지정된 크기의 Azure VM에서 인덱스가 빌드됩니다.  이렇게 하면 더 많은 메모리의 Azure VM을 사용하여 큰 인덱스를 효율적으로 빌드할 수 있습니다.  빌드 프로세스를 느리게 만드는 페이징을 방지하려면 RAM 크기가 입력 데이터 파일 크기보다 3배인 VM을 사용하는 것이 좋습니다.  사용 가능한 VM 크기 목록은 [가상 머신 크기](../../../articles/virtual-machines/virtual-machines-windows-sizes.md)를 참조하세요.

> [!TIP] 
> 더 빠른 빌드를 위해 데이터 파일의 개체를 확률 감소 순으로 미리 정렬합니다.

<a name="build_grammar-command"></a>
## <a name="buildgrammar-command"></a>build_grammar 명령
**build_grammar** 명령은 XML에 지정된 문법을 이진 문법 파일로 컴파일합니다.  결과 문법 파일을 인덱스 파일과 함께 사용하여 자연어 쿼리의 해석을 생성할 수 있습니다.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| 매개 변수       | 설명               |
|-----------------|---------------------------|
| `<xmlFile>`     | 입력 XML 문법 사양 경로 |
| `<grammarFile>` | 출력 컴파일된 문법 경로         |

이러한 파일은 로컬 파일 경로 또는 Azure blob에 대한 URL 경로로 지정할 수 있습니다.  문법 사양은 가중치가 적용된 자연어 식 및 해당 의미 체계 해석의 집합을 설명합니다([문법 형식](GrammarFormat.md) 참조).  빌드에 성공하면 출력 문법 파일에 빠른 디코딩을 가능하게 하는 문법 사양의 이진 표현이 포함됩니다.

<a name="host_service-command"/>
## <a name="hostservice-command"></a>host_service 명령
**host_service** 명령은 로컬 컴퓨터에서 KES 서비스의 인스턴스를 호스팅합니다.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| 매개 변수       | 설명                |
|-----------------|----------------------------|
| `<grammarFile>` | 입력 이진 문법 경로         |
| `<indexFile>`   | 입력 이진 인덱스 경로           |
| `--port <port>` | 로컬 포트 번호.  기본값: 8000 |

이러한 파일은 로컬 파일 경로 또는 Azure blob에 대한 URL 경로로 지정할 수 있습니다.  웹 서비스는 http://localhost:&lt; port&gt;/에서 호스팅됩니다.  지원되는 목록은 [Web API](WebAPI.md)를 참조하세요.

Azure 환경 외부에서 로컬로 호스팅된 서비스는 최대 1MB 크기, 초당 10개 요청 및 총 1000개의 호출의 인덱스 파일로 제한됩니다.  이러한 제한을 해결하려면 Azure VM 내에서 **host_service**를 실행하거나 **deploy_service**를 사용하여 Azure 클라우드 서비스에 배포합니다.

<a name="deploy_service-command"/>
## <a name="deployservice-command"></a>deploy_service 명령
**deploy_service** 명령은 KES 서비스의 인스턴스를 Azure 클라우드 서비스에 배포합니다.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| 매개 변수       | 설명                  |
|-----------------|------------------------------|
| `<grammarFile>` | 입력 이진 문법 경로           |
| `<indexFile>`   | 입력 이진 인덱스 경로             |
| `<serviceName>` | 대상 클라우드 서비스의 이름 |
| `<vmSize>`      | 클라우드 서비스 VM의 크기     |
| `--slot <slot>` | 클라우드 서비스 슬롯: “staging”(기본값), “production” |

이러한 파일은 로컬 파일 경로 또는 Azure blob에 대한 URL 경로로 지정할 수 있습니다.  서비스 이름은 미리 구성된 Azure 클라우드 서비스를 지정합니다([클라우드 서비스 만들기 및 배포 방법](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md) 참조).  명령은 자동으로 지정된 크기의 VM을 사용하여 지정된 Azure 클라우드 서비스에 KES 서비스를 배포합니다.  성능을 크게 저하시키는 페이징을 방지하려면 입력 인덱스 파일 크기보다 1GB 이상의 RAM이 더 추가된 VM을 사용하는 것이 좋습니다.  사용 가능한 VM 크기 목록은 [Cloud Services에 적합한 크기](../../../articles/cloud-services/cloud-services-sizes-specs.md)를 참조하세요.

기본적으로 서비스는 스테이징 환경에 배포되며 --slot 매개 변수를 통해 필요에 따라 재정의됩니다.  지원되는 목록은 [Web API](WebAPI.md)를 참조하세요.

<a name="describe_index-command"/>
## <a name="describeindex-command"></a>describe_index 명령
**describe_index** 명령은 스키마 및 설명을 비롯한 인덱스 파일에 대한 정보를 출력합니다.

`kes.exe describe_index <indexFile>`

| 매개 변수     | 설명      |
|---------------|------------------|
| `<indexFile>` | 입력 인덱스 경로 |

이 파일은 Azure blob에 대한 로컬 파일 경로 또는 URL 경로로 지정될 수 있습니다.  출력 설명 문자열은 **build_index** 명령의 --description 매개 변수를 사용하여 지정할 수 있습니다.

<a name="describe_grammar-command"/>
## <a name="describegrammar-command"></a>describe_grammar 명령
**describe_grammar** 명령은 이진 문법을 빌드하는 데 사용되는 원래 문법 사양을 출력합니다.

`kes.exe describe_grammar <grammarFile>`

| 매개 변수       | 설명      |
|-----------------|------------------|
| `<grammarFile>` | 입력 문법 경로 |

이 파일은 Azure blob에 대한 로컬 파일 경로 또는 URL 경로로 지정될 수 있습니다.

