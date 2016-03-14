<properties
   pageTitle="Azure 리소스에 대한 권장되는 명명 규칙 | 지침 | Microsoft Azure"
   description="Azure 리소스에 대한 권장되는 명명 규칙. 가상 컴퓨터, 저장소 계정, 네트워크, 가상 네트워크, 서브넷 및 기타 Azure 엔터티의 이름을 지정하는 방법"
   services=""
   documentationCenter="na"
   authors="masimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="masimms"/>
   
# Azure 리소스에 대한 권장되는 명명 규칙

Microsoft Azure에서 모든 리소스에 대한 이름 선택은 다음 이유로 인해 중요합니다.

- 나중에 이름을 변경하는 것은 어렵습니다.
- 이름은 해당 특정 리소스 유형의 요구 사항을 충족해야 합니다.

마찬가지로 일관된 명명 규칙은 리소스를 더 쉽게 찾을 수 있도록 합니다. 또한 솔루션의 리소스 역할 이해에 도움이 됩니다.

이 문서는 Azure 리소스에 대한 명명 규칙 및 제한 사항에 대한 요약 뿐만 아니라 명명 규칙에 대한 권장 사항의 기준 집합을 제공합니다. 필요에 따라 사용자 고유의 규칙에 대한 시작 지점으로 이러한 권장 사항을 사용할 수 있습니다.

명명 규칙에 대한 성공의 열쇠는 응용 프로그램 및 조직에서 이를 설정 및 따르고 Azure 플랫폼 전반에 걸쳐 더 많은 응용 프로그램 및 서비스를 배포하는 동안 조정하는 것입니다.

## 구독 명명

Azure 구독을 명명하는 경우 자세한 이름은 각 구독의 컨텍스트 및 용도에 대한 이해를 명확하게 합니다. 많은 구독을 포함할 수 있는 환경에서 작업하는 경우 공유 명명 규칙을 따르는 것은 명확성을 크게 향상시킬 수 있습니다.

구독 명명에 대한 권장되는 패턴은 다음과 같습니다.

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- 대부분의 경우에서 회사는 각 구독에 대해 동일할 것입니다. 그러나 일부 회사에서는 조직 구조 내에 자식 회사가 있을 수 있습니다. 이러한 회사는 중앙 IT 그룹에 의해 관리될 수 있으며 이 경우 부모 회사 이름(*Contoso*) 및 자식 회사 이름(*North Wind*) 모두를 가져서 식별될 수 있습니다.

- 부서는 개인 작업의 그룹인 조직 내에서 이름입니다. 네임스페이스 내에서 이 항목은 선택적입니다. 일부 회사에서는 크기로 인해 그러한 세부 정보를 자세히 다룰 필요가 없을 수 있습니다.

- 제품 라인은 부서 내에서 수행되는 제품 또는 기능에 대한 특정 이름입니다. 내부 전용 서비스 및 응용 프로그램에 대해 일반적으로 선택적이지만 쉬운 분리 및 ID(예: 청구 레코드의 명확한 분리)가 필요한 공용 서비스에 사용하는 것이 좋습니다.

- 환경은 개발, QA 또는 프로덕션 등의 응용 프로그램 또는 서비스의 배포 수명 주기를 설명하는 이름입니다.

| 회사 | 부서 | 제품 라인 또는 서비스 | 환경 | 전체 이름 |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | 프로덕션 | Contoso SocialGaming AwesomeService 프로덕션 |
| Contoso | SocialGaming | AwesomeService | 개발 | Contoso SocialGaming AwesomeService 개발 |
| Contoso | IT | InternalApps | 프로덕션 | Contoso IT InternalApps 프로덕션 |
| Contoso | IT | InternalApps | 개발 | Contoso IT InternalApps 개발 |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc -->

## 모호성을 예방하기 위해 접사 사용

Azure에서 리소스를 명명하는 경우 리소스의 유형 및 컨텍스트를 확인하도록 공통 접두사 또는 접미사를 사용하는 것이 좋습니다. 유형, 메타데이터, 컨텍스트에 대한 모든 정보는 프로그래밍 방식으로 사용할 수 있지만 공통 접사 사용은 식별을 간소화합니다. 명명 규칙에 접사를 통합할 때 접사를 이름의 시작 부분(접두사) 또는 끝 부분(접미사)에 붙여야 하는지 여부를 명확하게 지정하는 것이 중요합니다.

예를 들어 다음은 계산 엔진을 호스팅하는 서비스에 가능한 두 이름입니다.

- SvcCalculationEngine(접두사)
- CalculationEngineSvc(접미사)

접사는 특정 리소스를 설명하는 다양한 측면을 참조할 수 있습니다. 다음 표에서 일반적으로 사용하는 일부 예를 보여줍니다.

| 측면 | 예제 | 참고 사항 |
| ------ | ------- | ----- |
| 환경 | 개발, 프로덕션, QA | 리소스에 대한 환경 식별 |
| 위치 | uw(미국 서부), ue(미국 동부) | 리소스가 배포되는 지역 식별 |
| 인스턴스 | 01, 02 | 둘 이상의 명명된 인스턴스가 있는 리소스의 경우(웹 서버 등). |
| 제품 또는 서비스 | 서비스 | 리소스가 지원하는 제품, 응용 프로그램 또는 서비스 식별 |
| 역할 | sql, 웹, 메시징 | 연결된 리소스의 역할 식별 |

회사 또는 프로젝트에 대한 특정 명명 규칙을 개발할 때 접사의 공통 집합 뿐만 아니라 해당 위치(접미사 또는 접두사)를 선택하는 것이 중요합니다.

## 명명 규칙 및 제한 사항

Azure의 각 리소스 또는 서비스 유형은 명명 제한 사항 및 범위가 적용됩니다. 모든 명명 규칙 또는 패턴은 필수 명명 규칙 및 범위를 준수해야 합니다. 예를 들어 VM의 이름을 DNS 이름에 매핑(따라서 모든 Azure에서 고유해야 함)하는 동안 VNET의 이름은 범위 내에서 만든 리소스 그룹으로 지정됩니다.

대부분의 유효성 검사 규칙에 실패하므로 일반적으로 모든 이름에서 첫 번째 또는 마지막 문자로 특수 문자(`-` 또는 `_`)를 피합니다.

| Category | 서비스 또는 엔터티 | 범위 | 길이 | 대/소문자 구분 | 사용할 수 있는 문자 | 제안된 패턴 | 예제 |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| 리소스 그룹 | 리소스 그룹 | 전역 | 1-64 | 대/소문자 구분하지 않음 | 영숫자, 밑줄 및 하이픈 | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| 리소스 그룹 | 가용성 집합 | 리소스 그룹 | 1-80 | 대/소문자 구분하지 않음 | 영숫자, 밑줄 및 하이픈 | `<service-short-name>-<context>-as` | `profx-sql-as` |
| 일반 | 태그 | 연결된 엔터티 | 512(이름), 256(값) | 대/소문자 구분하지 않음 | 영숫자 | `"key" : "value"` | `"department" : "Central IT"` |
| 계산 | 가상 컴퓨터 | 리소스 그룹 | 1-15 | 대/소문자 구분하지 않음 | 영숫자, 밑줄 및 하이픈 | `<name>-<role>-<instance>` | `profx-sql-001` |
| 저장소 | 저장소 계정 이름(데이터) | 전역 | 3-24 | 소문자 | 영숫자 | `<service short name><type><number>` | `profxdata001` |
| 저장소 | 저장소 계정 이름(디스크) | 전역 | 3-24 | 소문자 | 영숫자 | `<vm name without dashes>st<number>` | `profxsql001st0` |
| 저장소 | 컨테이너 이름 | 저장소 계정 | 3-63 |	소문자 | 영숫자 및 대시 | `<context>` | `logs` |
| 저장소 | Blob 이름 | 컨테이너 | 1-1024 | 대/소문자 구분 | 모든 URL 문자 | `<variable based on blob usage>` | `<variable based on blob usage>` |
| 저장소 | 큐 이름 | 저장소 계정 | 3-63 | 소문자 | 영숫자 및 대시 | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| 저장소 | 테이블 이름 | 저장소 계정 | 3-63 |대/소문자 구분하지 않음 | 영숫자 | `<service short name>-<context>` | `awesomeservice-logs` |
| 저장소 | 파일 이름 | 저장소 계정 | 3-63 | 소문자 | 영숫자 | `<variable based on blob usage>` | `<variable based on blob usage>` |
| 네트워킹 | 가상 네트워크(VNet) | 리소스 그룹 | 2-80 | 대/소문자 구분하지 않음 | 영숫자, 대시, 밑줄 및 마침표 | `<service short name>-[section]-vnet` | `profx-vnet` |
| 네트워킹 | 서브넷 | 부모 VNet | 2-80 | 대/소문자 구분하지 않음 | 영숫자, 밑줄, 대시 및 마침표 | `<role>-subnet` | `gateway-subnet` |
| 네트워킹 | 네트워크 인터페이스 | 리소스 그룹 | 1-80 | 대/소문자 구분하지 않음 | 영숫자, 대시, 밑줄 및 마침표 | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| 네트워킹 | 네트워크 보안 그룹 | 리소스 그룹 | 1-80 | 대/소문자 구분하지 않음 | 영숫자, 대시, 밑줄 및 마침표 | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| 네트워킹 | 네트워크 보안 그룹 규칙 | 리소스 그룹 | 1-80 | 대/소문자 구분하지 않음 | 영숫자, 대시, 밑줄 및 마침표 | `<descriptive context>` | `sql-allow` |
| 네트워킹 | 공용 IP 주소 | 리소스 그룹 | 1-80 | 대/소문자 구분하지 않음 | 영숫자, 대시, 밑줄 및 마침표 | `<vm or service name>-pip` | `profx-sql1-pip` |
| 네트워킹 | 부하 분산 장치 | 리소스 그룹 | 1-80 | 대/소문자 구분하지 않음 | 영숫자, 대시, 밑줄 및 마침표 | `<service or role>-lb` | `profx-lb` |
| 네트워킹 | 부하 분산된 규칙 구성 | 부하 분산 장치 | 1-80 | 대/소문자 구분하지 않음 | 영숫자, 대시, 밑줄 및 마침표 | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | TODO | TODO
-->

## 태그를 사용하여 리소스 구성

Azure Resource Manager는 임의적인 텍스트 문자열로 태그 지정 엔터티를 지원하여 컨텍스트를 식별하고 자동화를 간소화합니다. 예를 들어 `"sqlVersion: "sql2014ee"`와(과) 같은 태그는 자동화된 스크립트 실행을 위해 SQL Server 2014 Enterprise Edition을 실행하는 배포의 모든 VM을 식별할 수 있었습니다. 태그는 선택한 명명 규칙에 대한 컨텍스트를 보강 및 향상하는 데 사용되어야 합니다.

> [AZURE.TIP] 태그의 다른 한 장점은 태그는 서로 다른 배포 전체에서 엔터티를 연결 및 상관 관계를 지정할 수 있도록 하는 리소스 그룹을 확장한다는 점입니다.

각 리소스 또는 리소스 그룹에는 최대 **15**개의 태그가 포함될 수 있습니다. 태그 이름은 512자로 제한되며 태그 값은 256자로 제한됩니다.

리소스 태그 지정에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../resource-group-using-tags.md)을 참조하세요.

일반적인 태그 사용 사례는 다음과 같습니다.

- **청구**; 리소스를 그룹화하고 청구 또는 차지 백 코드와 연결합니다.
- **서비스 컨텍스트 ID**; 일반 작업 및 그룹화에 대한 리소스 그룹의 리소스 그룹 식별
- **액세스 제어 및 보안 컨텍스트**; 포트폴리오, 시스템, 서비스, 앱, 인스턴스 등을 기준으로 관리 역할 식별 

> [AZURE.TIP] 태그 초기 - 태그인 경우가 많습니다. 사후에 개/보수하는 것보다 기준 태그 지정 체계를 준비하고 시간에 따라 조정하는 것이 좋습니다.

일부 일반적인 태그 지정 방법의 예:

| 태그 이름 | 키 | 예제 | 주석 |
| -------- | --- | ------- | ------- |
| 청구 대상 / 내부 차지백 ID | 청구 대상 | `IT-Chargeback-1234` | 내부 I/O 또는 청구 코드 |
| 운영자 또는 DRI(직접적인 책임자) | managedBy | `joe@contoso.com` | 별칭 또는 메일 주소 |
| 프로젝트 이름 | project-name | `myproject` | 프로젝트 또는 제품 라인 이름 |
| 프로젝트 버전 | project-version | `3.4` | 프로젝트 또는 제품 라인 버전 |
| 환경 | environment | `<Production, Staging, QA >` | 환경 식별자 | 
| 계층 | tier | `Front End, Back End, Data` | 계층 또는 역할/컨텍스트 식별 |
| 데이터 프로필 | dataProfile | `Public, Confidential, Restricted, Internal` | 리소스에 저장된 데이터의 민감도 |
 
## 팁과 요령

응용 프로그램의 유형에 따라 특정 유형의 리소스는 이름 지정 및 규칙에 주의해야 합니다. 이에 대한 추가 세부 정보 및 컨텍스트는 아래에 나와 있습니다.

### 가상 컴퓨터

특히 더 큰 토폴로지에서 신중하게 가상 컴퓨터를 명명하는 것은 각 컴퓨터의 역할 및 용도 식별을 간소화할 뿐만 아니라 예측 가능성이 더욱 뛰어난 스크립팅을 활성화합니다.

> [AZURE.WARNING] Azure의 모든 가상 컴퓨터는 모두 Azure 리소스 이름과 운영 체제 호스트 이름을 갖습니다. 리소스 이름 및 호스트 이름이 다른 경우 이러한 VM 관리는 어려울 수 있으며(예: 가상 컴퓨터가 호스트 이름으로 구성된 운영 체제를 이미 포함하는 .vhd에서 만들어진 경우) 피해야 합니다.

- [Windows Server VM에 대한 명명 규칙](https://support.microsoft.com/ko-KR/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

###	저장소 계정 및 저장소 엔터티

저장소 계정에 대한 두 가지 기본 사용 사례가 있습니다. VM에 대한 디스크 백업, Blob, 큐 및 테이블에 데이터 저장. VM 디스크에 사용되는 저장소 계정은 부모 VM 이름과 연결하는 명명 규칙을 따라야 합니다(및 고성능 VM SKU에 대한 여러 저장소 계정에 대한 잠재적 필요성으로, 숫자 접미사 활용).

> [AZURE.TIP] 저장소 계정(데이터 또는 디스크에 대한)은 여러 저장소 계정을 활용되도록 허용하는 명명 규칙을 따라야 합니다(즉 항상 숫자 접미사 사용).

Azure 저장소 계정에서 Blob 데이터에 액세스할 수 있도록 사용자 지정 도메인 이름을 구성할 수 있습니다. Blob 서비스의 기본 끝점은 `https://mystorage.blob.core.windows.net`입니다.

그러나 사용자 지정 도메인(예: www.contoso.com)을 저장소 계정의 Blob 끝점에 매핑하는 경우 해당 도메인을 사용하여 저장소 계정의 Blob 데이터에 액세스할 수 있습니다. 예를 들어 사용자 지정 도메인 이름으로 `http://mystorage.blob.core.windows.net/mycontainer/myblob`은(는) `http://www.contoso.com/mycontainer/myblob`(으)로 액세스될 수 있습니다.

이 기능 구성에 대한 자세한 내용은 [Blob 저장소 끝점에 대한 사용자 지정 도메인 이름 구성](../storage/storage-custom-domain-name.md)을 참조하세요.

Blob, 컨테이너 및 테이블 명명에 대한 자세한 내용:

- [컨테이너, BLOB, 메타데이터 이름 명명 및 참조](https://msdn.microsoft.com/library/dd135715.aspx)
- [큐 및 메타데이터 명명](https://msdn.microsoft.com/library/dd179349.aspx)
- [테이블 명명](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Blob 이름은 문자 조합을 포함할 수 있지만 예약된 URL 문자는 적절히 이스케이프되어야 합니다. 마침표(.), 슬래시(/) 또는 시퀀스 또는 둘의 조합으로 끝나는 Blob 이름은 피합니다. 규칙에 따라 슬래시는 **가상** 디렉터리 구분 문자입니다. Blob 이름에 백슬래시()를 사용하지 마십시오. 클라이언트 API는 이를 허용할 수도 있지만 이 경우 제대로 해시하지 않고 서명이 일치하지 않게 됩니다.

만든 후 저장소 계정 또는 컨테이너의 이름을 수정하는 것이 불가능합니다. 새 이름을 사용하려는 경우 삭제하고 새로 만들어야 합니다.

> [AZURE.TIP] 새 서비스 또는 응용 프로그램의 개발을 시작하기 전에 모든 저장소 계정 및 유형에 대한 명명 규칙을 설정하는 것이 좋습니다.

## 예 - N 계층 서비스 배포

이 예제에서 SQL Server(두 개의 Windows Server VM에서 호스트됨), ElasticSearch 클러스터(6 Linux VM에서 호스트됨) 및 연결된 저장소 계정, 가상 네트워크, 리소스 그룹 및 부하 분산 장치와 함께 프런트 엔드 IIS 서버(Windows Server VM에서 호스트됨)로 구성된 N 계층 서비스 구성을 정의합니다.

이 응용 프로그램에 대한 상황별 규칙을 정의하여 시작합니다.

| 엔터티 | 규칙 | 설명 |
| ------ | ---------- | ------------ |  
| 서비스 이름 | `profx` | 배포되는 응용 프로그램 또는 서비스의 짧은 이름 |
| 환경 | `prod` | 프로덕션 배포용(QA, 테스트 등이 아닌) |

이러한 기준선에서 각 리소스 종류에 대한 규칙을 준비할 수 있습니다.

| 리소스 종류 | 규칙 기본 | 예제 |
| ------------- | --------------- | ------- |
| 구독 | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| 리소스 그룹 | `servicename-rg` | `profx-rg` |
| 가상 네트워크 | `servicename-vnet` | `profx-vnet` |
| 서브넷 | `role-subnet` | `sql-vnet` |
| 부하 분산 장치 | `servicename-lb` | `profx-lb` |
| 가상 컴퓨터 | `servicename-role[number]` | `profx-sql0` |
| 저장소 계정 | `<vmnamenodashes>st<num>` | `profxsql0st0` |

아래 다이어그램에 표시합니다.

![응용 프로그램 토폴로지 다이어그램](media/guidance-naming-conventions/guidance-naming-convention-example.png "샘플 응용 프로그램 토폴로지")

## 샘플 - 위의 샘플을 배포하기 위한 Azure CLI 스크립트

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

	# Create the network interface card for this VM
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

	# Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

<!---HONumber=AcomDC_0302_2016-->