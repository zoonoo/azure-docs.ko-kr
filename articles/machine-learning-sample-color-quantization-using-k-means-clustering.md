<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="기계 학습 샘플: K-평균 클러스터링을 사용한 색상 양자화 | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure 기계 학습 샘플: K-평균 클러스터링을 사용한 색상 양자화

>[AZURE.NOTE]
>이 모델과 관련된 [샘플 실험] 및 [샘플 데이터 집합]은 기계 학습 스튜디오에서 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.
[샘플 실험]: #sample-experiment
[샘플 데이터 집합]: #sample-dataset


##문제 설명

[색상 양자화](http://en.wikipedia.org/wiki/Color_quantization "Color quantization")는 이미지에서 개별 색상 수를 줄이고 나서 이미지를 압축하는 프로세스입니다. 일반적으로 색상 양자화의 목적은 이미지의 색상 현시를 가능하면 많이 보존하면서 메모리 제한을 위해서든, 압축을 위해서든 색상 수를 줄이는 것입니다. 

##데이터

이 샘플 실험에서는 제공된 24비트 RGB 이미지에 256 x 256 x 256 가능한 색상이 있다고 가정합니다. 또한 이러한 무결성 값에 따라 표준 색상 히스토그램을 빌드할 수 있습니다. 그러나 또 다른 접근법은 이미지를 명시적으로 양자화하고 표현 색상 수를 16 또는 64로 *줄이는* 것입니다. 이를 통해 공간이 많이 감소하고 이상적으로 노이즈와 차이가 감소합니다. 이를 위해 픽셀 데이터(데이터 집합 행으로서 각 픽셀)를 K-평균 클러스터링 모듈에 전달했습니다. 

##모델

모델은 아래 이미지에 표시된 대로 만들어집니다.

![Model][image1]

5개의 다른 분기에서 K가 10-500인 K-평균 클러스터링을 실행했습니다. 먼저 클러스터를 계산하고 클러스터링을 픽셀 색상의 평균으로 집계했습니다(R 스크립트 사용). 
마지막으로 각 픽셀을 집계된 클러스터 색상과 연결하고 새 이미지를 CSV 형식으로 전송했습니다. 한편, 원래 이미지를 사용하여 새 픽셀 색상의 제곱 평균 차이를 계산하고 R 플롯에서 표시했습니다(R 스크립트 실행 사용). 

##결과

아래 실험 모델에 표시된 대로 여러 클러스터(색상)에 대한 결과를 테스트했습니다. 아래에서 알 수 있듯이 더 많이 클러스터링되면 더 적은 압축으로 더 높은 품질의 이미지가 만들어집니다.

||
------------ | ---------
**원본** | ![Original][image2a]
**K=10**     | ![K=10][image2b]
**K=20**     | ![K=20][image2c]
**K=50**     | ![K=50][image2d]
**K=100**    | ![K=100][image2e]
**K=500**    | ![K=500][image2f]


또한 "R 스크립트 실행" 모듈의 두 번째 출력 포트에서 볼 수 있는 원래 이미지 색상에 대한 제곱 평균 차이를 사용하여 정확도를 측정했습니다.

![Output of Execute R Script module][image3]

표시된 대로 색상 클러스터 수가 더 많을수록 더 많은 색상이 원래 이미지와 일치합니다(더 나은 품질). 

##이미지를 CSV로 변환하고 그 반대로 변환하는 코드

이미지를 기계 학습 스튜디오에 공급하기 위해 이미지 파일을 기계 학습 스튜디오에서 사용할 수 있는 csv 형식으로 변환할 수 있는 단순 변환 코드와 이를 다시 이미지로 변환하는 코드를 작성했습니다. 다음 코드를 자유롭게 사용하세요. 나중에 이미지에서 읽기 위한 모듈도 추가할 예정입니다. 

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Drawing;
	using System.Drawing.Imaging;
	using System.IO;
	 
	namespace Text2Image
	{
	    class Program
	    {
	        static void img2csv(string img_path)
	        {
	            FileInfo img_info = new FileInfo(img_path);
	            string destination_file_directory = img_info.DirectoryName + "\\";
	            string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".csv";
	 
	            // Read the image
	            Bitmap img = new Bitmap(img_path);
	 
	            // Create the CSV File and write the header values
	            System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
	            file.WriteLine("X,Y,R,G,B");
	 
	            // Write the Pixel values
	            for (int x = 0; x < img.Width; x++)
	                for (int y = 0; y < img.Height; y++)
	                {
	                    string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
	                    file.WriteLine(line);
	                }
	 
	            file.Close();
	        }
	 
	        static void csv2img(string csv_path)
	        {
	            FileInfo csv_info = new FileInfo(csv_path);
	            string destination_file_directory = csv_info.DirectoryName + "\\";
	            string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".png";
	            
	            // Read all the lines in the CSV file
	            string[] lines = System.IO.File.ReadAllLines(csv_path);
	 
	            // set a new bitmap image with the provided width and height in the header
	            string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	            int img_width = Convert.ToInt32(wh[0])+1;
	            int img_height = Convert.ToInt32(wh[1])+1;
	 
	            Bitmap bmp_img = new Bitmap(img_width, img_height);
	 
	            for (int i = 1; i < lines.Length ;i++ )
	            {
	                string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	                if (values.Length < 3)
	                    continue;
	 
	                int x = Convert.ToInt16(values[0]);
	                int y = Convert.ToInt32(values[1]);
	                int r = Convert.ToInt32(values[2]);
	                int g = Convert.ToInt32(values[3]);
	                int b = Convert.ToInt32(values[4]);
	 
	                bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
	            }
	 
	            bmp_img.Save(destination_file_path);
	        }
	 
	        static void Main(string[] args)
	        {
	            string source_path = args[1];
	            FileInfo source_info = new FileInfo(source_path);
	 
	            if (source_info.Extension == ".csv")
	                csv2img(source_path);
	            else
	                img2csv(source_path);
	        }
	    }
	}


## 샘플 실험

이 모델과 연결된 다음 샘플 실험은 기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에 있습니다.

> **샘플 실험 - K-Means 클러스터링을 사용한 색상 기반 이미지 압축 - 개발**


## 샘플 데이터 집합

이 실험에서 사용되는 다음 샘플 데이터 집합은 기계 학습 스튜디오의 **저장된 데이터 집합** 아래 모듈 팔레트에 있습니다.

###Bil Gates RGB 이미지
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]



[image1]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[image2a]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[image2b]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[image2c]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[image2d]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[image2e]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[image2f]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[image3]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png

