---
title: HPC 응용 프로그램 크기 조정 - Azure 가상 시스템 | 마이크로 소프트 문서
description: Azure VM에서 HPC 응용 프로그램을 확장하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707832"
---
# <a name="scaling-hpc-applications"></a>HPC 애플리케이션 확장

Azure에서 HPC 응용 프로그램의 최적의 확장 및 확장 성능에는 특정 워크로드에 대한 성능 조정 및 최적화 실험이 필요합니다. 이 섹션과 VM 시리즈별 페이지는 응용 프로그램 크기를 조정하기 위한 일반적인 지침을 제공합니다.

## <a name="compiling-applications"></a>응용 프로그램 컴파일

필요하지는 않지만 적절한 최적화 플래그로 응용 프로그램을 컴파일하면 HB 및 HC 시리즈 VM에서 최상의 확장 성능을 제공합니다.

### <a name="amd-optimizing-cc-compiler"></a>AMD 최적화 C/C++ 컴파일러

AMD 최적화 C/C++ 컴파일러(AOCC) 컴파일러 시스템은 글로벌 최적화, 벡터화, 절차 간 분석, 루프 변환 및 코드 생성을 포함하는 높은 수준의 고급 최적화, 멀티 스레딩 및 프로세서 지원을 제공합니다. AOCC 컴파일러 바이너리는 GNU C 라이브러리(glibc) 버전 2.17 이상을 갖는 리눅스 시스템에 적합합니다. 컴파일러 제품군은 C/C++ 컴파일러(clang), 포트란 컴파일러(FLANG) 및 포트란 프론트 엔드에서 Clang(드래곤 에그)으로 구성됩니다.

### <a name="clang"></a>쿨랑 (주)

Clang은 C, C++및 Objective-C 컴파일러로 전처리, 구문 분석, 최적화, 코드 생성, 어셈블리 및 연결을 처리합니다. Clang은 `-march=znver1` AMD의 Zen 기반 x86 아키텍처에 대해 최상의 코드 생성 및 튜닝을 가능하게 하는 플래그를 지원합니다.

### <a name="flang"></a>플랜 (것)이 많은 것

FLANG 컴파일러는 AOCC 제품군(2018년 4월 추가)에 최근에 추가되었으며 현재 개발자가 다운로드하여 테스트할 수 있도록 시험판에 있습니다. 포트란 2008을 기반으로, AMD는 FLANG의 GitHubhttps://github.com/flangcompiler/flang)버전을 확장 (. FLANG 컴파일러는 모든 Clang 컴파일러 옵션과 추가 수의 FLANG 관련 컴파일러 옵션을 지원합니다.

### <a name="dragonegg"></a>드래곤에그

DragonEgg는 GCC의 최적화 프로그램 및 코드 생성기를 LLVM 프로젝트의 플러그인으로 대체하는 gcc 플러그인입니다. AOCC와 함께 제공되는 DragonEgg는 gcc-4.8.x와 함께 작동하며 x86-32/x86-64 대상에 대해 테스트되었으며 다양한 Linux 플랫폼에서 성공적으로 사용되었습니다.

GFortran은 GCC GIMPLE 중간 표현(IR)을 생성하는 전처리, 구문 분석 및 의미 분석을 담당하는 Fortran 프로그램의 실제 프런트 엔드입니다. 드래곤에그는 GNU 플러그인으로, GFortran 컴파일 흐름에 연결됩니다. GNU 플러그인 API를 구현합니다. 플러그인 아키텍처를 통해 DragonEgg는 컴파일러 드라이버가 되어 컴파일의 여러 단계를 구동합니다.  다운로드 및 설치 지침을 따라 후, 드래곤 에그를 사용하여 호출 할 수 있습니다 : 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 컴파일러
PGI 커뮤니티 에디션 ver. 17은 AMD EPYC와 함께 작동하는 것으로 확인되었습니다. PGI 컴파일된 STREAM 버전은 플랫폼의 전체 메모리 대역폭을 제공합니다. 새로운 커뮤니티 에디션 18.10 (2018년 11월)도 마찬가지로 잘 작동해야 합니다. 다음은 인텔 컴파일러를 사용하여 최적으로 컴파일러하는 샘플 CLI입니다.

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>인텔 컴파일러
인텔 컴파일러 ver. 18은 AMD EPYC와 함께 작동하는 것으로 확인되었습니다. 다음은 인텔 컴파일러를 사용하여 최적으로 컴파일러하는 샘플 CLI입니다.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 컴파일러 
HPC의 경우 AMD는 GCC 컴파일러 7.3 이상을 권장합니다. RHEL/CentOS 7.4에 포함된 4.8.5와 같은 이전 버전은 권장되지 않습니다. GCC 7.3 및 최신 은 HPL, HPCG 및 DGEMM 테스트에서 훨씬 더 높은 성능을 제공합니다.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>애플리케이션 크기 조정 

다음 제안 사항은 최적의 응용 프로그램 확장 효율성, 성능 및 일관성에 적용됩니다.

* 자동 균형 접근 방식이 아닌 순차적 고정 방식을 사용하여 0-59 코어에 고정 처리합니다. 
* Numa/Core/HwThread에 의한 바인딩은 기본 바인딩보다 낫습니다.
* 하이브리드 병렬 응용 프로그램(OpenMP+MPI)의 경우 CCX당 4개의 스레드와 1개의 MPI 순위를 사용합니다.
* 순수 MPI 애플리케이션의 경우 CCX당 1-4MPI 순위를 실험하여 최적의 성능을 발휘합니다.
* 메모리 대역폭에 대한 민감도가 매우 큰 일부 응용 프로그램은 CCX당 감소된 코어 수를 사용하는 것이 도움이 될 수 있습니다. 이러한 애플리케이션의 경우 CCX당 3개 또는 2개의 코어를 사용하면 메모리 대역폭 경합이 줄어들고 실제 성능이 향상되거나 확장성이 더 일관되게 발생할 수 있습니다. 특히, MPI Allreduce이 혜택을 누릴 수 있습니다.
* 훨씬 더 큰 스케일 실행의 경우 UD 또는 하이브리드 RC+UD 전송을 사용하는 것이 좋습니다. 많은 MPI 라이브러리/런타임 라이브러리는 내부적으로 이 작업을 수행합니다(예: UCX 또는 MVAPICH2). 대규모 실행에 대 한 전송 구성을 확인 합니다.

## <a name="next-steps"></a>다음 단계

Azure에서 [HPC에](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) 대해 자세히 알아보세요.
