NC 및 NV 크기를 GPU 사용 인스턴스라고도 합니다. 다양한 시나리오 및 사용 사례에 대해 최적화된 NVIDIA GPU 카드를 포함하는 특수한 가상 컴퓨터입니다. NV 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 활용하는 원격 시각화, 스트리밍, 게임, 인코딩 및 VDI 시나리오에 맞게 최적화되고 설계되었습니다. NC 크기는 계산 집약적 및 네트워크 집약적 응용 프로그램, CUDA를 비롯한 알고리즘, OpenCL 기반 응용 프로그램 및 시뮬레이션에 좀 더 최적화되어 있습니다. 


NV 인스턴스는 고객이 자체 데이터 또는 시뮬레이션을 시각화할 수 있는 데스크톱 가속화 응용 프로그램 및 가상 데스크톱용 NVIDIA Tesla M60 GPU 카드 및 NVIDIA GRID를 통해 지원됩니다. 사용자는 NV 인스턴스에서 그래픽 집약적인 워크플로를 시각화하여 뛰어난 그래픽 기능을 가져오고 인코딩 및 렌더링 등의 단정밀도 작업을 추가적으로 실행할 수 있습니다. Tesla M60는 최대 36 스트림의 1080p H.264를 갖는 이중 GPU 디자인의 4096개 CUDA 코어를 제공합니다. 

NC 인스턴스는 NVIDIA Tesla K80 카드를 통해 구동됩니다. 이제 에너지 탐색 응용 프로그램, 충돌 시뮬레이션, 광선 추적 렌더링, 심층 학습 등에 CUDA를 활용하여 데이터를 훨씬 더 빠르게 처리할 수 있습니다. Tesla K80는 최대 2.91테라플롭 배정밀도 및 8.93테라플롭의 단정밀도 성능을 갖춘 이중 GPU 디자인의 4992 CUDA 코어를 제공합니다.

## <a name="nv-instances"></a>NV 인스턴스

| 크기 | CPU 코어 | 메모리: GiB | 로컬 SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1,440 | 4 |

1 GPU = M60 카드 절반.

**지원되는 운영 체제**

* Windows Server 2016, Windows Server 2012 R2 - [Windows용 N-시리즈 드라이버 설치](../articles/virtual-machines/windows/n-series-driver-setup.md) 참조

## <a name="nc-instances"></a>NC 인스턴스

| 크기 | CPU 코어 | 메모리: GiB | 로컬 SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1,440 | 4 |
| Standard_NC24r* |24 |224 | 1,440 | 4 |

1 GPU = K80 카드의 절반.

*RDMA 지원

**지원되는 운영 체제**

* Windows Server 2016, Windows Server 2012 R2 - [Windows용 N-시리즈 드라이버 설치](../articles/virtual-machines/windows/n-series-driver-setup.md) 참조
* Ubuntu 16.04 LTS - [Linux용 N-시리즈 드라이버 설치](../articles/virtual-machines/linux/n-series-driver-setup.md) 참조

<br>

