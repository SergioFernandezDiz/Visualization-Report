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
After requesting the complete dataset for all years via the email address available on the website, we received a total of 11 out of 61 files in PDF format. Below is an example:

![image](https://github.com/user-attachments/assets/4d4f25a1-5129-4703-a895-8e0bd8fab0dd)

To work with the data contained in these PDF files, the following steps were performed:

- PDFs were first converted into high-resolution images using [pdf2image](https://pypi.org/project/pdf2image/).
- Text was extracted from these images using OCR software [Tesseract](https://github.com/tesseract-ocr).
- From the extracted text, we focused on the MO-SO row, representing total weekly traffic, to maintain consistency across datasets.

Due to the limitations of OCR, many numbers were extracted with errors, such as extra digits, missing digits, or misplaced values in the wrong cells. To address this, the extracted data was manually verified against the original PDFs, and corrections were made to ensure consistency and accuracy.

Below is the function used to extract text from a single image using Tesseract. This function is then used within **process_folder()** to process the entire folder containing all the images for a given year. As you can see, the process is performed using the **extract_table()** function, which returns the table within the image in the **Pandas_df** format.

```Python
def process_image(image_filename, image_folder, output_folder):
    image_path = os.path.join(image_folder, image_filename)
    logger.info(f"Processing image: {image_path}")

    try:
        table_df = extract_table(image_path)
        if not table_df.empty:
            output_csv = os.path.join(output_folder, f"{os.path.splitext(image_filename)[0]}.csv")
            table_df.to_csv(output_csv, index=False)
            logger.info(f"Saved table to {output_csv}")
        else:
            logger.warning(f"No data found in image: {image_filename}")
    except Exception as e:
        logger.error(f"Error processing {image_filename}: {str(e)}")

```
### 3.2 Standardizing Formats
After extracting tables from the various PDF files, several issues needed to be addressed.

One of the primary issues was the incorrect recognition of station names by the OCR used to extract text from the images. For example, some station names were misinterpreted and stored as: [COLOVRE>, GENÃ^VE, HÃ,,GENDO, ZAceRICH]. As evident, these errors stemmed from OCR misreadings of the station names.
To resolve this, given that each of the 11 files contained 400 rows, we utilized **thefuzz**, a Python library designed for fuzzy string matching. This library allows for comparison and matching of strings with minor discrepancies, such as spelling errors or slight variations. By comparing the OCR-extracted station names with the correct reference names, we were able to correct these errors and ensure consistency in the station names across all files.
Here is the code that was used:

```Python
# Iterate through each CSV file in the input folder
for filename in os.listdir(input_folder):
    if filename.endswith('.csv'):
        filepath = os.path.join(input_folder, filename)
        print(f'Processing {filename}...')

        # Load the CSV file
        df = pd.read_csv(filepath)

        if 'Name' not in df.columns:
            print(f"The file {filename} does not contain the 'Name' column. Skipping this file.")
            continue

        # Convert the 'Name' column to string and uppercase to ensure proper processing
        df['Name'] = df['Name'].astype(str).str.upper()

        # Apply name correction
        df['Name'] = df['Name'].apply(lambda x: correct_name(x, official_names, threshold=75))

        # Save the updated CSV file to the output folder
        output_path = os.path.join(output_folder, filename)
        df.to_csv(output_path, index=False,
                  encoding='utf-8-sig')  # Use 'utf-8-sig' to maintain compatibility with Excel
        print(f'{filename} updated and saved to {output_folder}')
```

Using this code, for each name in the file, the `process` and `fuzz` functions from the **thefuzz** library are applied to correct them.

In addition to this, a standard format has been set for the dataset to avoid inconsistencies across the different years.

![image](https://github.com/user-attachments/assets/55f75e34-99d1-4fea-9f6e-ac81e7c9dd86)


### 3.3 Handling Missing and Invalid Data
The handling of missing values was carried out following a structured approach to avoid introducing errors into the dataset:
- If the Annual Mean column was available for a station, missing monthly values were replaced with the annual mean.
- If the Annual Mean was unavailable, the mean of the existing monthly values for that station was calculated and used.

This method ensured that missing values were replaced with accurate approximations based on the available data.

### 3.4 Data Cleaning and Normalization
The OCR process introduced errors in numeric fields, including extra digits, missing digits, or misplaced values. These issues were identified and corrected by manually comparing the extracted data to the original PDFs. Decimal separators were standardized to use a period (.). Additionally, traffic counts from certain years were scaled by multiplying by 1,000 to align with datasets recorded in different units.

### 3.5. Grouping by Canton and Zones
Each station was assigned to a specific canton and zone based on its location and coordinates. Siccome la svizzera ha un totale di 26 Cantoni i quali sono troppi per realizzare l'animazione iniziale, si è deciso di unire alcuni cantoni assieme arrivando ad un totale di 16 zone: Vaud, Ginevra, Neuchatel, Friburgo, Vallese, Bern, Soletta, Basilea, Agrovia, Zurich, Sciaffusa, Central Cantons [Lucerna, Obvaldo, Nidvaldo,Uri, Svitto], Ticino, Grigioni, San Gallo [San Gallo, Appenzello IN,Appenzello Out], Turgovia and Sciaffusa. 

This allowed for regional analysis at both the canton and zone levels. Two additional datasets 
were created:
- A summary of traffic data grouped by canton.
- A dataset grouping stations into predefined zones for more detailed analysis.

### 3.5 Dataset Integration
Once cleaned and standardized, all 60 years of data were merged into a single unified dataset. 
Consistency in column structures and naming conventions was ensured during integration. The 
merged dataset was validated against official reference metadata, such as station lists, to verify 
its accuracy.

### 3.6 Final Dataset Structure
The final dataset contained one row per traffic station with the following columns:
- `Location`: The name of the area where the station is located.
- `Measuring Station` : A unique identifier for each station.
- `Monthly Traffic Counts`: Columns from January to December recording traffic volumes for each month.
- `Annual Mean`: The yearly average traffic volume for each station.

### 3.7 Real-Time Dataset:

In addition to the process of making the main dataset usable, we have also created a real-time dataset that gets updated with new values every 20 minutes. This dataset contains the average speed at specific coordinates along roads and highways, allowing us to track real-time traffic conditions in the Ticino region. The dataset is generated using an API from [TomTom](https://www.tomtom.com/), which, when provided with coordinates, returns the average speed and the maximum speed for that segment of the road. You can also specify the precision radius to reduce the number of requests needed.

This real-time dataset is created through the following process:

1. **Initial Road Data**: An initial file containing road data, including coordinates, is downloaded using OpenStreetMap. This file includes specific road segments selected for traffic monitoring.

2. **Traffic Data Update**: Every 20 minutes, the file is reopened. For each line (road segment) within the file, the API is called to gather traffic information for that particular point. The information collected includes the average speed, maximum speed, and speed limits. The updated traffic data is then saved to a new file, which is continually updated with the latest traffic conditions.

With this dataset, we can visualize traffic information through a dynamic map like the one shown below:

![image](https://github.com/user-attachments/assets/a69ee2b6-0656-4ef5-b2e6-aa4950a1aa31)


### API Functionality:
The TomTom API provides real-time traffic data and allows you to track road conditions based on specific coordinates. The API response includes key traffic details, such as:

- **Average Speed**: The current speed at a specific point on the road.
- **Speed Limits**: The maximum allowed speed for the road segment.
- **Traffic Congestion**: The level of congestion, which is used to determine the current traffic condition.
- **Precision Radius**: This feature allows you to specify a radius around the given coordinates, helping to optimize the number of requests made.

### Dataset Creation Process:
1. **OpenStreetMap**: The initial road data is extracted using OpenStreetMap, which provides detailed map data, including the coordinates of road segments.
2. **API Integration**: After every 30 minutes, the dataset is updated by calling the TomTom API for each road segment, retrieving the current traffic speed, speed limits, and other relevant data.
3. **Saving Data**: The traffic information for each segment is collected and saved into a new file, providing an up-to-date snapshot of the road conditions.

### Code:
Here is the Python code that implements the process for collecting traffic data from the API and saving it to the dataset:

```python
def get_traffic_data(lat, lon):
    # Call the TomTom API to get the speed limits and traffic data
    geojson_data = get_speedbox.get_speed_limits(lat, lon, size)
    
    traffic_data_points = []
    
    # Loop through the returned geojson data
    for feature in geojson_data['features']:
        if feature['geometry']['type'] == 'LineString':
            coordinates = feature['geometry']['coordinates']
            
            # Get the speed limit from the feature properties
            speed_limit = feature['properties'].get('maxspeed', None)
            speed_limit = int(speed_limit) if speed_limit and speed_limit.isdigit() else 50
            
            # Get the center point of the road segment for traffic data
            center_index = len(coordinates) // 2
            center_point = (coordinates[center_index][1], coordinates[center_index][0])
            
            # Call the traffic API to get real-time traffic data at the center point
            traffic_info = traffic.get_data(center_point)
            
            # Get the current speed if available
            current_speed = float(traffic_info['current_speed']) if traffic_info and 'current_speed' in traffic_info else None
            
            # Append the traffic data point to the list
            traffic_data_points.append({
                'coordinates': coordinates,
                'speed_limit': speed_limit,
                'current_speed': current_speed,
                'color': get_color_by_congestion(50, speed_limit)
            })
    
    return traffic_data_points
```

