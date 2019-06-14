---
title: Azure의 Red Hat Enterprise Linux 이미지 | Microsoft Docs
description: Microsoft Azure의 Red Hat Enterprise Linux 이미지에 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: b40f62a90dbe7c822b95476abe6ec25cf3fb21d6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070023"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Azure의 Red Hat Enterprise Linux 이미지
이 문서에서는 명명 및 보존 정책에 대한 정책과 함께 Azure Marketplace에서 사용할 수 있는 RHEL(Red Hat Enterprise Linux) 이미지를 설명합니다.

모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.

>[!Important]
> Azure Marketplace에서 현재 사용할 수 있는 RHEL 이미지는 BYOS(Bring-Your-Own-Subscription) 또는 PANG(종량제) 라이선스 모델을 지원합니다. [Azure Hybrid Use Benefit](../windows/hybrid-use-benefit-licensing.md) 및 BYOS와 PAYG 간의 동적 전환은 지원되지 않습니다. 라이선스 모드를 전환하려면 해당 이미지에서 VM을 다시 배포해야 합니다.

>[!Note]
> Azure Marketplace Gallery의 RHEL 이미지와 관련된 문제에 대해서는 Microsoft에 지원 티켓을 제출하세요.

## <a name="images-available-in-the-ui"></a>UI에서 사용 가능한 이미지
Marketplace에서 "Red Hat"을 검색하거나 Azure Portal UI에서 리소스를 만들 때 사용할 수 있는 RHEL 이미지의 하위 세트 및 관련된 Red Hat 제품이 표시됩니다. Azure CLI/PowerShell/API를 사용하여 항상 사용 가능한 VM 이미지의 전체 세트를 얻을 수 있습니다.

Azure에서 사용 가능한 Red Hat 이미지의 전체 세트를 보려면 다음 명령을 실행합니다.

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>명명 규칙
Azure에서 VM 이미지는 게시자, 제품, SKU 및 버전별로 구성됩니다. 게시자:제품:SKU:버전의 조합은 이미지 URN이며 사용할 이미지를 고유하게 식별합니다.

예를 들어 `RedHat:RHEL:7-RAW:7.6.2018103108`은 2018년 10월 31일에 빌드된 RHEL 7.6 원시 분할된 이미지를 나타냅니다.

RHEL 7.6 VM을 만드는 방법의 샘플은 다음과 같습니다.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"최신" 모니커
Azure REST API는 특정 버전 대신 버전에 대한 "최신" 모니커의 사용을 허용합니다. "최신"을 사용하면 지정된 게시자, 제품 및 SKU에 대해 사용 가능한 최신 이미지를 프로비전합니다.

예를 들어 `RedHat:RHEL:7-RAW:latest`는 사용 가능한 최신 RHEL 7 제품군 원시 분할된 이미지를 나타냅니다.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> 일반적으로 최신을 결정하는 버전의 비교는 [CompareTo 메서드](https://msdn.microsoft.com/library/a5ts8tb6.aspx)의 규칙을 따릅니다.

### <a name="current-naming-convention"></a>현재 명명 규칙
현재 게시된 모든 RHEL 이미지는 종량제 모델을 사용하며 [Azure의 RHUI(Red Hat 업데이트 인프라)](https://aka.ms/rhui-update)에 연결되어 있습니다. 새 명명 규칙은 채택 되는 디스크 파티션 구성표가 RHEL 7 제품군 이미지에 대 한 (raw, LVM) 버전 대신 SKU에 지정 됩니다. RHEL 이미지 버전 중 하나를 포함 하는 7 원시 또는 LVM 7입니다. RHEL 6 제품군 명명은 현재 변경되지 않았습니다.

이 명명 규칙에서 RHEL 7 이미지 Sku의 2 형식 됩니다. 부 버전을 나열 하는 Sku와 하지 않는 Sku입니다. 7-LVM SKU 또는 7 원시 사용 하려는 경우에 버전에서 배포 하려는 RHEL 부 버전을 지정할 수 있습니다. 됩니다 "최신"을 선택 하면 RHEL의 사소한 최신 릴리스를 프로 비전 합니다.

>[!NOTE]
> SAP 이미지 세트에 대한 RHEL에서 RHEL 버전은 고정으로 유지됩니다. 따라서 해당 명명 규칙은 SKU에서 특정 버전을 포함합니다.

>[!NOTE]
> RHEL 6 이미지 세트는 새 명명 규칙으로 이동되지 않았습니다.

## <a name="extended-update-support-eus"></a>확장된 업데이트 지원 (EUS)
4 월 2019의 RHEL 이미지를 사용할 수 있는 확장 업데이트 지원 (EUS) 저장소에 기본적으로 연결 된 합니다. 사용할 수 있는 RHEL EUS 대 한 자세한 내용은 [Red Hat 설명서](https://access.redhat.com/articles/rhel-eus)합니다.

EUS EUS 지원 수명 종료 날짜에 대 한 자세한 내용은 VM 전환 하는 방법에 사용할 수 있습니다 [여기](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)합니다.

>[!NOTE]
> EUS는 RHEL 추가 기능에서 지원 되지 않습니다. 이 일반적으로 RHEL Extras 채널에서 사용할 수 있는 패키지를 설치 하는 경우 있습니다 됩니다 EUS 반면 그렇게 할 것을 의미 합니다. Red Hat 기타 제품 수명 주기는 자세한 [여기](https://access.redhat.com/support/policy/updates/extras/)합니다.

### <a name="for-customers-that-want-to-use-eus-images"></a>EUS 이미지를 사용 하려는 고객의 경우:
EUS 리포지토리에 연결 된 이미지를 사용 하려는 고객은 SKU에 RHEL 부 버전 번호를 포함 하는 RHEL 이미지를 사용 해야 합니다. 이러한 이미지를 원시 분할 됩니다 (즉, 없습니다 LVM).

예를 들어 사용 가능한 다음 2 RHEL 7.4 이미지 표시 될 수 있습니다.
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
이 예에서 `RedHat:RHEL:7.4:7.4.2019041718` 기본적으로 EUS 리포지토리에 연결할 및 `RedHat:RHEL:7-RAW:7.4.2018010506` 기본적으로 비 EUS 저장소에 연결 됩니다.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>EUS 이미지를 사용 하지 않은 고객의 경우:
기본적으로 EUS에 연결 되어 있는 이미지를 사용 하지 않으려는 경우 SKU의 부 버전 번호를 포함 하지 않는 이미지를 사용 하 여 배포 합니다.

#### <a name="rhel-images-with-eus"></a>EUS 사용 하 여 RHEL 이미지
다음 표에서 SKU의 부 버전을 포함 하는 RHEL 이미지에 적용 됩니다.

>[!NOTE]
> 작성 당시 RHEL 7.4 및 부 버전 나중 경우 EUS 지원 Rhel EUS 더 이상 지원 되지 < 7.3 =.

부 버전 |EUS 이미지 예제              |EUS 상태                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | 이미지는 2019 년 4 월 게시 하 고 나중는 기본적으로 EUS 됩니다.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 이미지는 2019 년 6 월을 게시 하 고 나중는 기본적으로 EUS 됩니다. |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 이미지는 2019 년 5 월 게시 하 고 나중는 기본적으로 EUS 됩니다.  |
RHEL 8.0      |N/A                            | 없는 EUS 현재 사용 가능한 현재 이미지                 |


## <a name="list-of-rhel-images-available"></a>사용할 수 있는 RHEL 이미지 목록
다음 제품은 SKU이며 현재 일반 용도로 사용할 수 있습니다.

제안| SKU | 분할 | 프로비전 | 메모
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Linux 에이전트 | RHEL 7 제품군 이미지입니다. <br> 기본적으로 EUS 리포지토리 연결 되지 않은 경우
|             | 7-LVM    | LVM    | Linux 에이전트 | RHEL 7 제품군 이미지입니다. <br> 기본적으로 EUS 리포지토리 연결 되지 않은 경우
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7 제품군 이미지입니다. <br> 기본적으로 EUS 리포지토리 연결 되지 않은 경우
|             | 6.7      | RAW    | Linux 에이전트 | RHEL 6.7 이미지, 이전 명명 규칙
|             | 6.8      | RAW    | Linux 에이전트 | RHEL 6.8에 대해 위와 동일
|             | 6.9      | RAW    | Linux 에이전트 | RHEL 6.9에 대해 위와 동일
|             | 6.10     | RAW    | Linux 에이전트 | RHEL 6.10에 대해 위와 동일
|             | 7.2      | RAW    | Linux 에이전트 | RHEL 7.2에 대해 위와 동일
|             | 7.3      | RAW    | Linux 에이전트 | RHEL 7.3에 대해 위와 동일
|             | 7.4      | RAW    | Linux 에이전트 | RHEL 7.4에 위와 동일 합니다. <br> 2019 년 4 월부터 기본적으로 EUS 리포지토리에 연결
|             | 7.5      | RAW    | Linux 에이전트 | RHEL 7.5에 위와 동일 합니다. <br> 2019 년 6 월을 기준으로 기본적으로 EUS 리포지토리에 연결
|             | 7.6      | RAW    | Linux 에이전트 | RHEL 7.6에 위와 동일 합니다. <br> 2019 년 5 월을 기준으로 기본적으로 EUS 리포지토리에 연결
RHEL-SAP      | 7.4      | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.4
|             | 7.5      | LVM    | Linux 에이전트 | SAP HANA 및 비즈니스 앱용 RHEL 7.5
RHEL-SAP-HANA | 6.7      | RAW    | Linux 에이전트 | SAP HANA용 RHEL 6.7
|             | 7.2      | LVM    | Linux 에이전트 | SAP HANA용 RHEL 7.2
|             | 7.3      | LVM    | Linux 에이전트 | SAP HANA용 RHEL 7.3
RHEL-SAP-APPS | 6.8      | RAW    | Linux 에이전트 | SAP Business Applications용 RHEL 6.8
|             | 7.3      | LVM    | Linux 에이전트 | SAP Business Applications용 RHEL 7.3

### <a name="old-naming-convention"></a>이전 명명 규칙
RHEL 7 이미지의 제품군 RHEL 6 이미지의 제품군은 위에서 설명한 명명 규칙이 변경될 때까지 해당 SKU의 특정 버전을 사용했습니다.

전체 이미지 목록에서 숫자 SKU를 찾을 수 있습니다. Microsoft 및 Red Hat은 새로운 사소한 릴리스가 출시될 때 새 숫자 SKU를 만듭니다.

### <a name="other-available-offers-and-skus"></a>다른 사용 가능한 제품 및 SKU
사용 가능한 제품 및 SKU의 전체 목록은 위의 표에 나열된 것 이외에 추가 이미지를 포함할 수 있습니다(예: `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`). 이러한 제품은 특정 마켓플레이스 솔루션의 지원을 제공하는 데 사용할 수 있거나 미리 보기 및 테스트 목적으로 게시될 수 있습니다. 경고 없이 언제든지 변경되거나 제거될 수 있습니다. 현재 상태가 Microsoft 또는 Red Hat에서 공개적으로 문서화되지 않는 한 사용하지 마십시오.

## <a name="publishing-policy"></a>정책 게시
Microsoft 및 Red Hat은 특정 CVE를 해결하기 위해 필요에 따라 또는 가끔 구성 변경/업데이트를 위해 새로운 부 버전이 출시될 때 이미지를 업데이트합니다. CVE 수정 사항의 릴리스 또는 가용성 이후 영업일 3일 이내에 가능한 빨리 업데이트된 이미지를 제공하기 위해 노력합니다.

지정된 이미지 제품군의 현재 부 릴리스만 업데이트합니다. 최신 부 버전 릴리스로 이전 부 버전 업데이트를 중지합니다. 예를 들어 RHEL 7.6의 릴리스로 RHEL 7.5 이미지는 더 이상 업데이트되지 않습니다.

>[!NOTE]
> RHEL 종량제 이미지에서 프로비전된 활성 Azure VM은 Azure RHUI에 연결되며 Red Hat에서 릴리스되고 Azure RHUI에 복제되는 대로 즉시 업데이트 및 수정 사항을 받을 수 있습니다(일반적으로 Red Hat의 공식 릴리스 후 24시간 이내). 이러한 VM에는 업데이트를 위해 게시된 새 이미지가 필요하지 않으며 고객은 업데이트를 시작하는 시기에 대한 모든 권한을 갖습니다.

## <a name="image-retention-policy"></a>이미지 보존 정책
현재 정책은 이전에 게시된 모든 이미지를 유지하는 것입니다. 모든 종류의 문제를 일으키는 것으로 알려진 이미지를 제거할 권리를 보유합니다. 예를 들어 후속 플랫폼 또는 구성 요소 업데이트로 인한 잘못된 구성의 이미지는 제거될 수 있습니다. 제거될 수 있는 이미지는 이미지 제거 전 최대 30일 알림을 제공하도록 현재 Marketplace 정책을 따릅니다.

## <a name="next-steps"></a>다음 단계
* [여기](https://aka.ms/rhui-update)에서 Azure Red Hat 업데이트 인프라에 대해 자세히 알아봅니다.
* 모든 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지에서 확인할 수 있습니다.
