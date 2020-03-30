---
title: 메인프레임 저장소를 Azure 저장소로 이동
description: 확장성이 큰 Azure 스토리지 리소스는 메인프레임 기반 조직이 IBM z14 애플리케이션을 마이그레이션하고 현대화하는 데 도움이 될 수 있습니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288917"
---
# <a name="move-mainframe-storage-to-azure"></a>메인프레임 저장소를 Azure로 이동

Microsoft Azure에서 메인프레임 워크로드를 실행하려면 메인프레임의 기능이 Azure와 어떻게 비교되는지 알아야 합니다. 확장성이 큰 스토리지 리소스는 조직이 의존하는 응용 프로그램을 포기하지 않고 현대화를 시작하는 데 도움이 될 수 있습니다.

Azure는 IBM z14 기반 시스템(이 필기현재 모델)과 유사한 메인프레임과 같은 기능 및 저장소 용량을 제공합니다. 이 문서에서는 Azure에서 비교 가능한 결과를 얻는 방법을 설명합니다.

## <a name="mainframe-storage-at-a-glance"></a>메인프레임 스토리지 한눈에 보기

IBM 메인프레임은 두 가지 방법으로 스토리지를 특성화합니다. 첫 번째는 직접 액세스 저장 장치(DASD)입니다. 두 번째는 순차적 저장입니다. 스토리지 관리를 위해 메인프레임은 DFSMS(데이터 시설 저장소 관리 하위 시스템)를 제공합니다. 다양한 저장 장치에 대한 데이터 액세스를 관리합니다.

[DASD는](https://en.wikipedia.org/wiki/Direct-access_storage_device) 데이터의 직접 액세스에 고유한 주소를 사용할 수 있도록 하는 보조(메모리 내) 저장소를 위한 별도의 장치를 말합니다. 원래 DASD라는 용어는 회전 디스크, 자기 드럼 또는 데이터 셀에 적용됩니다. 그러나 이제 이 용어는 솔리드 스테이트 스토리지 장치(SSD), 저장 영역 네트워크(SA), 네트워크 연결 스토리지(NAS) 및 광학 드라이브에도 적용될 수 있습니다. 이 문서에서 DASD는 회전 디스크, SA 및 SSD를 나타냅니다.

DASD 스토리지와 달리 메인프레임의 순차적 스토리지는 시작 지점에서 데이터에 액세스한 다음 한 줄로 읽거나 쓰는 테이프 드라이브와 같은 장치를 말합니다.

저장 장치는 일반적으로 파이버 커넥션(FICON)을 사용하여 연결되거나 하이퍼바이저가 있는 서버의 파티션 간 고속 통신을 위한 IBM 기술인 [HiperSockets를](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)사용하여 메인프레임의 IO 버스에 직접 액세스됩니다.

대부분의 메인프레임 시스템은 스토리지를 두 가지 유형으로 구분합니다.

- *일상적인* 작업에는 온라인 저장소(핫 스토리지라고도 도는)가 필요합니다. DASD 스토리지는 일반적으로 이 목적을 위해 사용됩니다. 그러나 일일 테이프 백업(논리적 또는 물리적)과 같은 순차적 저장소도 이러한 용도로 사용할 수 있습니다.

- *아카이브 스토리지(콜드* 스토리지라고도 함)는 지정된 시간에 장착될 수 없습니다. 대신 필요에 따라 탑재되고 액세스됩니다. 아카이브 스토리지는 종종 스토리지를 위해 순차적 테이프 백업(논리적 또는 물리적)을 사용하여 구현됩니다.

## <a name="mainframe-versus-io-latency-and-iops"></a>메인프레임 대 IO 대기 시간 및 IOPS

메인프레임은 고성능 IO와 낮은 IO 대기 시간이 필요한 응용 프로그램에 자주 사용됩니다. IO 장치 및 HiperSockets에 대한 FICON 연결을 사용하여 이 작업을 수행할 수 있습니다. HiperSockets를 사용하여 응용 프로그램과 장치를 메인프레임의 IO 채널에 직접 연결하면 마이크로초의 대기 시간을 달성할 수 있습니다.

## <a name="azure-storage-at-a-glance"></a>Azure 스토리지 한눈에 보기

스토리지에 대한 Azure 서비스형[인프라(IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)옵션은 유사한 메인프레임 용량을 제공합니다.

Microsoft는 Azure에서 호스팅되는 응용 프로그램에 대해 페타바이트 규모의 저장소를 제공하며 몇 가지 저장소 옵션이 있습니다. 고성능 SSD 스토리지부터 대용량 스토리지 및 아카이브를 위한 저가형 Blob 스토리지까지 다양합니다. 또한 Azure는 메인프레임 환경에서 설정하는 데 더 많은 노력이 필요한 저장소에 대한 데이터 중복 옵션을 제공합니다.

Azure 저장소는 다음 표에 요약된 대로 [Azure 디스크,](/azure/virtual-machines/windows/managed-disks-overview) [Azure 파일](/azure/storage/files/storage-files-introduction)및 [Azure Blob으로](/azure/storage/blobs/storage-blobs-overview) 사용할 수 있습니다. 각 을 [사용할 시기에](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)대해 자세히 알아봅니다.

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Description</th><th>다음을 수행할 때 사용하십시오.</th></tr>
</thead>
<tbody>
<tr><td>Azure 파일
</td>
<td>
SMB 인터페이스, 클라이언트 라이브러리 및 저장된 파일에 어디서나 액세스할 수 있는 <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> 인터페이스를 제공합니다.
</td>
<td><ul>
<li>응용 프로그램이 네이티브 파일 시스템 API를 사용하여 Azure에서 실행중인 다른 응용 프로그램과 데이터를 공유하는 경우 응용 프로그램을 클라우드로 이동합니다.</li>
<li>많은 VM에서 액세스해야 하는 개발 및 디버깅 도구를 저장합니다.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>클라이언트 라이브러리와 구조화되지 않은 데이터를 블록 Blob의 대규모로 저장하고 액세스할 수 있는 <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> 인터페이스를 제공합니다. 엔터프라이즈 빅 데이터 분석 솔루션을 위한 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a>도 지원합니다.
</td>
<td><ul>
<li>응용 프로그램에서 스트리밍 및 임의 액세스 시나리오를 지원합니다.</li>
<li>어디서나 응용 프로그램 데이터에 액세스할 수 있습니다.</li>
<li>Azure에서 엔터프라이즈 데이터 레이크를 구축하고 빅 데이터 분석을 수행합니다.</li>
</ul></td>
</tr>
<tr><td>Azure 디스크
</td>
<td>연결된 가상 하드 디스크에서 데이터를 지속적으로 저장하고 액세스할 수 있는 클라이언트 라이브러리와 <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> 인터페이스를 제공합니다.
</td>
<td><ul>
<li>네이티브 파일 시스템 API를 사용하여 영구 디스크에 데이터를 읽고 쓰는 응용 프로그램을 들어 올리고 이동합니다.</li>
<li>디스크가 연결된 VM 외부에서 액세스할 필요가 없는 데이터를 저장합니다.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 핫(온라인) 및 콜드(아카이브) 저장소

지정된 시스템의 저장소 유형은 저장소 크기, 처리량 및 IOPS를 비롯한 시스템의 요구 사항에 따라 달라집니다. 메인프레임의 DASD 형식 저장소의 경우 Azure의 응용 프로그램은 일반적으로 Azure Disks 드라이브 저장소를 대신 사용합니다. 메인프레임 아카이브 스토리지의 경우 Azure에서 Blob 저장소가 사용됩니다.

SSD는 Azure에서 최고의 저장소 성능을 제공합니다. 다음 옵션을 사용할 수 있습니다(이 문서 작성 시)

| Type         | 크기           | IOPS                  |
|--------------|----------------|-----------------------|
| 울트라 SSD    | 4GB ~ 64TB  | 1,200 ~ 160,000 IOPS |
| 프리미엄 SSD  | 32GB ~ 32TB | 12 ~ 15,000 IOPS     |
| 표준 SSD | 32GB ~ 32TB | 12 ~ 2,000 IOPS      |

Blob 저장소는 Azure에서 가장 큰 저장소 볼륨을 제공합니다. Azure는 저장소 크기 외에도 관리되는 저장소와 관리되지 않는 저장소를 모두 제공합니다. 관리되는 저장소를 사용하면 Azure에서 기본 저장소 계정 관리를 처리합니다. 관리되지 않는 저장소를 사용하면 사용자는 저장소 요구 사항을 충족하기 위해 적절한 크기의 Azure 저장소 계정을 설정하는 책임을 집니다.

## <a name="next-steps"></a>다음 단계

- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 가상 머신에서 메인프레임 다시 호스팅](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [메인프레임 계산을 Azure로 이동](mainframe-compute-Azure.md)
- [Azure Blob, Azure 파일 또는 Azure 디스크를 사용할 시기 결정](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Azure VM 워크로드에 대한 표준 SSD 관리 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM 리소스

- [IBM Z의 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 및 커플링 기능: 기본 사항 이상](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 순수 규모 기능 설치에 필요한 사용자 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 인스턴스 명령 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 퓨어스케일 클러스터된 데이터베이스 솔루션](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [메인프레임 응용 프로그램에 대한 마이크로 소프트 Azure 정부 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [마이크로소프트와 페드램](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>더 많은 마이그레이션 리소스

- [Azure Virtual Data Center 리프트 앤 시프트 가이드](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
