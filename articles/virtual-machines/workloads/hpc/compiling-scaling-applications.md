---
title: HPC 응용 프로그램-Azure Virtual Machines 크기 조정 | Microsoft Docs
description: Azure Vm에서 HPC 응용 프로그램을 확장 하는 방법에 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707832"
---
# <a name="scaling-hpc-applications"></a>HPC 응용 프로그램 크기 조정

스케일 업 및 스케일 아웃 성능이 Azure에서 HPC 응용 프로그램의 성능 조정 해야 하 고 특정 워크 로드에 대 한 최적화 실험 합니다. 이 섹션 및 VM 시리즈 관련 페이지를 사용 하 여 응용 프로그램 크기 조정에 대 한 일반적인 지침을 제공 합니다.

## <a name="compiling-applications"></a>응용 프로그램 컴파일

필요 하지 않습니다 하지만 적절 한 최적화 플래그를 사용 하 여 응용 프로그램을 컴파일하 성능이 가장 좋습니다 규모 HB 및 HC 시리즈 Vm에서.

### <a name="amd-optimizing-cc-compiler"></a>C를 최적화 하는 AMD /C++ 컴파일러

AMD 최적화 C /C++ 컴파일러 (AOCC) 컴파일러 system은 높은 수준의 고급 최적화, 다중 스레딩 및 전역 최적화, 벡터화, 루프 변환 절차 간 분석을 포함 하는 프로세서 지원을 제공 하 고 코드를 생성 합니다. AOCC 컴파일러 이진 파일은 Linux 시스템 GNU C 라이브러리 (glibc) 2.17 버전 이상에 적합 합니다. C 컴파일러 suite 구성 /C++ 컴파일러 (clang), Fortran compiler (FLANG) 및 Fortran 프런트 엔드 Clang (Dragon Egg)입니다.

### <a name="clang"></a>Clang

Clang C에는 C++, 및 Objective-c로 컴파일러 전처리, 구문 분석, 최적화, 코드 생성, 어셈블리를 처리 하 고 연결 합니다. 지원 clang를 `-march=znver1` 플래그 가장을 사용 하도록 설정 하려면 코드 생성 및 튜닝 AMD의 Zen x86 기반 아키텍처.

### <a name="flang"></a>FLANG

FLANG 컴파일러 (2018 년 4 월 추가) AOCC 도구 모음에 최근에 추가 이며 현재 시험판 다운로드 하 고 테스트 하는 개발자를 위한 합니다. AMD Fortran 2008에 따라 GitHub 버전의 FLANG 확장 (https://github.com/flangcompiler/flang) 합니다. FLANG 컴파일러는 모든 Clang 컴파일러 옵션 및 FLANG 관련 컴파일러 옵션의 추가 수를 지원합니다.

### <a name="dragonegg"></a>DragonEgg

DragonEgg은 LLVM 프로젝트에서 이러한 값을 사용 하 여 GCC의 최적화 및 코드 생성기를 대체 하는 gcc 플러그 인 경우 Gcc-4.8.x AOCC 작동 함께 제공 되는 DragonEgg x86 32/x86 64 대상에 대해 테스트 되었습니다 및 다양 한 Linux 플랫폼에서 성공적으로 사용 되었습니다.

GFortran은 Fortran 프로그램 전처리, 구문 분석 하 고 GCC GIMPLE 중간 표현 (IR)를 생성 하는 의미 체계 분석에 대 한 실제 프런트 엔드입니다. DragonEgg은 GNU 플러그 인이 GFortran 컴파일 흐름에 연결 합니다. GNU 플러그 인 API를 구현합니다. 플러그 인 아키텍처를 사용 하 여 DragonEgg은 컴파일러 드라이버 컴파일의 여러 단계를 구동 됩니다.  다운로드 및 설치 지침에 따라, Dragon Egg은 사용 하 여 호출할 수 있습니다. 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 컴파일러
PGI Community Edition 버전 17은 AMD EPYC 작업할 확인 됩니다. 스트림의 PGI 컴파일된 버전을 플랫폼의 전체 메모리 대역폭을 제공할지 않습니다. 최신 Community Edition 18.10 (2018 년 11 월)가 제대로 작동 마찬가지로 해야 합니다. 다음 Intel 컴파일러를 사용 하 여 최적으로 컴파일러에 CLI 샘플은입니다.

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel 컴파일러
Intel 컴파일러 버전 18은 AMD EPYC 작업할 확인 됩니다. 다음은 Intel 컴파일러를 사용 하 여 최적으로 컴파일러에 CLI 샘플입니다.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 컴파일러 
For HPC, AMD 7.3 이상 GCC 컴파일러를 권장합니다. RHEL/CentOS 7.4, 포함 된 4.8.5 등의 이전 버전을 권장 되지 않습니다. GCC, 7.3 이상는 HPL, HPCG, 및 DGEMM 테스트 훨씬 더 높은 성능을 제공 합니다.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>애플리케이션 크기 조정 

다음 제안 사항을 효율성, 성능 및 일관성을 확장 하는 최적의 응용 프로그램에 적용 됩니다.

* Pin (것이 아니라 자동 분산 접근 방식을) 접근 순차 고정 코어 0-59 사용 하 여 처리 합니다. 
* Numa/코어/HwThread 별 바인딩은 기본 바인딩 보다 더 좋습니다.
* 하이브리드 병렬 응용 프로그램 (OpenMP + MPI)에 대 한 4 개의 스레드 및 CCX 당 1 MPI 차수를 사용 합니다.
* 순수 MPI 응용 프로그램에 대 한 최적의 성능을 위해 CCX 당 MPI의 순위가 1-4를 사용 하 여 실험 합니다.
* 일부 응용 프로그램의 극단적인 민감도 사용 하 여 메모리 대역폭 CCX 당 코어 수를 사용 하 여 도움이 될 수 있습니다. 이러한 응용 프로그램에 대 한 CCX 당 3 또는 2 개 코어를 사용 하 여 수 메모리 대역폭 경합을 줄이고 높은 실제 성능 또는 더 일관 된 확장성을 생성 합니다. 특히 MPI Allreduce이 도움이 될 수 있습니다.
* 훨씬 더 큰 크기 조정 실행에 대 한 UD 또는 하이브리드 RC + UD 전송을 사용 하는 것이 좋습니다. 많은 MPI 라이브러리/런타임 라이브러리는 내부적으로 (예: UCX 또는 MVAPICH2)이 작업을 수행 합니다. 대규모 실행 하 여 전송 구성을 확인 합니다.

## <a name="next-steps"></a>다음 단계

에 대해 자세히 알아보세요 [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure에서.
