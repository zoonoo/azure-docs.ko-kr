---
title: HPC 응용 프로그램 크기 조정-Azure Virtual Machines | Microsoft Docs
description: Azure Vm에서 HPC 응용 프로그램 크기를 조정 하는 방법을 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 2c20fcf2624c1f620b68b46e56e0602f15041348
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993827"
---
# <a name="scaling-hpc-applications"></a>HPC 응용 프로그램 크기 조정

Azure에서 HPC 응용 프로그램의 최적의 확장 및 확장 성능을 위해서는 특정 워크 로드에 대 한 성능 조정 및 최적화 실험이 필요 합니다. 이 섹션과 VM 시리즈 관련 페이지에서는 응용 프로그램 크기 조정에 대 한 일반적인 지침을 제공 합니다.

## <a name="compiling-applications"></a>응용 프로그램 컴파일

필요 하지는 않지만 적절 한 최적화 플래그를 사용 하 여 응용 프로그램을 컴파일하면 HB 및 HC 시리즈 Vm에서 최상의 확장 성능을 제공 합니다.

### <a name="amd-optimizing-cc-compiler"></a>AMD 최적화 C/c + + 컴파일러

AMD 최적화 C/c + + 컴파일러 (AOCC) 컴파일러 시스템은 전역 최적화, 벡터화, 절차적 분석, 루프 변환 및 코드 생성을 포함 하는 높은 수준의 고급 최적화, 다중 스레딩 및 프로세서 지원을 제공 합니다. AOCC 컴파일러 이진 파일은 glibc (GNU C Library) 버전 2.17 이상이 있는 Linux 시스템에 적합 합니다. 컴파일러 제품군은 C/c + + 컴파일러 (clang), 포트란 컴파일러 (FLANG) 및 포트란 front end to Clang (드래곤)로 구성 됩니다.

### <a name="clang"></a>Clang

Clang는 C, c + + 및 목표-C 컴파일러 처리 전처리, 구문 분석, 최적화, 코드 생성, 어셈블리 및 연결입니다. Clang는  `-march=znver1` AMD의 Zen 기반 x86 아키텍처에 대해 최상의 코드 생성 및 조정을 사용할 수 있도록 플래그를 지원 합니다.

### <a name="flang"></a>FLANG

FLANG 컴파일러는 AOCC 제품군 (4 월 2018 추가)에 대 한 최근 추가 기능으로, 현재 개발자가 다운로드 하 고 테스트할 수 있도록 시험판에 있습니다. 포트란 2008을 기반으로 하는 AMD는 GitHub 버전의 FLANG ()를 확장 합니다 https://github.com/flang-compiler/flang) . FLANG 컴파일러는 모든 Clang 컴파일러 옵션 및 추가 개수의 FLANG 관련 컴파일러 옵션을 지원 합니다.

### <a name="dragonegg"></a>DragonEgg

DragonEgg은 GCC의 최적화 도구 및 코드 생성기를 LLVM 프로젝트의 코드 생성기로 바꾸는 gcc 플러그 인입니다. AOCC와 함께 제공 되는 DragonEgg은 gcc-4.8와 함께 작동 하며, x86-32/x86-64 대상에 대해 테스트 되었으며 다양 한 Linux 플랫폼에서 성공적으로 사용 되었습니다.

GFortran은 GCC GIMPLE 중간 표현 (IR)을 생성 하는 전처리, 구문 분석 및 의미 체계 분석을 담당 하는 포트란 프로그램의 실제 프런트 엔드입니다. DragonEgg는 GFortran 컴파일 흐름에 연결 하는 GNU 플러그 인입니다. GNU 플러그 인 API를 구현 합니다. 플러그 인 아키텍처를 사용 하는 경우 DragonEgg는 컴파일러 드라이버가 되므로 컴파일의 여러 단계를 구동 합니다.  다운로드 및 설치 지침을 수행한 후 다음을 사용 하 여 드래곤을 호출할 수 있습니다. 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 컴파일러
PGI Community Edition ver. 17은 AMD EPYC를 사용 하도록 확인 되었습니다. PGI 컴파일된 버전의 STREAM은 플랫폼의 전체 메모리 대역폭을 제공 합니다. 최신 Community 버전 18.10 (11 월 2018)도 마찬가지로 작동 합니다. 다음은 Intel 컴파일러를 사용 하 여 최적의 컴파일러에 대 한 샘플 CLI입니다.

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel 컴파일러
Intel 컴파일러 ver. 18은 AMD EPYC를 사용 하기 위해 확인 됩니다. 다음은 Intel 컴파일러로 최적으로 사용 되는 샘플 CLI입니다.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 컴파일러 
HPC의 경우 AMD는 GCC 컴파일러 7.3 이상 버전을 권장 합니다. RHEL/CentOS 7.4에 포함 된 4.8.5와 같은 이전 버전은 사용 하지 않는 것이 좋습니다. GCC 7.3 이상에서는 HPL, HPL 및 DGEMM 테스트에 대해 훨씬 높은 성능을 제공 합니다.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>애플리케이션 크기 조정 

최적의 응용 프로그램 크기 조정 효율성, 성능 및 일관성을 위해 다음 제안이 적용 됩니다.

* 자동 잔액 방식과 달리 순차적 고정 방법을 사용 하 여 코어 0-59에 프로세스를 고정 합니다. 
* Numa/Core/HwThread로의 바인딩은 기본 바인딩 보다 좋습니다.
* 하이브리드 병렬 응용 프로그램 (OpenMP + MPI)의 경우 4 개의 스레드를 사용 하 고, CCX 당 MPI 순위를 1 개 사용 합니다.
* 순수한 MPI 응용 프로그램의 경우 최적의 성능을 위해 1-4 MPI 순위를로 시험해 보세요.
* 메모리 대역폭과 크게 구분 되는 일부 응용 프로그램의 경우 CCX 당 코어 수를 줄이는 것이 유용할 수 있습니다. 이러한 응용 프로그램의 경우 CCX 당 3 개 또는 2 개 코어를 사용 하면 메모리 대역폭 경합이 감소 하 고 실제 성능 또는 확장성이 높은 확장성을 얻을 수 있습니다. 특히 MPI Allreduce은이를 활용 하는 것이 좋습니다.
* 크기를 크게 증가 시키려면 UD 또는 하이브리드 RC + UD 전송을 사용 하는 것이 좋습니다. 대부분의 MPI 라이브러리/런타임 라이브러리는 내부적으로이 작업을 수행 합니다 (예: 작업 x 또는 MVAPICH2). 대량 실행에 대 한 전송 구성을 확인 합니다.

## <a name="next-steps"></a>다음 단계

Azure의 [HPC](/azure/architecture/topics/high-performance-computing/) 에 대해 자세히 알아보세요.
