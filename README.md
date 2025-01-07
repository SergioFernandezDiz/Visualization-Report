SUPSI 2024-25  
Data Visualization course, M-D3202E  
Teacher Giovanni Profeta


# Ticino's Traffic
Authors: [Sergio Fernandez Diz](https://github.com/SergioFernandezDiz), [Mirko Keller](https://github.com/giovannipro), [Alessandro Carnio](https://github.com/giovannipro)

[Ticino's Traffic](https://dataviz-supsi.github.io/2024/template/)

## 1. Introduction
Traffic congestion is a global issue affecting major cities and urban areas worldwide. With population growth and urban expansion, the number of vehicles on the roads has increased significantly over the past few decades. This phenomenon has led to a range of negative consequences, including traffic jams, air pollution, longer travel times, and an overall decrease in quality of life. The primary causes of this issue include rising household incomes, greater accessibility to cars, and urban planning that does not always optimally manage high traffic flows.

The consequences of traffic congestion are not limited to economic impacts but also have environmental and social effects. Air pollution caused by vehicle emissions and the noise from traffic negatively affect human health and overall well-being. Furthermore, increased traffic can lead to road accidents, impacting public safety.

### 1.1 Objective
The objective of our project is to analyze traffic in Switzerland, specifically in Ticino. Our goal is to identify patterns and areas with the highest traffic density. Additionally, we will examine traffic trends from 1963 to the present, both at the national level and more specifically for Ticino. This analysis is carried out with the aim of gaining a clear understanding of traffic patterns in Ticino, allowing us to study how traffic is distributed and how roadways can be optimized. The ultimate goal is to reduce traffic-related accidents and minimize pollution caused by vehicles stuck in traffic.

## 2. Dataset 
### 2.1 Dataset Source
For the implementation of the project, we are using a dataset obtained from an [official governmental website](https://www.astra.admin.ch/astra/en/home/documentation/data-and-information-products/traffic-data/data-and-publication/swiss-automatic-road-traffic-counts--sartc-/annual-and-monthly-results.html). The dataset consists of a total of 61 files, each covering a year from 1963 to the present. It provides data on the number of vehicles passing through over 400 traffic counting stations located throughout Switzerland, recorded over specific time periods.

### 2.2 Dataset Structure
As mentioned above, the dataset consists of a total of 61 files, each following different types of templates, which complicates the data reading process. One of the most common templates is as follows:

![image](https://github.com/user-attachments/assets/7d2e35e6-1790-4c8a-88d6-e9f84b5249f2)

As we can observe, for each station, there are a total of 5 different types of data:

- **ADT**: Average daily traffic
- **AWT**: Average weekday traffic
- **ADT Tu-Th**: Average daily traffic from Tuesday to Thursday
- **ADT Sa**: Average traffic on Saturdays
- **ADT Su**: Average traffic on Sundays
  
If needed, here is the [full legend](https://github.com/user-attachments/files/18332543/Bulletin_2023_en.xlsx.-.legend.pdf) provided:

### 2.3 Dataset Problems
As explained earlier, the dataset consists of a total of 61 files, of which 11 were provided in PDF format instead of Excel. This issue has made data processing much more complex, as the files cannot be read directly. Additionally, there is another challenge related to the stations, which are not fixed across all years from installation to the present. For instance, while certain stations may be present in 2020, it is not guaranteed that they will be available in 2021, making data analysis more complicated and limiting the types of analyses that can be performed.

## 3. Data pre-processing

### 3.1 Trasforming PDF Files into Tables
Dopo aver richiesto il dataset completo con tutti gli anni tramite l'indirizzo email presente sul sito, ci sono stati consegnati un totale di 11 su 61 in formato PDF, ecco un esempio:

![image](https://github.com/user-attachments/assets/4d4f25a1-5129-4703-a895-8e0bd8fab0dd)

In modo da poter utilizzando i dati presenti in questi files si sono dovuti realizzare i seguenti passi:
- PDFs were first converted into high-resolution images using [pdf2image](https://pypi.org/project/pdf2image/).
- Text was extracted from these images using OCR software [Tesseract](https://github.com/tesseract-ocr).



```Python
import pandas as pd

df = pd.read_csv('file.tsv', sep='\t')
print(df.columns)
```

## Data visualizations
Sed enim ut sem viverra aliquet eget sit. Iaculis at erat pellentesque adipiscing commodo. Et pharetra pharetra massa massa ultricies mi quis hendrerit dolor. At tempor commodo ullamcorper a lacus vestibulum sed arcu. Ipsum faucibus vitae aliquet nec ullamcorper sit. Tempus quam pellentesque nec nam aliquam sem et tortor. Turpis egestas sed tempus urna et pharetra pharetra massa. Ridiculus mus mauris vitae ultricies leo integer malesuada nunc vel.

### Title of the data visualization n. 1 
Accumsan lacus vel facilisis volutpat est velit egestas dui id. Quisque egestas diam in arcu cursus. Eget nulla facilisi etiam dignissim diam. Aenean sed adipiscing diam donec adipiscing tristique. Porttitor massa id neque aliquam. Sem viverra aliquet eget sit amet tellus cras. Scelerisque eu ultrices vitae auctor eu augue ut lectus.

[<img src="assets/images/03.png" width="800" alt="Placeholder image">]()

### Title of the data visualization n. 2
Accumsan lacus vel facilisis volutpat est velit egestas dui id. Quisque egestas diam in arcu cursus. Eget nulla facilisi etiam dignissim diam. Aenean sed adipiscing diam donec adipiscing tristique. Porttitor massa id neque aliquam. Sem viverra aliquet eget sit amet tellus cras. Scelerisque eu ultrices vitae auctor eu augue ut lectus.

[<img src="assets/images/04.png" width="800" alt="Placeholder image">]()


## Key findings
Accumsan lacus vel facilisis volutpat est velit egestas dui id. Quisque egestas diam in arcu cursus. Eget nulla facilisi etiam dignissim diam. Aenean sed adipiscing diam donec adipiscing tristique. Porttitor massa id neque aliquam. Sem viverra aliquet eget sit amet tellus cras. Scelerisque eu ultrices vitae auctor eu augue ut lectus. Nunc aliquet bibendum enim facilisis gravida neque convallis a. Lacus sed turpis tincidunt id aliquet risus feugiat.

## Next steps
Tellus rutrum tellus pellentesque eu. Dictum sit amet justo donec enim. Aliquam malesuada bibendum arcu vitae elementum curabitur vitae. Sed faucibus turpis in eu mi bibendum neque egestas congue. Tellus in metus vulputate eu scelerisque felis imperdiet proin. Dolor magna eget est lorem ipsum dolor. Sit amet mattis vulputate enim nulla. Elit pellentesque habitant morbi tristique senectus et.
