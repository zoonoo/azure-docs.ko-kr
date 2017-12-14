---
title: "PowerShell 커넥터 | Microsoft Docs"
description: "이 문서에서는 Microsoft의 Windows PowerShell 커넥터를 구성하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 6dba8e34-a874-4ff0-90bc-bd2b0a4199b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 27ca89a2032c82a8be909349b38a64fc6aa9579e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="windows-powershell-connector-technical-reference"></a>WIndows PowerShell 커넥터 기술 참조
이 문서에서는 Windows PowerShell 커넥터를 설명합니다. 이 문서는 다음 제품에 적용됩니다.

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * 핫픽스 4.1.3671.0 이상 [KB3092178](https://support.microsoft.com/kb/3092178)을 사용해야 합니다.

MIM2016와 FIM2010R2의 경우 커넥터는 [Microsoft 다운로드 센터](http://go.microsoft.com/fwlink/?LinkId=717495)에서 다운로드로 사용할 수 있습니다.

## <a name="overview-of-the-powershell-connector"></a>PowerShell 커넥터의 개요
PowerShell 커넥터를 사용하면 동기화 서비스를 Windows PowerShell 기반 API를 제공하는 외부 시스템과 통합할 수 있습니다. 커넥터는 호출 기반 ECMA2(extensible connectivity management agent 2) 프레임 워크 및 Windows PowerShell의 기능 간에 브리지를 제공합니다. ECMA 프레임 워크에 대한 자세한 내용은 [Extensible Connectivity 2.2 관리 에이전트 참조](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx)를 참조하세요.

### <a name="prerequisites"></a>필수 조건
커넥터를 사용하기 전에 동기화 서버에 다음 사항이 있는지 확인합니다.

* Microsoft.NET 4.5.2 Framework 이상
* Windows PowerShell 2.0, 3.0 또는 4.0

동기화 서비스 서버의 실행 정책은 커넥터가 Windows PowerShell 스크립트를 실행하도록 구성되어야 합니다. 커넥터가 실행될 스크립트가 디지털로 서명되지 않으면 이 명령을 실행하여 실행 정책을 구성합니다.  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>새 커넥터 만들기
동기화 서비스에서 Windows PowerShell 커넥터를 만들려면 동기화 서비스에서 요청하는 단계를 실행하는 일련의 Windows PowerShell 스크립트를 제공해야 합니다. 데이터 원본에 따라 필요한 기능에 연결하고 구현해야 하는 스크립트가 달라집니다. 이 섹션에서는 구현할 수 있는 스크립트와 필요할 때를 각각 간략하게 설명합니다.

Windows PowerShell 커넥터는 동기화 서비스 데이터베이스 내에서 스크립트를 각각 저장하도록 설계됩니다. 파일 시스템에 저장된 스크립트를 실행할 수 있지만 각 스크립트의 본문을 직접 커넥터의 구성에 삽입하는 것이 더 용이합니다.

PowerShell 커넥터를 만들려면 **동기화 서비스**에서 **관리 에이전트** 및 **만들기**를 선택합니다. **PowerShell(Microsoft)** 커넥터를 선택합니다.

![커넥터 만들기](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>연결
원격 시스템에 연결하기 위한 구성 매개 변수를 제공합니다. 이러한 값은 동기화 서비스에 의해 안전하게 저장되고 커넥터를 실행할 때 Windows PowerShell 스크립트를 사용할 수 있도록 만들어 집니다.

![연결](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

다음 연결 매개 변수를 구성할 수 있습니다.

**연결**

| 매개 변수 | 기본값 | 목적 |
| --- | --- | --- |
| 서버 |<Blank> |커넥터가 연결해야 하는 서버 이름입니다. |
| 도메인 |<Blank> |커넥터를 실행할 때 사용하기 위해 저장하는 자격 증명의 도메인입니다. |
| 사용자 |<Blank> |커넥터를 실행할 때 사용하기 위해 저장하는 자격 증명의 사용자 이름입니다. |
| 암호 |<Blank> |커넥터를 실행할 때 사용하기 위해 저장하는 자격 증명의 암호입니다. |
| 커넥터 계정 가장 |False |true인 경우 동기화 서비스는 제공된 자격 증명의 컨텍스트에서 Windows PowerShell 스크립트를 실행합니다. 가능하면 각 스크립트에 전달된 **$Credentials** 매개 변수가 가장 대신 사용되는 것이 좋습니다. 이 옵션을 사용하는 데 필요한 추가 사용 권한에 대한 자세한 내용은 [가장에 대한 추가 구성](#additional-configuration-for-impersonation)을 참조하세요. |
| 가장 시 사용자 프로필 로드 |False |가장하는 동안 Windows가 커넥터의 자격 증명에서 사용자 프로필을 로드하도록 지시합니다. 가장된 사용자에게 로밍 프로필이 있는 경우 커넥터는 로밍 프로필을 로드하지 않습니다. 이 매개 변수를 사용하는 데 필요한 추가 사용 권한에 대한 자세한 내용은 [가장에 대한 추가 구성](#additional-configuration-for-impersonation)을 참조하세요. |
| 가장 시 로그온 형식 |없음 |가장하는 동안 로그온 형식입니다. 자세한 내용은 [dwLogonType][dw] 설명서를 참조하세요. |
| 서명된 스크립트만 해당 |False |true인 경우 Windows PowerShell 커넥터는 각 스크립트에 유효한 디지털 서명이 있는지 확인합니다. false인 경우 동기화 서비스 서버의 Windows PowerShell 실행 정책이 RemoteSigned 또는 무제한인지 확인합니다. |

**일반 모듈**  
커넥터를 사용하면 구성에 공유 Windows PowerShell 모듈을 저장할 수 있습니다. 커넥터가 스크립트를 실행할 때 Windows PowerShell 모듈을 파일 시스템에 추출하므로 각 스크립트에서 가져올 수 있습니다.

가져오기, 내보내기 및 암호 동기화 스크립트의 경우 일반 모듈은 커넥터의 MAData 폴더에 추출됩니다. 스키마, 유효성 검사, 계층 및 파티션 검색 스크립트의 경우 일반 모듈은 %TEMP% 폴더에 추출됩니다. 두 경우 모두 압축된 일반 모듈 스크립트는 일반 모듈 스크립트 이름 설정에 따라 이름이 지정됩니다.

MAData 폴더에서 FIMPowerShellConnectorModule.psm1이라는 모듈을 로드하려면 다음 문을 사용합니다. `Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

%TEMP% 폴더에서 FIMPowerShellConnectorModule.psm1이라는 모듈을 로드하려면 다음 문을 사용합니다. `Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**매개 변수 유효성 검사**  
유효성 검사 스크립트는 관리자가 제공한 커넥터 구성 매개 변수가 유효한지 확인하는 데 사용될 수 있는 선택적 Windows PowerShell 스크립트입니다. 서버, 연결 자격 증명 및 연결 매개 변수의 유효성을 검사하는 작업은 유효성 검사 스크립트의 일반적인 사용입니다. 유효성 검사 스크립트는 다음과 같은 탭 뒤에 호출되고 대화 상자에서 수정됩니다.

* 연결
* 글로벌 매개 변수
* 파티션 구성

유효성 검사 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameterPage |[ConfigParameterPage][cpp] |유효성 검사 요청을 트리거한 구성 탭 또는 대화 상자입니다. |
| ConfigParameters |[KeyedCollection][keyk] [string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |

유효성 검사 스크립트는 단일 ParameterValidationResult 개체를 파이프라인에 반환해야 합니다.

**스키마 검색**  
스키마 검색 스크립트는 필수입니다. 이 스크립트는 특성 흐름 규칙을 구성할 때 동기화 서비스에서 사용할 개체 형식, 특성 및 특성 제약 조건을 반환합니다. 스키마 검색 스크립트는 커넥터를 만드는 동안 실행되고 커넥터의 스키마로 채워집니다. 또한 동기화 서비스 관리자에서 스키마 새로 고침 작업에서 사용됩니다.

스키마 검색 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk] [string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |

스크립트는 단일 [스키마][schema] 개체를 파이프라인에 반환해야 합니다. 스키마 개체는 개체 형식(예: 사용자 및 그룹)을 나타내는 [SchemaType][schemaT] 개체로 구성됩니다. SchemaType 개체는 형식의 특성(예: 지정된 이름, 성 및 우편 주소)을 나타내는 [SchemaAttribute][schemaA] 개체의 컬렉션을 보유합니다.

**추가 매개 변수**  
표준 구성 설정 외에도 커넥터의 인스턴스에 지정된 추가 사용자 지정 구성 설정을 정의할 수 있습니다. 이러한 매개 변수는 파티션, 커넥터에서 지정되거나 단계 수준을 실행하고 관련 Windows PowerShell 스크립트에서 액세스할 수 있습니다. 사용자 지정 구성 설정은 일반 텍스트 형식으로 동기화 서비스 데이터베이스에 저장하거나 암호화될 수 있습니다. 동기화 서비스는 자동으로 필요할 때 안전한 구성 설정을 암호화 및 해독합니다.

사용자 지정 구성 설정을 지정하려면 각 매개 변수의 이름을 쉼표(,)로 구분합니다.

스크립트에서 사용자 지정 구성 설정에 액세스하려면 밑줄( \_ ) 및 매개 변수의 범위(전역, 파티션 또는 RunStep)를 사용하여 접미사 이름을 지정해야 합니다. 예를 들어 전역 FileName 매개 변수에 액세스하려면 다음 코드 조각을 사용합니다. `$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>기능
관리 에이전트 설계자의 기능 탭은 커넥터의 동작 및 기능을 정의합니다. 커넥터를 만들 때 이 탭에서 선택한 내용을 수정할 수 없습니다. 이 테이블은 기능 설정을 나열합니다.

![기능](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| 기능 | 설명 |
| --- | --- |
| [고유 이름 스타일][dnstyle] |커넥터가 고유 이름을 지원하는지, 그렇다면 어떤 스타일을 지원하는지를 나타냅니다. |
| [내보내기 형식][exportT] |내보내기 스크립트에 표시되는 개체의 형식을 결정합니다. <li>AttributeReplace – 특성이 변경될 때 다중값 특성에 대한 값의 전체 집합을 포함합니다.</li><li>AttributeUpdate – 특성이 변경될 때 다중값 특성의 델타만 포함합니다.</li><li>MultivaluedReferenceAttributeUpdate - 비참조 다중값 특성에 대한 값의 전체 집합과 다중값 참조 특성에 대한 델타만 포함합니다.</li><li>ObjectReplace – 특성을 변경하는 경우 개체에 대한 모든 특성을 포함합니다.</li> |
| [데이터 정규화][DataNorm] |스크립트에 제공되기 전에 앵커 특성을 정규화하도록 동기화 서비스에 지시합니다. |
| [개체 확인][oconf] |동기화 서비스에서 보류 중인 가져오기 동작을 구성합니다. <li>Normal – 가져오기를 통해 모든 내보낸 변경 내용을 확인하는 기본 동작</li><li>NoDeleteConfirmation – 개체를 삭제하면 생성 보류 중인 가져오기 작업이 없습니다.</li><li>NoAddAndDeleteConfirmation – 개체를 생성하거나 삭제하면 생성 보류 중인 가져오기 작업이 없습니다.</li> |
| 앵커로 DN 사용 |또한 고유 이름 스타일이 LDAP로 설정되면 커넥터 공간에 대한 앵커 특성은 고유 이름입니다. |
| 여러 커넥터의 동시 작업 |옵션을 선택하면 여러 Windows PowerShell 커넥터는 동시에 실행할 수 있습니다. |
| 파티션 |옵션을 선택하면 커넥터는 여러 파티션 및 파티션 검색을 지원합니다. |
| 계층 구조 |옵션을 선택하면 커넥터는 LDAP 스타일 계층 구조를 지원합니다. |
| 가져오기 사용 |옵션을 선택하면 커넥터는 가져오기 스크립트를 통해 데이터를 가져옵니다. |
| 델타 가져오기 사용 |옵션을 선택하면 커넥터는 가져오기 스크립트를 통해 델타를 요청할 수 있습니다. |
| 내보내기 사용 |옵션을 선택하면 커넥터는 내보내기 스크립트를 통해 데이터를 내보냅니다. |
| 전체 내보내기 사용 |옵션을 선택하면 내보내기 스크립트는 전체 커넥터 공간을 내보내도록 지원합니다. 또한 이 옵션을 사용하려면 내보내기 사용을 선택해야 합니다. |
| 첫 번째 내보내기 단계에서 참조 값 없음 |옵션을 선택하면 두 번째 내보내기 과정에서 참조 특성을 내보냅니다. |
| 개체 이름 바꾸기 사용 |옵션을 선택하면 고유 이름을 수정할 수 있습니다. |
| 바꾸기로 삭제-추가 |옵션을 선택하면 삭제-추가 작업이 단일 교체로 내보내집니다. |
| 암호 작업 사용 |옵션을 선택하면 암호 동기화 스크립트가 지원됩니다. |
| 첫 번째 패스에서 암호 내보내기 사용 |옵션을 선택하면 개체를 만들 때 프로비전하는 동안 암호 설정이 내보내집니다. |

### <a name="global-parameters"></a>글로벌 매개 변수
관리 에이전트 디자이너에서 전역 매개 변수 탭을 통해 커넥터를 통해 실행되는 Windows PowerShell 스크립트를 구성할 수 있습니다. 연결 탭에 정의된 사용자 지정 구성 설정에 대한 전역 값을 구성할 수도 있습니다.

**파티션 검색**  
파티션은 하나의 공유 스키마 내에서 별도의 네임스페이스입니다. 예를 들어 Active Directory에서 모든 도메인은 한 포리스트 내의 파티션입니다. 파티션은 작업 가져오기 및 내보내기에 대한 논리적 그룹화입니다. 가져오기 및 내보내기에는 파티션이 컨텍스트로 존재하고 모든 작업은 이 컨텍스트에서 발생합니다. 파티션은 LDAP의 계층 구조를 나타내야 합니다. 파티션의 고유 이름은 파티션의 범위 내에서 반환된 개체가 모두 있는지 확인하기 위해 가져오기에 사용됩니다. 파티션 고유 이름은 메타버스에서 커넥터 공간까지 프로비전하는 동안 사용되어 내보내는 동안 연결되어야 하는 파티션 개체를 결정합니다.

파티션 검색 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |

스크립트는 단일 [파티션][part] 개체 또는 파티션 개체의 목록[T]를 파이프라인에 반환해야 합니다.

**계층 구조 검색**  
계층 구조 검색 스크립트는 고유 이름 스타일 기능이 LDAP인 경우에 사용됩니다. 가져오기 및 내보내기 작업에 대한 범위 내외에서 고려하는 일련의 컨테이너를 찾고 선택하는 데 스크립트를 사용할 수 있습니다. 스크립트는 스크립트에 제공된 루트 노드의 직접 하위 항목인 노드의 목록을 제공해야 합니다.

계층 구조 검색 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |
| ParentNode |[HierarchyNode][hn] |스크립트에서 계층의 루트 노드는 직접 하위 항목을 반환해야 합니다. |

스크립트는 단일 하위 HierarchyNode 개체 또는 하위 HierarchyNode 개체의 목록[T]를 파이프라인에 반환해야 합니다.

#### <a name="import"></a>가져오기
가져오기 작업을 지원하는 커넥터는 세 가지 스크립트를 구현해야 합니다.

**가져오기 시작**  
가져오기 시작 스크립트는 가져오기 실행 단계의 시작 부분에서 실행됩니다. 이 단계에서는 원본 시스템에 대한 연결을 설정하고 연결된 시스템에서 데이터를 가져오기 전에 준비 단계를 수행할 수 있습니다.

가져오기 시작 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |가져오기 실행(델타 또는 전체), 파티션, 계층, 워터 마크 및 예상된 페이지 크기의 형식에 대한 스크립트를 알립니다. |
| 형식 |[Schema][schema] |가져올 커넥터 공간에 대한 스키마입니다. |

스크립트는 단일 [OpenImportConnectionResults][oicres] 개체를 파이프라인에 반환해야 합니다. 예: `Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**데이터 가져오기**  
스크립트가 더 이상 가져올 데이터가 없다고 나타낼 때까지 데이터 가져오기 스크립트는 커넥터에서 호출됩니다. Windows PowerShell 커넥터에는 9,999개의 개체를 가진 페이지 크기가 있습니다. 스크립트가 가져오기에 9,999개 이상의 개체를 반환하면 페이징을 지원해야 합니다. 커넥터가 저장소에 사용할 수 있는 사용자 지정 데이터 속성을 노출하므로 데이터 가져오기 스크립트가 호출될 때마다 스크립트는 중단된 위치에서 개체를 다시 가져오기 시작합니다.

데이터 가져오기 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |
| GetImportEntriesRunStep |[ImportRunStep][irs] |페이징 가져오기 및 델타 가져오기를 수행하는 동안 사용될 수 있는 워터 마크(CustomData)를 보유합니다. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |가져오기 실행(델타 또는 전체), 파티션, 계층, 워터 마크 및 예상된 페이지 크기의 형식에 대한 스크립트를 알립니다. |
| 형식 |[Schema][schema] |가져올 커넥터 공간에 대한 스키마입니다. |

데이터 가져오기 스크립트는 목록[[CSEntryChange][csec]] 개체를 파이프라인에 작성해야 합니다. 이 컬렉션은 가져오는 각 개체를 나타내는 CSEntryChange 특성으로 구성됩니다. 전체 가져오기를 실행하는 동안 이 컬렉션에는 모든 개체에 대한 속성을 모두 가진 CSEntryChange 개체의 전체 집합이 있어야 합니다. 델타를 가져오는 동안 CSEntryChange 개체는 가져올 각 개체 또는 변경된(대체 모드) 개체의 전체 표시에 대한 특성 수준 델타 중에 하나를 포함해야 합니다.

**최종 가져오기**  
가져오기 실행을 끝낼 때 최종 가져오기 스크립트가 실행됩니다. 이 스크립트는 필요한 정리 작업을 수행합니다(예: 시스템에 대한 연결 닫기 및 오류에 대한 응답).

최종 가져오기 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |가져오기 실행(델타 또는 전체), 파티션, 계층, 워터 마크 및 예상된 페이지 크기의 형식에 대한 스크립트를 알립니다. |
| CloseImportConnectionRunStep |[CloseImportConnectionRunStep][cecrs] |가져오기가 종료된 이유에 대한 스크립트를 알립니다. |

스크립트는 단일 [CloseImportConnectionResults][cicres] 개체를 파이프라인으로 반환해야 합니다. 예: `Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>내보내기
커넥터의 가져오기 아키텍처와 동일하게 내보내기를 지원하는 커넥터는 세 가지 스크립트 구현해야 합니다.

**내보내기 시작**  
내보내기 시작 스크립트는 내보내기 실행 단계의 시작 부분에서 실행됩니다. 이 단계에서는 원본 시스템에 대한 연결을 설정할 수 있으며 연결된 시스템에 데이터를 내보내기 전에 모든 준비 단계를 수행할 수 있습니다.

내보내기 시작 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |내보내기 실행(델타 또는 전체), 파티션, 계층 및 예상된 페이지 크기의 형식에 대한 스크립트를 알립니다. |
| 형식 |[Schema][schema] |내보낼 커넥터 공간에 대한 스키마입니다. |

스크립트는 파이프라인에 출력을 반환해야 합니다.

**데이터 내보내기**  
동기화 서비스는 보류 중인 내보내기를 모두 처리하는 데 필요한 횟수 만큼 데이터 내보내기 스크립트를 호출합니다. 커넥터 공간에 커넥터의 페이지 크기보다 많은 보류 중인 내보내기가 있는 경우 데이터 내보내기 스크립트는 동일한 개체에서 가능한 여러 번 호출될 수 있습니다.

데이터 내보내기 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |
| CSEntries |IList[CSEntryChange][csec] |이 단계 동안 보류 중인 내보내기를 통해 모든 커넥터 공간 개체의 목록을 처리합니다. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |내보내기 실행(델타 또는 전체), 파티션, 계층 및 예상된 페이지 크기의 형식에 대한 스크립트를 알립니다. |
| 형식 |[Schema][schema] |내보낼 커넥터 공간에 대한 스키마입니다. |

데이터 내보내기 스크립트는 [PutExportEntriesResults][peeres] 개체를 파이프라인에 반환해야 합니다. 앵커 특성에 대한 오류 또는 변경이 발생하지 않으면 이 개체는 내보낸 각 커넥터에 대한 결과 정보를 포함할 필요가 없습니다. 예를 들어, PutExportEntriesResults 개체를 파이프라인에 반환하려면: `Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**최종 내보내기**  
내보내기 실행을 끝낼 때 최종 내보내기 스크립트가 실행됩니다. 이 스크립트는 필요한 정리 작업을 수행합니다(예: 시스템에 대한 연결 닫기 및 오류에 대한 응답).

최종 내보내기 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |내보내기 실행(델타 또는 전체), 파티션, 계층 및 예상된 페이지 크기의 형식에 대한 스크립트를 알립니다. |
| CloseExportConnectionRunStep |[CloseExportConnectionRunStep][cecrs] |내보내기가 종료된 이유에 대한 스크립트를 알립니다. |

스크립트는 파이프라인에 출력을 반환해야 합니다.

#### <a name="password-synchronization"></a>암호 동기화
Windows PowerShell 커넥터는 암호 변경/재설정에 대한 대상으로 사용될 수 있습니다.

암호 스크립트는 커넥터에서 다음 매개 변수를 받습니다.

| Name | 데이터 형식 | 설명 |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |커넥터에 대한 구성 매개 변수의 테이블입니다. |
| 자격 증명 |[PSCredential][pscred] |연결 탭에서 관리자가 입력한 자격 증명을 포함합니다. |
| 파티션 |[파티션][part] |CSEntry가 위치한 디렉터리 파티션입니다. |
| CSEntry |[CSEntry][cse] |암호 변경 또는 재설정을 받는 개체에 대한 커넥터 공간 항목입니다. |
| OperationType |문자열 |작업이 다시 설정(**SetPassword**)인지 또는 변경(**ChangePassword**)인지를 나타냅니다. |
| PasswordOptions |[PasswordOptions][pwdopt] |의도된 암호 재설정 동작을 지정하는 플래그입니다. 이 매개 변수는 OperationType이 **SetPassword**인 경우에만 사용할 수 있습니다. |
| OldPassword |문자열 |암호 변경에 대한 개체의 이전 암호를 사용하여 채워졌습니다. 이 매개 변수는 OperationType이 **ChangePassword**인 경우에만 사용할 수 있습니다. |
| NewPassword |문자열 |스크립트가 설정해야 하는 개체의 새 암호를 사용하여 채워졌습니다. |

암호 스크립트는 Windows PowerShell 파이프라인에 아무 결과도 반환하지 않을 것으로 예상됩니다. 암호 스크립트에서 오류가 발생하는 경우 스크립트는 문제에 대한 동기화 서비스를 알리는 다음 예외 중 하나를 throw해야 합니다.

* [PasswordPolicyViolationException][pwdex1] – 암호가 연결된 시스템에서 암호 정책을 충족하지 않는 경우 throw됩니다.
* [PasswordIllFormedException][pwdex2] – 암호가 연결된 시스템에 허용되지 않는 경우 throw됩니다.
* [PasswordExtension][pwdex3] – 암호 스크립트의 다른 모든 오류에 대해 throw됩니다.

## <a name="sample-connectors"></a>샘플 커넥터
사용 가능한 샘플 커넥터의 전체적인 개요는 [Windows PowerShell 커넥터 샘플 커넥터 컬렉션][samp]을 참조하세요.

## <a name="other-notes"></a>기타 참고 사항
### <a name="additional-configuration-for-impersonation"></a>가장에 대한 추가 구성
동기화 서비스 서버에 다음 사용 권한을 가장하는 사용자에게 권한을 부여합니다.

다음 레지스트리 키에 대한 액세스를 읽습니다.

* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Software\Microsoft\PowerShell
* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Environment

동기화 서비스 서비스 계정의 보안 식별자(SID)를 확인하려면 다음 PowerShell 명령을 실행합니다.

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

다음 파일 시스템 폴더에 대한 액세스를 읽습니다.

* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\Extensions
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\MaData\\{ConnectorName}

{ConnectorName} 자리 표시자에 대한 Windows PowerShell 커넥터의 이름으로는 대체합니다.

## <a name="troubleshooting"></a>문제 해결
* 커넥터의 문제를 해결하기 위해 로깅을 사용하는 방법에 대한 자세한 내용은 [커넥터에 ETW 추적을 사용하는 방법](http://go.microsoft.com/fwlink/?LinkId=335731)참조하세요.

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
