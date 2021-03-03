---
title: NP 시리즈-Azure Virtual Machines
description: NP 시리즈 Vm에 대 한 사양입니다.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 4fe6df5a9eabc6e5088618039fdea450ee8314ce
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693852"
---
# <a name="np-series-preview"></a>NP 시리즈 (미리 보기) 
NP 시리즈 가상 머신은 기계 학습 유추, 비디오 트랜스 코딩 및 데이터베이스 검색 & 분석을 비롯 한 워크 로드를 가속화 하기 위해 [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) fpgas 구동 됩니다. NP 시리즈 Vm은 모든 코어 터보 클록 속도 3.2 g h z를 사용 하 여 Intel Xeon 8171M (Skylake) Cpu로도 제공 됩니다.

소개 시리즈 미리 보기 프로그램의 일부가 될 [미리 보기 양식을](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR9x_QCQkJXxHl4qOI4jC9YtUOVI0VkgwVjhaTFFQMTVBTDFJVFpBMzJSSCQlQCN0PWcu) 사용 하 여 요청을 제출 합니다.


[Premium Storage](premium-storage-performance.md): 지원 됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원 됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원 되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원 되지 않음<br>
VM 생성 지원: 1 세대<br>
[가속 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원 됨<br>
[삭제 되는 OS 디스크](ephemeral-os-disks.md): 지원 되지 않음 <br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | FPGA | FPGA memory: GiB | 최대 데이터 디스크 수 | 최대 Nic/예상 네트워크 대역폭 (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버
[Xilinx Runtime (XRT) 릴리스 정보](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) 를 방문 하 여 지원 되는 운영 체제의 전체 목록을 확인 하세요.

미리 보기 프로그램 Microsoft Azure 엔지니어링 팀은 드라이버 설치에 대 한 특정 지침을 공유 합니다.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
