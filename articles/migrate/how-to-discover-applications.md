---
title: Azure 마이그레이션을 통해 온-프레미스 서버에서 앱, 역할 및 기능 검색
description: Azure 마이그레이션 서버 평가를 사용하여 온-프레미스 서버에서 앱, 역할 및 기능을 검색하는 방법을 알아봅니다.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453585"
---
# <a name="discover-machine-apps-roles-and-features"></a>머신 앱, 역할 및 기능 검색

이 문서에서는 Azure 마이그레이션: 서버 평가를 사용하여 온-프레미스 서버에서 응용 프로그램, 역할 및 기능을 검색하는 방법을 설명합니다.

온-프레미스 컴퓨터에서 실행되는 앱 및 역할/기능의 인벤토리를 검색하면 워크로드에 맞게 조정된 Azure로의 마이그레이션 경로를 식별하고 계획할 수 있습니다.

> [!NOTE]
> 앱 검색은 현재 VMware VM에 대해서만 미리 보기 중이며 검색전용으로 제한됩니다. 아직 앱 기반 평가를 제공하지 않습니다. 온-프레미스 VM웨어 VM, 하이퍼-VVM 및 물리적 서버에 대한 컴퓨터 기반 평가

Azure 마이그레이션을 사용한 앱 검색: 서버 평가는 에이전트가 없습니다. 컴퓨터와 VM에는 아무 것도 설치되지 않습니다. 서버 평가는 Azure 마이그레이션 어플라이언스를 사용하여 컴퓨터 게스트 자격 증명과 함께 검색을 수행합니다. 어플라이언스는 VMware API를 사용하여 VMware 컴퓨터에 원격으로 액세스합니다.


## <a name="before-you-start"></a>시작하기 전에

1. Azure 마이그레이션 프로젝트를 [만들었는지](how-to-add-tool-first-time.md) 확인합니다.
2. Azure 마이그레이션: 서버 평가 도구를 프로젝트에 [추가했는지](how-to-assess.md) 확인합니다.
4. Azure 마이그레이션 어플라이언스를 사용 하 여 VM웨어 VM을 검색 하 고 평가 하기 위한 [VM웨어 요구 사항을](migrate-support-matrix-vmware.md#vmware-requirements) 확인 합니다.
5. Azure 마이그레이션 어플라이언스 배포에 대한 [요구 사항을](migrate-appliance.md) 확인합니다.
6. 응용 프로그램 검색을 위한 [지원 및 요구 사항을 확인합니다.](migrate-support-matrix-vmware.md#application-discovery)

## <a name="prepare-for-app-discovery"></a>앱 검색 준비

1. [어플라이언스 배포를 준비합니다.](tutorial-prepare-vmware.md) 준비에는 어플라이언스 설정 확인 및 어플라이언스가 vCenter Server에 액세스하는 데 사용할 계정 설정이 포함됩니다.
2. 앱, 역할 및 기능을 검색하려는 컴퓨터에 대한 관리자 권한이 있는 사용자 계정(Windows 및 Linux 서버에 대해 하나씩)이 있는지 확인합니다.
3. 검색을 시작하려면 [Azure 마이그레이션 어플라이언스를 배포합니다.](how-to-set-up-appliance-vmware.md) 어플라이언스를 배포하려면 OVA 템플릿을 다운로드하여 VMware로 가져와 어플라이언스를 VM웨어 VM으로 만듭니다. 어플라이언스를 구성한 다음 Azure 마이그레이션에 등록합니다.
2. 어플라이언스를 배포할 때 연속 검색을 시작하려면 다음을 지정합니다.
    - 연결할 vCenter 서버의 이름입니다.
    - vCenter 서버에 연결하기 위해 어플라이언스에 대해 만든 자격 증명입니다.
    - Windows/Linux VM에 연결하기 위해 어플라이언스에 대해 만든 계정 자격 증명입니다.

어플라이언스를 배포하고 자격 증명을 제공한 후 어플라이언스는 앱, 기능 및 역할의 검색과 함께 VM 메타데이터 및 성능 데이터의 지속적인 검색을 시작합니다.  앱 검색 기간은 사용 빈도에 따라 달라집니다. 일반적으로 500개의 VM을 앱에서 검색하는 데 1시간이 걸립니다.

## <a name="review-and-export-the-inventory"></a>인벤토리 검토 및 내보내기

검색이 종료된 후 앱 검색에 대한 자격 증명을 제공한 경우 Azure Portal에서 앱 인벤토리를 검토하고 내보낼 수 있습니다.

1. **Azure 마이그레이션 - 서버** > **Azure 마이그레이션: 서버 평가,** 표시된 개수를 클릭하여 검색된 서버 페이지를 **엽니다.**

    > [!NOTE]
    > 이 단계에서는 검색된 컴퓨터에 대한 종속성 매핑을 선택적으로 설정하여 평가하려는 컴퓨터 간에 종속성을 시각화할 수도 있습니다. [자세히 알아봅니다](how-to-create-group-machine-dependencies.md).

2. **검색된 응용 프로그램에서**표시된 개수를 클릭합니다.
3. **응용 프로그램 인벤토리에서**검색된 앱, 역할 및 기능을 검토할 수 있습니다.
4. 인벤토리를 내보내려면 **검색된 서버에서** **앱 인벤토리 내보내기를**클릭합니다.

앱 인벤토리를 Excel 형식으로 내보내고 다운로드합니다. **응용 프로그램 인벤토리** 시트에는 모든 컴퓨터에서 검색된 모든 앱이 표시됩니다.

## <a name="next-steps"></a>다음 단계

- 검색된 서버의 리프트 및 시프트 마이그레이션에 대한 [평가를 만듭니다.](how-to-create-assessment.md)
- [Azure 마이그레이션: 데이터베이스 평가를](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)사용하여 SQL Server 데이터베이스 평가.
