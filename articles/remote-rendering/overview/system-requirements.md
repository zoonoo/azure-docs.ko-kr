---
title: 시스템 요구 사항
description: Azure 원격 렌더링에 대한 시스템 요구 사항 나열
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 8573a88d5371bbde07a541c789f52e6c44f1e279
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411122"
---
# <a name="system-requirements"></a>시스템 요구 사항

> [!IMPORTANT]
> **Azure Remote Rendering**은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 장에서는 *ARR(Azure 원격 렌더링)에서* 작동하기 위한 최소 시스템 요구 사항을 나열합니다.

## <a name="development-pc"></a>개발 PC

* 윈도우 10 버전 1903 이상.
* 최신 그래픽 드라이버.
* 선택 사항: H265 하드웨어 비디오 디코더(예: Unity)에서 원격으로 렌더링된 콘텐츠의 로컬 미리 보기를 사용하려는 경우.

> [!IMPORTANT]
> Windows 업데이트는 항상 최신 GPU 드라이버를 제공하지 않습니다, 최신 드라이버에 대한 GPU 제조업체의 웹 사이트를 확인 :
>
> * [AMD 드라이버](https://www.amd.com/en/support)
> * [인텔 드라이버](https://www.intel.com/content/www/us/en/support/detect.html)
> * [엔비디아 드라이버](https://www.nvidia.com/Download/index.aspx)

아래 표는 H265 하드웨어 비디오 디코딩을 지원하는 GPU를 나열합니다.

| GPU 제조업체 | 지원되는 모델 |
|-----------|:-----------|
| 엔비디아 | [이 페이지 하단의](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix) **NVDEC 지원 매트릭스를** 확인하십시오. **GPU에는 H.265 4:2:0 8비트** 열에 YES가 필요합니다. |
| Amd | AMD의 [통합 비디오 디코더의](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)적어도 버전 6 GPU . |
| Intel | 스카이레이크 및 최신 CPU |

올바른 H265 코덱이 설치되어 있더라도 코덱 DLL의 보안 속성으로 인해 코덱 초기화 오류가 발생할 수 있습니다. [문제 해결 가이드는](../resources/troubleshoot.md#h265-codec-not-available) 이 문제를 해결하는 방법에 대해 설명합니다. DLL 문제는 Unity와 같은 데스크톱 응용 프로그램에서 서비스를 사용할 때만 발생할 수 있습니다.

## <a name="devices"></a>디바이스

Azure 원격 렌더링은 현재 **HoloLens 2** 및 Windows 데스크톱을 대상 장치로만 지원합니다. 플랫폼 [제한](../reference/limits.md#platform-limitations) 섹션을 참조하십시오.

최신 버전은 대기 시간이 크게 향상되어 최신 HEVC 코덱을 사용하는 것이 중요합니다. 장치에 설치된 버전을 확인하려면 다음 을 수행하십시오.

1. 마이크로 **소프트 스토어를**시작합니다.
1. 오른쪽 상단에 있는 **"..."** 버튼을 클릭합니다.
1. **다운로드 및 업데이트**선택.
1. **장치 제조업체에서 HEVC 비디오 확장 목록을 검색합니다.**
1. 나열된 코덱에 버전 이 **1.0.21821.0**이상 있는지 확인합니다.
1. 업데이트 **받기** 단추를 클릭하고 설치될 때까지 기다립니다.

## <a name="network"></a>네트워크

안정적인 대기 시간이 짧은 네트워크 연결은 좋은 사용자 경험에 매우 중요합니다.

네트워크 요구 [사항에](../reference/network-requirements.md)대한 전용 장을 참조하십시오.

네트워크 문제 해결은 문제 [해결 가이드를](../resources/troubleshoot.md#unstable-holograms)참조하십시오.

## <a name="software"></a>소프트웨어

다음 소프트웨어가 설치되어 있어야 합니다.

* 비주얼 스튜디오 **2019의** 최신 버전 [(다운로드)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **윈도우 SDK 10.0.18362.0** [(다운로드)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(다운로드)](https://git-scm.com/downloads)
* 선택 사항: 데스크톱 PC에서 서버에서 비디오 스트림을 보려면 **HEVC 비디오** [확장(Microsoft 스토어 링크)이](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)필요합니다.

## <a name="unity"></a>Unity

Unity개발을 위해

* Unity 2019.3.1[(다운로드)](https://unity3d.com/get-unity/download)
* 다음 모듈을 Unity에 설치합니다.
  * **UWP** - 유니버설 Windows 플랫폼 빌드 지원
  * **IL2CPP** - Windows 빌드 지원(IL2CPP)

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Unity를 사용하여 모델 렌더링](../quickstarts/render-model.md)
