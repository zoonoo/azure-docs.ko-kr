---
title: 예제-ISO 27001 공유 Services blueprint-배포 단계
description: ISO 27001 공유 서비스 청사진 샘플의 단계를 배포 합니다.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 64259d029ce67ecfd5db4b97fc165be0ee391ab8
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/15/2019
ms.locfileid: "58008069"
---
# <a name="deploy-the-azure-blueprints-iso-27001-shared-services-blueprint-sample"></a>Azure 청사진 ISO 27001 공유 Services blueprint 샘플 배포

Azure 청사진 ISO 27001 공유 Services blueprint 샘플을 배포 하려면 다음 단계를 수행 해야 합니다.

> [!div class="checklist"]
> - 이 샘플에서 새 blueprint 만들기
> - 으로 샘플의 사본을 표시 **게시 됨**
> - 청사진의 사본을 기존 구독에 할당

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="create-blueprint-from-sample"></a>샘플에서 blueprint 만들기

첫째, 스타터로 샘플을 사용 하 여 환경의 새 청사진을 만들어 blueprint 샘플을 구현 합니다.

1. 선택 **모든 서비스** 검색 하 고 선택 **정책** 왼쪽된 창에서. 에 **정책** 페이지에서 **청사진**합니다.

1. **Getting started** 페이지 왼쪽에서 선택 합니다 **만들기** 단추 _blueprint 만들기_합니다.

1. 찾기는 **ISO 27001: 공유 서비스** 아래에서 blueprint 샘플 _다른 샘플_ 선택한 **이 샘플을 사용 하 여**.

1. 입력 된 _기본 사항_ blueprint 샘플:

   - **Blueprint 이름**: ISO 27001 공유 서비스 청사진 샘플에 대 한 이름을 제공 합니다.
   - **정의 위치**: 줄임표를 사용 하 고 샘플에 복사본을 저장 하는 관리 그룹을 선택 합니다.

1. 선택 된 _아티팩트_ 페이지의 맨 위에 있는 탭 또는 **다음: 아티팩트** 페이지의 맨 아래에 있습니다.

1. Blueprint 샘플을 구성 하는 아티팩트 목록을 검토 합니다. 아티팩트 대부분에 매개 변수는 나중에 정의할 것입니다. 선택 **초안 저장** blueprint 샘플 검토를 완료 했습니다.

## <a name="publish-the-sample-copy"></a>게시 샘플 복사

이제 blueprint 샘플의 사본은 사용자 환경에서 작성 되었습니다. 만든 **초안** 모드 여야 합니다 **게시** 할당 고 배포할 수 있습니다. Blueprint 샘플의 복사본을 수정 ISO 27001 표준에서 이동할 수 있지만 해당 환경 및 요구 사항에 사용자 지정할 수 있습니다.

1. 선택 **모든 서비스** 검색 하 고 선택 **정책** 왼쪽된 창에서. 에 **정책** 페이지에서 **청사진**합니다.

1. 선택 된 **Blueprint 정의** 왼쪽 페이지입니다. 필터를 사용 하 여 blueprint 샘플의 복사본을 찾아서 선택 합니다.

1. 선택 **게시 blueprint** 페이지의 맨 위에 있는 합니다. 오른쪽에 새 페이지에서 제공 된 **버전** blueprint 샘플에 대 한 합니다. 이 속성은 나중에 수정할 경우에 유용 합니다. 제공할 **정보를 변경** "첫 번째 버전에서 ISO 27001 blueprint 샘플 게시 합니다."와 같은 선택한 **게시** 페이지의 맨 아래에 있습니다.

## <a name="assign-the-sample-copy"></a>샘플 복사 할당

Blueprint 샘플의 복사본을 성공적으로 되 면 **게시**에 저장 된 관리 그룹 내에서 구독을 할당할 수 있습니다. 이 단계는 각 배포 blueprint 샘플의 복사본을 고유 하 게 매개 변수가 제공 되는 위치입니다.

1. 선택 **모든 서비스** 검색 하 고 선택 **정책** 왼쪽된 창에서. 에 **정책** 페이지에서 **청사진**합니다.

1. 선택 된 **Blueprint 정의** 왼쪽 페이지입니다. 필터를 사용 하 여 blueprint 샘플의 복사본을 찾아서 선택 합니다.

1. 선택 **할당 blueprint** blueprint 정의 페이지의 맨 위에 있는 합니다.

1. Blueprint 할당에 대 한 매개 변수 값을 제공 합니다.

   - 기본 사항

     - **구독**: 하나를 선택 하거나 blueprint 샘플을 프레젠테이션과 저장 하는 관리 그룹에는 구독 중입니다. 둘 이상의 구독을 선택 하면 입력 매개 변수를 사용 하 여 각에 대 한 할당 만들어질 수 있습니다.
     - **할당 이름**: 이름을은 청사진의 이름에 따라 미리 채워집니다.
       필요에 따라 변경 하거나 그대로 둡니다.
     - **위치**: 만들려는 관리 되는 id에 대 한 지역을 선택 합니다. Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다. 자세히 알아보려면 [Azure 리소스의 관리 ID](../../../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
     - **Blueprint 정의 버전**: 선택 된 **게시** blueprint 샘플 복사본 버전.

   - 할당 잠금

     Blueprint 잠금 환경에 대 한 설정을 선택 합니다. 자세한 내용은 [청사진 리소스 잠금](../../concepts/resource-locking.md)을 참조하세요.

   - 관리 ID

     기본값을 그대로 두고 _시스템 할당_ id 옵션을 관리 합니다.

   - 청사진 매개 변수

     이 섹션에 정의 된 매개 변수는 일관성을 제공 blueprint 정의의 아티팩트 대부분에서 사용 됩니다.

     - **조직 이름**: 조직에 대 한 짧은 이름을 입력 합니다. 이 속성은 리소스 이름 지정에 대 한 주로 사용 됩니다.
     - **공유 서비스 서브넷 주소 접두사**: 배포 된 리소스를 함께 네트워킹에 대 한 CIDR 표기법으로 값을 제공 합니다.
     - **공유 서비스 위치**: 아티팩트를 배포 하는 위치를 결정 합니다. 일부 서비스는 모든 위치에서 사용할 수 있습니다. 이러한 서비스를 배포 하는 아티팩트에는 해당 아티팩트를 배포 하는 위치에 대 한 매개 변수 옵션을 제공 합니다.
     - **허용 되는 위치 (정책: ISO 27001에 대 한 이니셔티브를 blueprint)**: 리소스 그룹 및 리소스에 대 한 허용 되는 위치를 나타내는 값입니다.
     - **VM 에이전트에 대 한 log Analytics 작업 영역 (정책: ISO 27001에 대 한 이니셔티브를 blueprint)**: 작업 영역 리소스 ID를 지정합니다. 이 매개 변수 사용을 `concat` id입니다. 리소스를 생성 하는 함수

   - 아티팩트 매개 변수

     이 섹션에 정의 된 매개 변수는 정의 된 아티팩트에 적용 됩니다. 이러한 매개 변수는 [동적 매개 변수](../../concepts/parameters.md#dynamic-parameters) blueprint 할당 하는 동안 정의 때문입니다. 전체 목록은 또는 아티팩트 매개 변수 및 해당 설명을 참조 하세요 [아티팩트 매개 변수 테이블](#artifact-parameters-table)합니다.

1. 모든 매개 변수에 입력 한 후 선택 **할당** 페이지의 맨 아래에 있습니다.

> [!WARNING]
> Azure 청사진 서비스와 기본 제공 blueprint 샘플 **비용의 무료**합니다. Azure 리소스가 [제품으로 가격이 책정](https://azure.microsoft.com/en-us/pricing/)합니다. 사용 하 여는 [가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/) 이 blueprint 샘플에서 배포한 리소스를 실행 하는 비용을 예측 합니다.

## <a name="artifact-parameters-table"></a>아티팩트 매개 변수 테이블

다음 표에서 아티팩트 매개 변수를 청사진의 목록을 제공 합니다.

|아티팩트 이름|아티팩트 형식|매개 변수 이름|설명|
|-|-|-|-|
|[미리 보기]: Linux VM Scale Sets (VMSS)에 대 한 Log Analytics 에이전트를 배포 합니다.|정책 할당|선택 사항: 지원 되는 범위에 추가 하는 Linux OS는 VM 이미지 목록|(선택 사항) 기본값은 _["none"]_ 합니다.|
|[미리 보기]: Linux VM용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 지원 되는 범위에 추가 하는 Linux OS는 VM 이미지 목록|(선택 사항) 기본값은 _["none"]_ 합니다.|
|[미리 보기]: Windows VM Scale Sets (VMSS)에 대 한 Log Analytics 에이전트를 배포 합니다.|정책 할당|선택 사항: 지원 되는 범위에 추가할 Windows OS는 VM 이미지 목록|(선택 사항) 기본값은 _["none"]_ 합니다.|
|[미리 보기]: Windows VM용 Log Analytics 에이전트 배포|정책 할당|선택 사항: 지원 되는 범위에 추가할 Windows OS는 VM 이미지 목록|(선택 사항) 기본값은 _["none"]_ 합니다.|
|허용되는 리소스 유형|정책 할당|허용되는 리소스 유형|배포 하도록 허용 하는 리소스 유형 목록입니다. 이 목록은 공유 서비스에 배포 된 모든 리소스 형식으로 구성 됩니다.|
|허용되는 저장소 계정 SKU|정책 할당|저장소 Sku 허용|진단 목록을 로그 저장소 계정 Sku 허용 합니다. 기본값은 _["Standard_LRS"]_ 합니다.|
|허용되는 가상 머신 SKU|정책 할당|가상 머신 Sku 목록을 배포할 수 있습니다. 기본값은 _["Standard_DS1_v2", "Standard_DS2_v2"]_ 합니다.|
|ISO 27001의 Blueprint 이니셔티브|정책 할당|감사 진단 로그에 리소스 종류|진단 로그 설정을 사용 하지 않는 경우를 감사 하려면 리소스 형식의 목록입니다. 허용 되는 값을 찾을 수 있습니다 [Azure Monitor 진단 로그 스키마](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)합니다.|
|Log Analytics 리소스 그룹|리소스 그룹|이름|**잠긴** -연결 된 **조직 이름** 사용 하 여 `-sharedsvsc-log-rg` 리소스 그룹을 고유 하 게 합니다.|
|Log Analytics 리소스 그룹|리소스 그룹|위치|**잠긴** -blueprint 매개 변수를 사용 합니다.|
|Log Analytics 템플릿|Resource Manager 템플릿|서비스 계층|Log Analytics 작업 영역의 계층을 설정합니다. 기본값은 _PerNode_합니다.|
|Log Analytics 템플릿|Resource Manager 템플릿|로그 보존(일)|데이터 보존 (일)입니다. 기본값은 _365_합니다.|
|Log Analytics 템플릿|Resource Manager 템플릿|위치|Log Analytics 작업 영역을 만드는 데 사용 하는 영역입니다. 기본값은 _미국 서 부 2_합니다.|
|네트워크 리소스 그룹|리소스 그룹|이름|**잠긴** -연결 된 **조직 이름** 사용 하 여 `-sharedsvcs-net-rg` 리소스 그룹을 고유 하 게 합니다.|
|네트워크 리소스 그룹|리소스 그룹|위치|**잠긴** -blueprint 매개 변수를 사용 합니다.|
|Azure Firewall 템플릿|Resource Manager 템플릿|Azure Firewall 개인 IP|개인 IP를 구성 합니다 [Azure 방화벽](../../../../firewall/overview.md)합니다. 이 값은 공유 서비스 서브넷에 대 한 기본 경로 테이블로 사용 됩니다. 에 정의 된 CIDR 표기법의 일부분 이어야 **Azure 방화벽 서브넷 주소 접두사**합니다. 기본값은 _10.0.4.4_합니다.|
|Azure Firewall 템플릿|Resource Manager 템플릿|로그 보존(일)|데이터 보존 (일)입니다. 기본값은 _365_합니다.|
|네트워크 보안 그룹 템플릿|Resource Manager 템플릿|로그 보존(일)|데이터 보존 (일)입니다. 기본값은 _365_합니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Virtual Network 주소 접두사|가상 네트워크에 대 한 CIDR 표기법을 지정 합니다. 기본값은 _10.0.0.0/16_합니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Virtual Network DDoS Protection 사용|가상 네트워크의 DDoS protection을 구성합니다. 기본값은 _true_합니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|공유 서비스 서브넷 주소 접두사|공유 서비스 서브넷에 대 한 CIDR 표기법을 지정 합니다. 기본값은 _10.0.0.0/24_합니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|DMZ 서브넷 주소 접두사|DMZ 서브넷의 CIDR 표기법을 지정 합니다. 기본값은 _10.0.1.0/24_합니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Application Gateway 서브넷 주소 접두사|응용 프로그램 게이트웨이 서브넷의 CIDR 표기법을 지정 합니다. 기본값은 _10.0.2.0/24_합니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Virtual Network Gateway 서브넷 주소 접두사|가상 네트워크 게이트웨이 서브넷의 CIDR 표기법입니다. 기본값은 _10.0.3.0/24_합니다.|
|Virtual Network 및 경로 테이블 템플릿|Resource Manager 템플릿|Azure Firewall 서브넷 주소 접두사|에 대 한 CIDR 표기법으로 된 [Azure 방화벽](../../../../firewall/overview.md) 서브넷입니다. 포함 해야 합니다 **Azure 방화벽 개인 IP** 매개 변수입니다.|
|주요 자격 증명 모음 리소스 그룹|리소스 그룹|이름|**잠긴** -연결 된 **조직 이름** 사용 하 여 `-sharedsvcs-kv-rg` 리소스 그룹을 고유 하 게 합니다.|
|주요 자격 증명 모음 리소스 그룹|리소스 그룹|위치|**잠긴** -blueprint 매개 변수를 사용 합니다.|
|Key Vault 템플릿|Resource Manager 템플릿|Jumpbox 관리 사용자 이름|Jumpbox에 대 한 사용자 이름입니다. 동일한 속성 값과 일치 해야 합니다 **Jumpbox 템플릿**합니다. 기본값은 _jb 관리자_합니다.|
|Key Vault 템플릿|Resource Manager 템플릿|Jumpbox 관리자 SSH 키 또는 암호|키 또는 jumpbox에서 계정에 대 한 암호입니다. 동일한 속성 값과 일치 해야 합니다 **Jumpbox 템플릿**합니다. 기본값은 없습니다 값을 비워 둘 수 없습니다.|
|Key Vault 템플릿|Resource Manager 템플릿|도메인 관리 사용자 이름|Active Directory VM을 액세스 하 고 다른 Vm을 도메인에 가입 하는 사용자 이름입니다. 일치 해야 합니다 **도메인 관리자** 속성 값 **Active Directory Domain Services 템플릿**합니다. 기본값은 _도메인 관리자_합니다.|
|Key Vault 템플릿|Resource Manager 템플릿|도메인 관리자 암호|도메인 관리자 사용자 암호입니다. 기본값은 없습니다 값을 비워 둘 수 없습니다.|
|Key Vault 템플릿|Resource Manager 템플릿|AAD 개체 ID|Key Vault 인스턴스의 액세스 해야 하는 계정의 AAD 개체 식별자입니다. 기본값은 없습니다 값을 비워 둘 수 없습니다. Azure portal에서이 값을 찾으려면 검색 하 고 아래에서 "Users"를 선택 _Services_합니다. 사용 된 _이름_ 계정 이름으로 필터링 하 여 계정을 선택 하는 상자입니다. 에 _사용자 프로필_ 페이지에서 다음에 "복사 하기 위한 클릭" 아이콘을 선택 합니다 _개체 ID_합니다.  |
|Key Vault 템플릿|Resource Manager 템플릿|로그 보존(일)|데이터 보존 (일)입니다. 기본값은 _365_합니다.|
|Key Vault 템플릿|Resource Manager 템플릿|Key Vault SKU|만든 키 자격 증명 모음의 SKU를 지정 합니다. 기본값은 _Premium_합니다.|
|Jumpbox 리소스 그룹|리소스 그룹|이름|**잠긴** -연결 된 **조직 이름** 사용 하 여 `-sharedsvcs-jb-rg` 리소스 그룹을 고유 하 게 합니다.|
|Jumpbox 리소스 그룹|리소스 그룹|위치|**잠긴** -blueprint 매개 변수를 사용 합니다.|
|Jumpbox 템플릿|Resource Manager 템플릿|Jumpbox 관리 사용자 이름|Jumpbox Vm에 액세스 하는 데 사용자 이름입니다. 동일한 속성 값과 일치 해야 합니다 **Key Vault 템플릿**합니다. 기본값은 _jb 관리자_합니다.|
|Jumpbox 템플릿|Resource Manager 템플릿|Jumpbox 관리자 암호 (키 자격 증명 모음 리소스 ID)|Key Vault의 리소스 ID입니다. 사용 하 여 "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" 바꾸고 `{subscriptionId}` 구독 ID로 하 고 `{orgName}` 사용 하 여는  **조직 이름** blueprint 매개 변수입니다.|
|Jumpbox 템플릿|Resource Manager 템플릿|Jumpbox 관리자 암호 (키 자격 증명 모음 비밀 이름)|Jumpbox 관리자의 사용자 이름 값과 일치 해야 합니다 **Key Vault 템플릿** 속성 **Jumpbox 관리자 사용자 이름**합니다.|
|Jumpbox 템플릿|Resource Manager 템플릿|Jumpbox 운영 체제|Jumpbox VM의 운영 체제를 확인합니다. 기본값은 _Windows_합니다.|
|Active Directory Domain Services 리소스 그룹|리소스 그룹|이름|**잠긴** -연결 된 **조직 이름** 사용 하 여 `-sharedsvcs-adds-rg` 리소스 그룹을 고유 하 게 합니다.|
|Active Directory Domain Services 리소스 그룹|리소스 그룹|위치|**잠긴** -blueprint 매개 변수를 사용 합니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리 사용자 이름|ADDS jumpbox에 대 한 사용자 이름입니다. 동일한 속성 값과 일치 해야 합니다 **Key Vault 템플릿**합니다. 기본값은 _관리자 추가-사용자_합니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리자 암호 (키 자격 증명 모음 리소스 ID)|Key Vault의 리소스 ID입니다. 사용 하 여 "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" 바꾸고 `{subscriptionId}` 구독 ID로 하 고 `{orgName}` 사용 하 여는  **조직 이름** blueprint 매개 변수입니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리자 암호 (키 자격 증명 모음 비밀 이름)|도메인 관리자의 사용자 이름 값과 일치 해야 합니다 **Key Vault 템플릿** 속성 **도메인 관리자 사용자 이름**합니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 이름|샘플에서 만든 Active Directory의 이름입니다. 기본값은 _contoso.com_합니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리자 사용자|장치를 AD 도메인에 가입 및 AD 관리자 계정에 대 한 사용자 이름입니다. 일치 해야 합니다 **AD 관리자 사용자 이름** 속성 값 **Key Vault 템플릿**합니다. 기본값은 _도메인 관리자_합니다.|
|Active Directory Domain Services 템플릿|Resource Manager 템플릿|도메인 관리자 암호|암호를 저장 하기 위한 키 자격 증명 모음 세부 정보를 설정 합니다. 기본값은 없습니다 값을 비워 둘 수 없습니다.|

## <a name="next-steps"></a>다음 단계

ISO 27001 공유 Services blueprint 예제를 배포 하는 단계를 검토 했으므로 이제 컨트롤 매핑을 확인 하 고 아키텍처에 대해 자세히 알아보려면 다음 문서를 참조:

> [!div class="nextstepaction"]
> [ISO 27001 공유 서비스 blueprint-개요](./index.md)
> [ISO 27001 공유 서비스 blueprint-컨트롤 매핑](./control-mapping.md)

청사진 및 사용 하는 방법에 대 한 추가 문서:

- 에 대 한 자세한 합니다 [수명 주기 blueprint](../../concepts/lifecycle.md)합니다.
- 사용 하는 방법 이해 [정적 및 동적 매개 변수](../../concepts/parameters.md)합니다.
- 사용자 지정 하는 방법을 알아봅니다 합니다 [시퀀싱 순서 blueprint](../../concepts/sequencing-order.md)합니다.
- 확인 하는 방법을 알아봅니다 이용 [리소스 잠금 blueprint](../../concepts/resource-locking.md)합니다.
- 설명 하는 방법 [기존 할당을 업데이트할](../../how-to/update-existing-assignments.md)합니다.