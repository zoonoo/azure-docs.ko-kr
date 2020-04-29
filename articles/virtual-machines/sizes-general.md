---
title: Azure VM 크기-범용 | Microsoft Docs
description: Azure의 가상 컴퓨터에 사용할 수 있는 다양 한 범용 크기를 나열 합니다. 이 시리즈의 크기에 대 한 저장소 처리량 및 네트워크 대역폭 뿐만 아니라 vCPUs, 데이터 디스크 및 Nic의 수에 대 한 정보를 나열 합니다.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: fc263eb6fbe6c6402aaf529229bb7025f070b8d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81269672"
---
# <a name="general-purpose-virtual-machine-sizes"></a>범용 가상 머신 크기

범용 VM 크기는 적당한 CPU 대 메모리 비율을 제공합니다. 테스트 및 개발, 중소 규모 데이터베이스 및 트래픽이 적거나 중간 정도인 웹 서버에 적합합니다. 이 문서에서는 범용 컴퓨팅을 위한 제품에 대 한 정보를 제공 합니다.

- [Av2 시리즈](av2-series.md) vm은 다양 한 하드웨어 유형 및 프로세서에 배포할 수 있습니다. A 시리즈 VM은 개발과 테스트 등 항목별 작업에 가장 적합한 CPU 성능 및 메모리 구성을 갖추고 있습니다. 배포된 하드웨어에 관계없이 인스턴스 실행 시 일관된 프로세서 성능을 제공하기 위해 하드웨어에 따라 크기가 제한됩니다. 이 크기가 배포되는 실제 하드웨어를 확인하려면 Virtual Machine 내에서 가상 하드웨어를 쿼리합니다. 사용 사례로는 개발 및 테스트 서버, 트래픽이 적은 웹 서버, 중소 규모의 데이터베이스, 개념 증명, 코드 리포지토리 등이 있습니다.

  > [!NOTE]
  > A8 – A11 Vm은 3/2021에서 사용 중지 될 예정입니다. 자세한 내용은 [HPC 마이그레이션 가이드](https://azure.microsoft.com/resources/hpc-migration-guide/)를 참조 하세요.

- [B-계열의 안정적인](sizes-b-series-burstable.md) Vm은 웹 서버, 소규모 데이터베이스 및 개발 및 테스트 환경과 같이 CPU의 전체 성능이 지속적으로 필요 하지 않은 작업에 적합 합니다. 이러한 작업에는 일반적으로 버스트 가능한 성능 요구 사항이 있습니다. B 시리즈는 이러한 고객들에게 VM에서 기준 성능보다 적게 사용할 때 VM 인스턴스에서 크레딧을 축적할 수 있는 양심적인 가격 위주의 기준 성능을 갖춘 VM 크기를 구입할 수 있는 기능을 제공합니다. VM에 대한 크레딧이 적립되면 애플리케이션에 더 높은 CPU 성능이 필요할 때 VM이 CPU의 최대 100%까지 사용하여 VM의 기준 이상으로 버스트할 수 있습니다.

- [Dav4 및 Dasv4 시리즈](dav4-dasv4-series.md) 는 AMD의 2.35 GHZ의 Epyc<sup>TM</sup> 7452 프로세서를 사용 하는 새로운 크기입니다. 최대 256 mb의 l3 캐시를 사용 하는 다중 스레드 구성에서 8 개 코어 마다 해당 l3 캐시를 사용 하 여 범용 워크 로드를 실행 하는 고객 옵션을 늘립니다. Dav4 시리즈와 Dasv4 시리즈는 D & Dsv3 시리즈와 동일한 메모리 및 디스크 구성을 포함 합니다.

- [DCv2 시리즈](dcv2-series.md) 는 공용 클라우드에서 처리 되는 동안 데이터 및 코드의 기밀성과 무결성을 보호 하는 데 도움이 될 수 있습니다. 이러한 컴퓨터는 SGX 기술을 사용 하는 Intel XEON E-2288G 프로세서의 최신 세대에서 지원 됩니다. Intel 터보 부스트 기술을 사용 하 여 이러한 컴퓨터는 최대 5.0 g h z까지 갈 수 있습니다. DCv2 series 인스턴스를 사용 하면 고객이 사용 중인 코드와 데이터를 보호 하기 위해 보안 enclave 기반 응용 프로그램을 빌드할 수 있습니다.

- [Dv2 및 Dsv2 시리즈](dv2-dsv2-series.md) 원본 D 시리즈에 대 한 후속 기능을 제공 하는 Vm은 더 강력한 CPU와 메모리를 최적으로 구성 하 여 대부분의 프로덕션 워크 로드에 적합 합니다. Dv2 시리즈는 D 시리즈 보다 약 35% 더 빠릅니다. Dv2 시리즈는 intel 터보 부스트 기술 2.0을 사용 하 여 Intel® Xeon® 8171M (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 또는 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 프로세서에서 실행 됩니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

- [Dv3 및 Dsv3 시리즈](dv3-dsv3-series.md) Vm은 하이퍼 스레드 구성에서 Intel® Xeon® 8171M (Skylake), Intel® Xeon® E5-2673 v4 2.3 g h z (Broadwell) 또는 Intel® Xeon® E5-2673 v3 2.4 g h z (Haswell) 프로세서에서 실행 되므로 대부분의 범용 작업에 대해 더 나은 가치를 제공 합니다. 하이퍼 스레딩으로 이동하기 위해 디스크 및 네트워크 제한이 코어 단위로 조정되는 동안 메모리가 확장되었습니다(~ 3.5GiB/vCPU에서 4GiB/vCPU로). Dv3 시리즈는 더 이상 D/Dv2 시리즈의 높은 메모리 VM 크기를 갖지 않으며 메모리 최적화 [Ev3 및 Esv3 시리즈](ev3-esv3-series.md)로 이동 되었습니다.

예제 D 시리즈 사용 사례에는 엔터프라이즈급 응용 프로그램, 관계형 데이터베이스, 메모리 내 캐싱 및 분석이 포함 됩니다.

## <a name="other-sizes"></a>기타 크기

- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
