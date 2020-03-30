---
title: Azure의 Red Hat Enterprise Linux 이미지 | Microsoft Docs
description: 마이크로소프트 Azure에서 레드 햇 엔터프라이즈 리눅스 이미지에 대해 자세히 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239869"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>레드 햇 엔터프라이즈 리눅스 이미지의 개요

이 문서에서는 Azure 마켓플레이스에서 사용 가능한 RED Hat 엔터프라이즈 Linux(RHEL) 이미지와 이름 지정 및 보존에 대한 정책에 대해 설명합니다.

모든 버전의 RHEL에 대한 Red Hat 지원 정책에 대한 자세한 내용은 [Red Hat 엔터프라이즈 Linux 수명 주기를](https://access.redhat.com/support/policy/updates/errata)참조하십시오. 가격 세부 정보는 [Azure 가격 계산기를](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)참조하십시오.

>[!IMPORTANT]
> Azure Marketplace에서 현재 사용할 수 있는 RHEL 이미지는 BYOS(사용자 고유 구독) 또는 종량제 라이선스 모델 중 하나를 지원합니다. [Azure 하이브리드 사용 혜택](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) 및 BYOS와 종량제 라이선스 간의 동적 전환은 지원되지 않습니다. 라이선스 모드를 전환하려면 해당 이미지에서 VM을 다시 배포해야 합니다.

>[!NOTE]
> Azure 마켓플레이스의 RHEL 이미지와 관련된 문제가 있는 경우 Microsoft에 지원 티켓을 제출하십시오.

## <a name="view-images-available-in-azure"></a>Azure에서 사용할 수 있는 이미지 보기

Azure Marketplace에서 "Red Hat"을 검색하거나 Azure 포털 UI에서 리소스를 만들 때 사용 가능한 모든 RHEL 이미지의 하위 집합만 표시됩니다. Azure CLI, PowerShell 및 API를 사용하여 사용 가능한 VM 이미지의 전체 집합을 언제든지 가져올 수 있습니다.

Azure에서 사용 가능한 Red Hat 이미지의 전체 집합을 보려면 다음 명령을 실행합니다.

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>명명 규칙

Azure의 VM 이미지는 게시자, 오퍼, SKU 및 버전으로 구성됩니다. 게시자:제품:SKU:버전의 조합은 이미지 URN이며 사용할 이미지를 고유하게 식별합니다.

예를 들어 `RedHat:RHEL:8-LVM:8.1.20200318` 2020년 3월 18일에 빌드된 RHEL 8.1 LVM 분할 이미지를 나타냅니다.

RHEL 8.1 VM을 만드는 방법에 대한 샘플이 여기에 나와 있습니다.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>"최신" 모니커

Azure REST API를 사용하면 특정 버전 대신 버전에 대한 모니커 "최신"을 사용할 수 있습니다. "최신" 프로비저닝을 사용하여 지정된 게시자, 제안 및 SKU에 사용할 수 있는 최신 이미지를 제공합니다.

예를 들어 `RedHat:RHEL:8-LVM:latest` 사용 가능한 최신 RHEL 8 제품군 LVM 분할 이미지를 참조합니다.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> 일반적으로 최신을 결정하는 버전의 비교는 [CompareTo 메서드](https://msdn.microsoft.com/library/a5ts8tb6.aspx)의 규칙을 따릅니다.
이 이미지 버전 비교는 값을 문자열이 아닌 [버전](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) 개체로 비교하여 수행됩니다.

## <a name="rhel-6-image-types"></a>RHEL 6 이미지 유형

RHEL 6.x 이미지의 경우 이미지 유형이 다음 표에 표시됩니다.

|게시자 | 제안 | SKU 값 | 버전 | 세부 정보
|----------|-------|-----------|---------|--------
|RedHat | RHEL | 부버전(예: 6.9) | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 6.9.2018010506) | 모든 표준 RHEL 6.x 이미지는 이 규칙을 따릅니다.
|RedHat | 렐 바이오스 | rhel-raw69 | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 6.9.201811023) | 이 이미지는 RHEL 6.9 BYOS 이미지입니다.
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 6.8.2017053118) | 이 이미지는 SAP 응용 프로그램 이미지용 RHEL 6.8입니다. SAP 응용 프로그램 리포지토리 및 기본 RHEL 리포지토리에 액세스할 수 있습니다.
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 6.7.2017053121) | 이 이미지는 SAP HANA 이미지의 RHEL 6.7입니다. SAP HANA 리포지토리 및 기본 RHEL 리포지토리에 액세스할 수 있습니다.

## <a name="rhel-7-image-types"></a>RHEL 7 이미지 유형

RHEL 7.x 이미지의 경우 몇 가지 다른 이미지 유형이 있습니다. 다음 표에서는 당사가 제공하는 다양한 이미지 집합을 보여 주실 수 있습니다. 전체 목록을 보려면 Azure CLI 명령을 `az vm image list --publisher redhat --all`사용합니다.

>[!NOTE]
> 달리 명시되지 않는 한 모든 이미지는 LVM분할되고 일반 RHEL 리포지토리에 연결됩니다. 즉, 리포지토리는 EUS(확장 업데이트 지원)가 아니며 SAP(E4S)에 대한 업데이트 서비스가 아닙니다. 앞으로 는 LVM 분할된 이미지만 게시하는 것으로 넘어가고 있지만 이 결정에 대한 피드백은 열려 있습니다. SAP에 대한 확장 업데이트 지원 및 업데이트 서비스에 대한 자세한 내용은 [Red Hat 엔터프라이즈 Linux 수명 주기를](https://access.redhat.com/support/policy/updates/errata)참조하십시오.

|게시자 | 제안 | SKU 값 | 버전 | 세부 정보
|----------|-------|------------|---------|--------
|RedHat | RHEL | 부버전(예: 7.6) | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.6.2019102813) | 2019년 4월 이전에 게시된 이미지는 표준 RHEL 리포지토리에 첨부됩니다. 2019년 4월 이후에 게시된 이미지는 특정 부버전의 버전 잠금을 허용하기 위해 Red Hat의 EUS 리포지토리에 첨부됩니다. 일반 리포지토리를 원하는 고객은 SKU 값에 7-LVM 또는 7-RAW가 포함된 이미지를 사용해야 합니다(자세한 내용은 다음). RHEL 7.7 이후 의 이미지는 LVM 분할됩니다. 이 범주의 다른 모든 이미지는 원시 분할됩니다.
|RedHat | RHEL | 7-RAW | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.6.2019102813) | 이러한 이미지는 원시 분할(예: 논리적 볼륨이 추가되지 않았습니다).
|RedHat | RHEL | 7-RAW-CI | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.6.2019072418) | 이러한 이미지는 원시 분할(예: 논리적 볼륨이 추가되지 않은 경우)이며 프로비저닝에 클라우드 init을 사용합니다.
|RedHat | RHEL | 7-LVM | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.6.2019062414) | 이러한 이미지는 LVM 분할됩니다.
|RedHat | 렐 바이오스 | rhel-{lvm, 원시} | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.7.20190819) | 이러한 이미지는 RHEL 7 BYOS 이미지입니다. 리포지토리에 부착되지 않으며 RHEL 프리미엄 요금이 부과되지 않습니다. RHEL BYOS 이미지에 관심이 있다면 [액세스를 요청하십시오.](https://aka.ms/rhel-byos) SKU 값은 부 버전으로 끝나고 이미지가 원시인지 LVM분할인지를 나타냅니다. 예를 들어, rhel-lvm77의 SKU 값은 LVM 분할된 RHEL 7.7 이미지를 나타냅니다.
|RedHat | RHEL | RHEL-SAP | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.6.2019071300) | 이러한 이미지는 SAP 이미지용 RHEL입니다. SAP HANA 및 응용 프로그램 리포지토리와 RHEL E4S 리포지토리에 액세스할 수 있습니다. 청구에는 기본 계산 요금에 대한 RHEL 프리미엄 및 SAP 프리미엄이 포함됩니다.
|RedHat | RHEL | RHEL-SAP-HA | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.6.2019062320) | 이러한 이미지는 고가용성 및 업데이트 서비스 이미지가 있는 SAP용 RHEL입니다. SAP HANA 및 응용 프로그램 리포지토리와 고가용성 리포지토리, RHEL E4S 리포지토리에 액세스할 수 있습니다. 청구에는 기본 계산 요금 외에도 RHEL 프리미엄, SAP 프리미엄 및 고가용성 프리미엄이 포함됩니다.
|RedHat | RHEL | RHEL-HA | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.6.2019062019) | 이러한 이미지는 고가용성 추가 기능에도 액세스할 수 있는 RHEL 이미지입니다. 그들은 고가용성 추가 프리미엄으로 인해 RHEL 및 기본 계산 요금의 상단에 약간 추가 요금을 부과합니다.
|RedHat | RHEL | RHEL-SAP-APPS | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.3.2017053118) | SAP 응용 프로그램 및 SAP HANA 리포지토리가 SAP 리포지토리에 결합되었기 때문에 이러한 이미지가 오래되었습니다. 이러한 이미지는 SAP 응용 프로그램 이미지용 RHEL입니다. SAP 응용 프로그램 리포지토리 및 기본 RHEL 리포지토리에 액세스할 수 있습니다.
|RedHat | RHEL | RHEL-SAP-HANA | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 7.3.2018051421) | SAP 응용 프로그램 및 SAP HANA 리포지토리가 SAP 리포지토리에 결합되었기 때문에 이러한 이미지가 오래되었습니다. 이러한 이미지는 SAP HANA 이미지용 RHEL입니다. SAP HANA 리포지토리 및 기본 RHEL 리포지토리에 액세스할 수 있습니다.

## <a name="rhel-8-image-types"></a>RHEL 8 이미지 유형

RHEL 8 이미지 유형에 대한 자세한 내용은 다음과 같습니다.

|게시자 | 제안 | SKU 값 | 버전 | 세부 정보
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 8.0.20191023) | 이러한 이미지는 표준 Red Hat 리포지토리에 연결된 RHEL 8 LVM 분할 이미지입니다.
|RedHat | RHEL | 8세대2 | RHEL 마이너 버전과 게시된 날짜의 통합 값(예: 8.0.20191024) | 이러한 이미지는 표준 Red Hat 저장소에 연결된 하이퍼 V 세대 2 RHEL 8 LVM 분할 이미지입니다. Azure의 2세대 VM에 대한 자세한 내용은 [Azure의 2세대 VM 에 대한 지원을](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)참조하십시오.

## <a name="rhel-longer-support-add-ons"></a>RHEL 더 이상 지원 추가 기능

### <a name="extended-update-support"></a>확장 업데이트 지원

2019년 4월부터 기본적으로 EUS 리포지토리에 첨부된 RHEL 이미지를 사용할 수 있습니다. RHEL EUS에 대한 자세한 내용은 [Red Hat 설명서에서](https://access.redhat.com/articles/rhel-eus)확인할 수 있습니다.

EUS 리포지토리로 전환할 수 있으며 지원됩니다. VM을 EUS로 전환하는 방법에 대한 지침과 EUS 지원 단종 날짜에 대한 자세한 내용은 [RHEL EUS 및 버전 잠금 RHEL VM을](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)참조하십시오.

>[!NOTE]
> EUS는 RHEL 엑스트라에서 지원되지 않습니다. 즉, RHEL Extras 채널에서 일반적으로 사용할 수 있는 패키지를 설치하는 경우 EUS에 있는 동안에는 패키지를 설치할 수 없습니다. Red Hat Extras 제품 수명 주기에 대한 자세한 내용은 [Red Hat 엔터프라이즈 Linux 엑스트라 수명 주기를](https://access.redhat.com/support/policy/updates/extras/)참조하십시오.

#### <a name="differentiate-between-regular-and-eus-images"></a>일반 이미지와 EUS 이미지 구분

EUS 리포지토리에 첨부된 이미지를 사용하려는 고객은 SKU에 RHEL 부버전 번호가 포함된 RHEL 이미지를 사용해야 합니다.

예를 들어 다음 두 개의 RHEL 7.4 이미지를 사용할 수 있습니다.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

이 경우 `RedHat:RHEL:7.6:7.6.2019102813` 기본적으로 EUS 리포지토리에 연결됩니다. SKU 값은 7.4입니다. 그리고 `RedHat:RHEL:7-LVM:7.6.2019062414` 기본적으로 비 EUS 리포지토리에 첨부됩니다. SKU 값은 7-LVM입니다.

일반(EUS가 아닌) 리포지토리를 사용하려면 SKU에 부 버전 번호가 포함되지 않은 이미지를 사용합니다.

#### <a name="rhel-images-with-eus"></a>EUS와 RHEL 이미지

다음 표의 정보는 EUS 리포지토리에 연결된 RHEL 이미지에 적용됩니다.

>[!NOTE]
> 작성 당시에는 RHEL 7.4 및 이후의 마이너 버전만 EUS를 지원합니다. EUS는 RHEL <=7.3에 대해 더 이상 지원되지 않습니다.
>
> RHEL EUS 가용성에 대한 자세한 내용은 [Red Hat 엔터프라이즈 Linux 수명 주기를](https://access.redhat.com/support/policy/updates/errata)참조하십시오.

부 버전 |EUS 이미지 예              |EUS 상태                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |레드햇:RHEL:7.4:7.2019041718 | 2019년 4월 이후에 게시된 이미지는 기본적으로 EUS입니다.|
RHEL 7.5      |레드햇:RHEL:7.5:7.5.2019060305 | 2019년 6월 이후에 게시된 이미지는 기본적으로 EUS입니다. |
RHEL 7.6      |레드햇:RHEL:7.6:7.2019052206 | 2019년 5월 이후에 게시된 이미지는 기본적으로 EUS입니다. |
RHEL 8.0      |해당 없음                            | 레드햇에는 EUS가 없습니다.                               |

### <a name="update-services-for-sap"></a>SAP용 업데이트 서비스

SAP 이미지에 대한 최신 RHEL은 SAP 솔루션 구독(E4S)에 대한 업데이트 서비스에 연결됩니다. E4S에 대한 자세한 내용은 Red Hat [문서를](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)참조하십시오.

#### <a name="rhel-images-with-e4s"></a>E4S를 가진 RHEL 심상

2019년 12월 이후에 생성된 다음 혜택의 이미지는 E4S 리포지토리에 연결됩니다.

* RHEL-SAP (SAP용 RHEL)
* RHEL-SAP-HA(고가용성 및 업데이트 서비스를 제공하는 SAP용 RHEL)

## <a name="other-available-offers-and-skus"></a>기타 이용 가능한 혜택 및 SUS

사용 가능한 오퍼 및 SCO의 전체 목록에는 이전 표에 나열된 것 이상의 추가 이미지가 포함될 수 있습니다. 예제는 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`입니다. 이러한 오퍼는 특정 마켓플레이스 솔루션에 대한 지원을 제공하는 데 사용될 수 있습니다. 또는 미리 보기 및 테스트 목적으로 게시할 수 있습니다. 경고 없이 언제든지 변경하거나 제거할 수 있습니다. Microsoft 또는 Red Hat에서 해당 존재를 공개적으로 문서화하지 않는 한 사용하지 마십시오.

## <a name="publishing-policy"></a>정책 게시

특정 공통 취약점 및 노출(C베세)을 해결하거나 가끔 구성 변경 또는 업데이트를 위해 필요에 따라 새 부 버전이 릴리스될 때 Microsoft 및 Red Hat은 이미지를 업데이트합니다. CVE 수정 프로그램의 릴리스 또는 가용성이후에 영업일 3일 이내에 가능한 한 빨리 업데이트된 이미지를 제공하기 위해 노력합니다.

지정된 이미지 패밀리에서 현재 부 릴리스만 업데이트합니다. 최신 부 버전 릴리스로 이전 부 버전 업데이트를 중지합니다. 예를 들어 RHEL 7.6이 릴리스되면 RHEL 7.5 이미지가 더 이상 업데이트되지 않습니다.

>[!NOTE]
> RHEL 종량제 이미지에서 프로비전된 활성 Azure VM은 Azure RHUI에 연결되며 Red Hat에서 릴리스되고 Azure RHUI로 복제되는 즉시 업데이트 및 수정 프로그램을 받을 수 있습니다. 타이밍은 일반적으로 레드 햇의 공식 출시 후 24 시간 미만입니다. 이러한 VM은 업데이트를 받기 위해 게시된 새 이미지가 필요하지 않습니다. 고객은 업데이트를 시작할 시기를 완전히 제어할 수 있습니다.

## <a name="image-retention-policy"></a>이미지 보존 정책

현재 정책은 이전에 게시된 모든 이미지를 유지하는 것입니다. 모든 종류의 문제를 일으키는 것으로 알려진 이미지를 제거할 권리를 보유합니다. 예를 들어 후속 플랫폼 또는 구성 요소 업데이트로 인해 구성이 잘못 된 이미지가 제거될 수 있습니다. 제거될 수 있는 이미지는 현재 Azure Marketplace 정책을 따라 이미지 제거 최대 30일 전에 알림을 제공합니다.

## <a name="next-steps"></a>다음 단계

* Azure에서 RHEL 이미지의 전체 목록을 보려면 [Azure에서 사용할 수 있는 RED Hat 엔터프라이즈 Linux(RHEL) 이미지를](./redhat-imagelist.md)참조하십시오.
* Azure Red Hat 업데이트 인프라에 대한 자세한 내용은 [Azure의 주문형 RHEL VM에 대한 Red Hat 업데이트 인프라를](https://aka.ms/rhui-update)참조하십시오.
* RHEL BYOS 제공에 대한 자세한 내용은 [Azure에서 Red Hat Enterprise Linux](./byos.md)가져오기 사용자 고유의 구독 골드 이미지를 참조하십시오.
* 모든 버전의 RHEL에 대한 Red Hat 지원 정책에 대한 자세한 내용은 [Red Hat 엔터프라이즈 Linux 수명 주기를](https://access.redhat.com/support/policy/updates/errata)참조하십시오.
