---
title: "Azure Migrate로 규모 검색 및 평가 | Microsoft Docs"
description: "Azure Migrate 서비스를 사용하는 많은 수의 온-프레미스 컴퓨터를 평가하는 방법을 설명합니다."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dde0d07f-94b7-4b6a-a158-a89aa9324a35
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 930ec182cf329e7dda072dc49bd7f70abb413f2d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>대규모 VMware 환경 검색 및 평가

이 문서에서는 [Azure Migrate](migrate-overview.md)를 사용하는 많은 수의 온-프레미스 컴퓨터를 평가하는 방법을 설명합니다. Azure Migrate는 컴퓨터를 평가하여 Azure로의 마이그레이션에 적합한지, Azure에서 컴퓨터를 실행하기 위해 크기 조정 및 비용 예상안을 제공하는지 여부를 확인합니다.

## <a name="prerequisites"></a>필수 조건

- **VMware**: ESXi 호스트 또는 버전 5.0 이상을 실행하는 클러스터에 있는 VMware VM이 하나 이상 필요합니다. 호스트 또는 클러스터는 버전 5.5 또는 6.0을 실행하는 vCenter 서버에서 관리되어야 합니다.
- **vCenter 계정**: vCenter 서버에 대한 관리자 자격 증명이 있는 읽기 전용 계정이 있어야 합니다. Azure Migrate는 이 계정을 사용하여 VM을 검색합니다.
- **사용 권한**: vCenter 서버에서 파일을 .OVA 형식으로 가져와 VM을 만들 수 있는 권한이 필요합니다.
- **통계 설정**: 배포를 시작하기 전에 vCenter 서버에 대한 통계 설정을 수준 2 이상으로 설정해야 합니다.

## <a name="plan-azure-migrate-projects"></a>Azure Migrate 프로젝트 계획

Azure Migrate 프로젝트는 최대 1500대 컴퓨터를 평가할 수 있습니다. 프로젝트에서 단일 검색은 최대 1000대 컴퓨터를 검색할 수 있습니다.

- 검색할 컴퓨터가 1000대 미만인 경우 단일 검색을 사용하는 단일 프로젝트가 필요합니다.
- 컴퓨터가 1000 ~ 1500대 사이인 경우 두 번 검색을 사용하는 단일 프로젝트가 필요합니다.
- 컴퓨터가 1500대 이상인 경우 필요한 요구 사항에 따라 여러 프로젝트를 만들고 여러 검색을 수행해야 합니다. 예:
    - 컴퓨터가 3000대인 경우 두 개의 검색을 사용한 두 개의 프로젝트 또는 단일 검색을 사용한 세 개의 프로젝트를 설정할 수 있습니다.
    - 컴퓨터가 5000대인 경우 4개의 프로젝트를 설정할 수 있습니다. 1500대 컴퓨터 검색을 사용한 프로젝트 2개 및 500대 컴퓨터 검색을 사용한 프로젝트 하나. 또는 각각 단일 검색을 사용한 프로젝트 5개를 설정할 수도 있습니다. 
- Azure Migrate에서 검색 작업을 수행하는 경우 검색 범위를 VMware 폴더, 데이터 센터 또는 클러스터로 설정할 수 있습니다.
- 둘 이상의 검색을 수행하려면 vCenter에서 검색할 VM이 1000대 컴퓨터 제한을 지원하는 폴더, 데이터 센터 또는 클러스터에 있는지 확인합니다.
- 평가 목적을 위해 동일한 프로젝트 및 평가 내에서 컴퓨터 간 상호종속성을 유지하는 것이 좋습니다. 따라서 vCenter에서 종속 컴퓨터가 평가 목적을 위한 동일한 폴더, 데이터 센터 또는 클러스터에 있는지 확인합니다.


## <a name="create-a-project"></a>프로젝트 만들기

요구 사항에 따라 Azure Migrate 프로젝트를 만듭니다.

1. Azure Portal에서 **리소스 만들기**를 클릭합니다.
2. **Azure Migrate**를 검색하고 검색 결과에서 서비스(**Azure Migrate(미리 보기)**를 선택합니다. 그런 다음 **Create**를 클릭합니다.
3. 프로젝트 이름과 프로젝트에 대한 Azure 구독을 지정합니다.
4. 새 리소스 그룹을 만듭니다.
5. 프로젝트를 만들 영역을 지정한 다음, **만들기**를 클릭합니다. 온-프레미스 VM에서 수집된 메타데이터는 이 영역에 저장됩니다.

## <a name="set-up-the-collector-appliance"></a>수집기 어플라이언스 설정

Azure Migrate는 수집기 어플라이언스라고 하는 온-프레미스 VM을 만듭니다. 이 VM은 온-프레미스 VMware VM을 검색하고 Azure Migrate 서비스에 대한 메타데이터를 보냅니다. 수집기 어플라이언스를 설정하려면 .OVA 파일을 다운로드하고 VM을 만드는 온-프레미스 vCenter 서버로 가져옵니다.

### <a name="download-the-collector-appliance"></a>수집기 어플라이언스 다운로드

프로젝트가 여러 개인 경우 수집기 어플라이언스는 vCenter 서버에 한 번만 다운로드하면 됩니다. 어플라이언스를 다운로드하여 설정한 후 각 프로젝트에 대해 실행하고 고유한 프로젝트 ID와 키를 지정합니다.

1. Azure Migrate 프로젝트에서 **시작** > **검색 및 평가** > **컴퓨터 검색**을 클릭합니다.
2. **컴퓨터 검색**에서 **다운로드**를 클릭하여 .OVA 파일을 다운로드합니다.
3. **프로젝트 자격 증명 복사**에서 프로젝트에 대한 ID 및 키를 복사합니다. 수집기를 구성하는 경우 필요합니다.

   
### <a name="verify-the-collector-appliance"></a>수집기 어플라이언스 확인

배포하기 전에 .OVA 파일이 안전한지 확인합니다.

1. 파일을 다운로드하는 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 OVA에 대한 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 생성된 해시는 이러한 설정에 일치해야 합니다.

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>수집기 VM 만들기

vCenter 서버에 다운로드한 파일을 가져옵니다.

1. vSphere 클라이언트 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.

    ![OVF 배포](./media/how-to-scale-assessment/vcenter-wizard.png)

2. OVF 템플릿 배포 마법사 > **원본**에서 .ova 파일의 위치를 지정합니다.
3. **이름** 및 **위치**에서 수집기 VM에 사용할 친숙한 이름 및 VM이 호스트되는 인벤토리 개체를 지정합니다.
5. **호스트/클러스터**에서 수집기 VM을 실행할 호스트 또는 클러스터를 지정합니다.
7. 저장소에서 수집기 VM에 대한 저장소 대상을 지정합니다.
8. **디스크 형식**에서 디스크 유형 및 크기를 지정합니다.
9. **네트워크 매핑**에서 수집기 VM을 연결할 네트워크를 지정합니다. 네트워크는 Azure에 메타데이터를 전송하기 위해 인터넷에 연결되어 있어야 합니다. 
10. 설정을 검토 및 확인한 후 **마침**을 클릭합니다.

## <a name="identify-the-key-and-id-for-each-project"></a>각 프로젝트에 대한 키와 ID 식별

프로젝트가 여러 개인 경우 각 프로젝트에 대한 ID 및 키를 식별했는지 확인합니다. VM을 검색하기 위해 수집기를 실행하는 경우 키가 필요합니다.

1. 프로젝트에서 **시작** > **검색 및 평가** > **컴퓨터 검색**을 클릭합니다.
2. **프로젝트 자격 증명 복사**에서 프로젝트에 대한 ID 및 키를 복사합니다. 
    ![프로젝트 ID](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>성능 카운터 수집을 위한 vCenter 통계 수준
다음은 검색하는 동안 수집되는 카운터 목록입니다. 이러한 카운터는 기본적으로 vCenter 서버의 다양한 수준에서 사용할 수 있습니다. 모든 카운터는 올바르게 수집되도록 통계 수준에 대한 가장 높은 일반적인 수준(수준 3)을 설정하는 것이 좋습니다. vCenter가 낮은 수준으로 설정된 경우 몇 가지 카운터만 완벽히 수집되고 나머지는 0으로 설정됩니다. 따라서 평가는 불완전한 데이터를 표시합니다. 아래 표에 특정 카운터가 수집되지 않은 경우 영향을 받게 되는 평가 결과가 나열되어 있습니다.

|카운터                                  |Level    |장치 수준당  |영향 평가                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |해당 없음                |권장되는 VM 크기 및 비용                    |
|mem.usage.average                        | 1       |해당 없음                |권장되는 VM 크기 및 비용                    |
|virtualDisk.read.average                 | 2       |2                 |디스크 크기, 저장소 비용 및 VM 크기         |
|virtualDisk.write.average                | 2       |2                 |디스크 크기, 저장소 비용 및 VM 크기         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |디스크 크기, 저장소 비용 및 VM 크기         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |디스크 크기, 저장소 비용 및 VM 크기         |
|net.received.average                     | 2       |3                 |VM 크기 및 네트워크 비용                        |
|net.transmitted.average                  | 2       |3                 |VM 크기 및 네트워크 비용                        |

> [!WARNING]
> 방금 더 높은 통계 수준을 설정한 경우 성능 카운터를 생성하는 데 하루가 걸립니다. 따라서 하루 뒤에 검색을 실행하는 것이 좋습니다.

## <a name="run-the-collector-to-discover-vms"></a>수집기를 실행하여 VM 검색

수행해야 하는 각 검색마다 수집기를 실행하여 필요한 범위에서 VM을 검색합니다. 검색을 차례로 실행합니다. 동시 검색은 지원되지 않으며, 각 검색은 다른 범위에 있어야 합니다.

1. vSphere 클라이언트 콘솔에서 VM을 마우스 오른쪽 단추로 클릭하고 **콘솔 열기**를 클릭합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 기본 암호를 제공합니다.
3. 바탕 화면에서 **수집기 실행** 바로 가기를 클릭합니다.
4. Azure Migrate Collector에서 **필수 구성 요소 설정**을 엽니다.
    - 사용 조건에 동의하고 타사 정보를 읽습니다.
    - 수집기는 VM에 인터넷 액세스 권한이 있는지 확인합니다.
    - VM이 프록시를 통해 인터넷에 액세스하는 경우 **프록시 설정**을 클릭하고 프록시 주소 및 수신 대기 포트를 지정합니다. 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
    - 수집기는 Windows 프로파일러 서비스가 실행 중인지 확인합니다. 서비스는 수집기 VM에 기본적으로 설치됩니다.
    - VMware PowerCLI를 다운로드하여 설치합니다.
. **컴퓨터 검색**에서 다음을 수행합니다.
    - vCenter 서버의 이름(FQDN) 또는 IP 주소를 지정합니다.
    - **사용자 이름** 및 **암호**에서 수집기가 vCenter 서버에서 VM을 검색하는 데 사용하는 읽기 전용 계정 자격 증명을 지정합니다.
    - **컬렉션 범위**에서 VM 검색에 대한 범위를 선택합니다. 수집기는 지정된 범위 내에 있는 VM만 검색할 수 있습니다. 범위는 특정 폴더, 데이터 센터 또는 클러스터로 설정할 수 있습니다. VM 수는 1000개 미만이어야 합니다. 
    - n **그룹화에 대한 태그 범주**에서 **없음**을 선택합니다.

        ![범위 선택](./media/how-to-scale-assessment/select-scope.png)

1. **프로젝트 선택**에서 프로젝트에 대한 ID 및 키를 지정합니다. 복사하지 않은 경우 수집기 VM에서 Azure Portal을 엽니다. 프로젝트 **개요** 페이지에서 **컴퓨터 검색**을 클릭하고 값을 복사합니다.  
**전체 검색**에서 검색 프로세스를 모니터링하여 VM에서 수집된 메타데이터가 범위에 있는지 확인합니다. 수집기는 대략적인 검색 시간을 제공합니다.


### <a name="verify-vms-in-the-portal"></a>포털에서 VM 확인

검색 시간은 검색 중인 VM 수에 따라 다릅니다. 일반적으로 VM 100대의 경우, 수집기가 실행을 완료한 후 검색을 완료하기까지 약 1시간이 걸립니다. 

1. 마이그레이션 플래너 프로젝트에서 **관리** > **컴퓨터**를 클릭합니다.
2. 검색하려는 VM이 포털에 표시되는지 확인합니다.


## <a name="next-steps"></a>다음 단계

- 평가를 위해 [그룹 만들기](how-to-create-a-group.md) 방법을 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).