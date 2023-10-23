# Faulty Water Pump Detection for Enhanced Access to Clean Water in Tanzania
![project banner](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/8e2b82e5-500b-4cd6-94b3-0b49020ece4f)


## Business Problem

Tanzania, one of Africa's most populous nations with over 60 million people in 2021, faces significant challenges in providing access to clean and safe drinking water. In rural areas, water wells, boreholes, and hand-dug wells are the primary sources of drinking water. However, access to clean water varies, with urban areas having better access compared to rural regions. 

Waterborne diseases like cholera and dysentery are prevalent due to poor water quality caused by contamination from pollutants and inadequate sanitation practices. Many Tanzanians, especially in rural areas, rely on potentially unsafe open water sources, making access to clean water a critical public health issue.


## Project Objective

The aim of the project is to create a predictive model to assess the operational status of water pumps in Tanzania. The primary task is to develop a classifier to categorize water wells into different operating conditions, such as "functional," "in need of repair," or "non-functional." 

The target audience is a non-governmental organization (NGO) focused on well maintenance and repair, using the model's predictions to prioritize and address wells in need of attention. This initiative aims to contribute to the broader objective of improving access to clean water in Tanzania, with significant implications for public health and local communities' well-being.


## Data Understanding 

The dataset is sourced from Taarifa and the Tanzanian Ministry of Water, aiming to facilitate the identification of waterpoints functionality statuses. This dataset was originally compiled for the ["Pump it Up: Data Mining the Water Table"](https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/page/23/)  competition on [Driven Data](https://www.drivendata.org/).

The dataset contains information on 59,400 waterpoints characterized by the following features:
* __amount_tsh__ - Total static head (amount water available to waterpoint)
* __date_recorded__ - The date the row was entered
* __funder__ - Who funded the well
* __gps_height__ - Altitude of the well
* __installer__ - Organization that installed the well
* __longitude__ - GPS coordinate
* __latitude__ - GPS coordinate
* __wpt_name__ - Name of the waterpoint if there is one
* __num_private__
* __basin__ - Geographic water basin
* __subvillage__ - Geographic location
* __region__ - Geographic location
* __region_code__ - Geographic location (coded)
* __district_code__ - Geographic location (coded)
* __lga__ - Geographic location
* __ward__ - Geographic location
* __population__ - Population around the well
* __public_meeting__ - True/False
* __recorded_by__ - Group entering this row of data
* __scheme_management__ - Who operates the waterpoint
* __scheme_name__ - Who operates the waterpoint
* __permit__ - If the waterpoint is permitted
* __construction_year__ - Year the waterpoint was constructed
* __extraction_type__ - The kind of extraction the waterpoint uses
* __extraction_type_group__ - The kind of extraction the waterpoint uses
* __extraction_type_class__ - The kind of extraction the waterpoint uses
* __management__ - How the waterpoint is managed
* __management_group__ - How the waterpoint is managed
* __payment__ - What the water costs
* __payment_type__ - What the water costs
* __water_quality__ - The quality of the water
* __quality_group__ - The quality of the water
* __quantity__ - The quantity of water
* __quantity_group__ - The quantity of water
* __source__ - The source of the water
* __source_type__ - The source of the water
* __source_class__ - The source of the water
* __waterpoint_type__ - The kind of waterpoint
* __waterpoint_type_group__ - The kind of waterpoint

The status of the waterpoints are labelled as follows:
* __functional__ - the waterpoint is operational and there are no repairs needed
* __functional needs repair__ - the waterpoint is operational, but needs repairs
* __non functional__ - the waterpoint is not operational

The target variable exhibits a notable class imbalance with 'functional needs repair' representing the minority class. 

![status group distribution](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/473541cd-55ff-45c9-a237-c17aaf01ebeb)

To address the class imbalance issue, there are a few potential strategies:
1. Under-sampling
2. Over-sampling
3. Leaving the class distribution as is and leveraging the libraries' capabilities to construct models.

Below is the distribution of water wells in Tanzania.

![Pump distribution](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/8e37939d-5bdb-48ec-a0ef-d5e9327a6766)


## Data Cleaning

The dataset presented several issues, notably the presence of missing values in various features. with "scheme_name" having the highest count of missing data at 28,166 entries. Other features, such as "funder," "installer," "public_meeting," "scheme_management," and "permit," also had a substantial number of missing values, ranging from 333 to 3,655 entries.

![Missing values](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/ae0258a9-ed82-46ad-b3fa-54c2c7ed42c1)

The "installer" feature contained a large number of unique entries, many of which were due to variations and misspellings related to different installer entities, making data cleaning and standardization essential. Installers outside the top 20 were consolidated into an 'other' feature for ease of predictive model development.

Furthermore, the presence of a significant number of zero values in certain features, including 'population', 'latitude', and 'construction_year', raised questions about data quality and integrity. While the zero values in the population feature were retained, those in 'construction_year' were replaced with the median year 2000,as a reasonable estimate for missing construction years and to mitigate potential outlier effects. 

![construction year dist](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/bec16938-6abb-49b1-aec6-c293364ff3bf)

A noticeable correlation exists between the age of water pumps and their functionality. Older pumps, spanning roughly from 1960 to 1988, tend to be non-functional, while newer pumps constructed after 1988 are more likely to be functional. This pattern remains consistent even after replacing the zero values with the median year of 2000. We note that the replacement of zero values with 2000 as the median construction year leads to a significant increase in the number of water points built in that year, although the functional/non-functional trend remains the same.

![longitude outlier map](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/f1277999-f66e-472b-a2f5-7439d7c5d616)

In the 'longitude' column, we discovered a substantial number of outliers, amounting to 1812 data points. Visualizing these outliers on a map revealed that they were located in the Atlantic Ocean, an implausible location for Tanzanian water pumps. As no reasonable method for imputing these data points was available, the decision was made to remove these rows from the dataset.

After addressing the above challenges with the data, the features were narrowed down from 41 to 18 to build the predictive models. The features are __status_group, amount_tsh, gps_height, installer, longitude, latitude, basin, region, population, construction_year, extraction_type_class, management, payment_type, water_quality, quantity, source_type and waterpoint_type__.


## Exploratory Data Analysis


### Relationship between Water Quantity and Water Well Functionality
![Water Quantity](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/2299db24-7d3d-42a1-ab1b-6f06ff6ff971)

There is a clear positive correlation between water availability and water pump functionality. Dry water points, totaling around 6,000, are predominantly non-functional, with fewer than 1,000 in working condition. In regions where water is scarce, there are approximately 8,000 functional pumps. Conversely, regions with ample water resources exhibit a high count of functional pumps, exceeding 20,000. It's noteworthy that "quantity_dry" is consistently recognized as a critical feature by various models when predicting water well functionality.


### Relationship between Regions and Water Well Functionality
![Region status](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/f2c4b1fe-d7ea-4e55-b8ff-d842b2270f91)

Iringa leads with a notable 4,138 functional pumps, followed by Kilimanjaro, Morogoro, and Arusha, which each have over 2,000 functional pumps. On the other hand, some regions face significant challenges with non-functional water pumps. Morogoro, reporting 1,587 non-functional pumps, leads this category, followed by Mbeya and Shinyanga. 

The regions of Morogoro and Mbeya, with substantial populations, require immediate attention and maintenance to restore clean and reliable water access. 


### Relationship between Payment Status and Water Well Functionality
![Payment type status](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/d2d9d7a4-0a36-41bf-9312-2c4694764de7)

Water pumps marked as "Never pay" are widespread in Tanzania, but they exhibit a higher count of non-functional water points compared to the functional ones. In contrast, categories associated with payments demonstrate a positive correlation between the presence of payments and water point functionality, underscoring the significance of payment systems as a determining factor.


### Relationship between Water Basin and Water Well Functionality
![Water basin status](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/c4e6f0d6-c143-4d0e-99d7-523421334e9d)

Some water basins in Tanzania emerge with a substantial number of functional water points, notably Pangani, Lake Victoria, and Rufiji. Lake Victoria stands out with a significant count of functional water points, suggesting a relatively robust water infrastructure, albeit with a notable need for repairs. On the contrary, certain basins, such as Lake Rukwa, grapple with a considerable number of non-functional water points, constituting almost 50% of the total, emphasizing the urgency of maintenance and attention to ensure access to clean and reliable water sources in these regions. 


### Relationship between Total Static Head and Water Well Functionality
![Amount TSH at zero](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/9f02e9e7-614e-4952-9adf-b146fadd44b4)
![Amount TSH above zero](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/fb1acdad-d474-4998-aecf-281d07df0326)

The "amount_tsh" variable, representing the Total Static Head, quantifies the vertical measurement in feet, indicating the distance between a water source and the point where liquid is discharged or reaches its surface. The analysis reveals that when "amount_tsh" registers as zero, there is an almost equal likelihood of the pump being functional or non-functional. However, as the "amount_tsh" value increases, there is a corresponding rise in the likelihood of pump functionality.


### Relationship between Management or Installer and Water Well Functionality
![Management Companies](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/03791048-a0e1-489c-9352-ff82ccf5d5d5)

Water well management is primarily conducted by VWC, they oversee approximately 19,000 functional wells, around 16,000 in need of repair, and fewer than 2,500 that are non-functional. Following VWC, WUG manages significantly fewer water wells, with around 4,000 functional, approximately 2,000 requiring repair, and just under 1,000 being non-functional. This suggests that there may be challenges in both companies' management practices or a need for increased maintenance efforts to improve the functionality of these wells.

![Installer status](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/44b540c0-2526-4114-ad3e-4820098ea517)

The Department of Water and Sanitation (DWE) emerges as a prominent installer, responsible for a substantial number of water pumps in Tanzania. While they have successfully installed a considerable 8,911 functional pumps, it is equally concerning that 6,016 of their installations currently remain non-functional. On the other hand, the Central Government exhibits a contrasting trend, as it has more non-functional pumps (2,130) than functional ones (1,182). This may indicate a lapse in terms of maintenance strategies by the organizations involved.


## Modeling
F1 score is used as the performance metric, benefiting the project by considering both precision and recall, thus balancing the model's ability to correctly classify pump functionality. 

The following models are tested to identify the best classifier:
1. __Logistic Regression__
2. __Decision Tree Classifier__
3. __K Nearest Neighbor__
4. __Random Forest Classifier__
5. __eXtreme Gradient Boosting (XGBoost)__
6. __eXtreme Gradient Boosting (XGBoost) with SMOTE__

__GridSearchCV__ is used for hyperparameter tuning. The technique efficiently identifies the best hyperparameter combination for a model while performing cross-validation to ensure optimal model performance.

We shall look at the two best performing models.

![Model Performance Scores](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/19cf7f3b-1c90-4d87-acf0-b99f9dc69969)

### Random Forest Classifier 
![Random Forest Classification Report](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/f0265078-6614-48bb-8e42-5737195c476d)

![Random Forest Best Performing](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/20bac4ff-1d56-464f-a4bc-08e001d0d9f8)

The random forest model demonstrates good overall performance with an __accuracy score of 81%__. It excels in __correctly identifying non-functional water pumps, achieving an impressive F1-score of 0.80 for this class__. However, __it struggles with the "functional needs repair" category, as indicated by the lower precision and recall scores__, suggesting that the model faces challenges in accurately predicting this class. Despite this, the model provides a solid foundation for water pump functionality prediction in Tanzania with a __weighted F1-score of 0.798__.

![Random Forest Feature Importances](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/d898c985-c8e7-4314-b46f-2948af66253f)

Looking at the model's feature importances, __"quantity_dry" emerges as the most critical factor, contributing approximately 10.57% to the model's predictive power__, highlighting the impact of water scarcity. __"longitude" and "latitude" closely follow, each contributing around 10.12% and 9.96%__, underlining the significance of geographical location. __"construction_year" plays a vital role at 6.07%, while "gps height" contributes approximately 5.43%__.

### eXtreme Gradient Boosting (XGBoost)
![XG Boost Classification Report](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/3a4f05a5-9224-4266-8f20-37c2a808572b)

![XG Boost Best Performing](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/82bbbaee-2d78-4142-94ce-173770872347)

The XG Boost model stands out as the top-performing model in various aspects. It achieves the __highest accuracy, reaching 81%, and has the best F1-Score of 80.21%__, showcasing its overall effectiveness in classifying water pump conditions. While the F1 scores for the "functional" and "non-functional" classes remain consistent with those of the Random Forest model, there is a notable improvement in the F1 scores for the "functional needs repair" class, indicating its enhanced capability to identify water pumps in need of repair.

The __training accuracy for the XG Boost model is notably high at 91.09%__, suggesting that the model successfully learned from the training data and made accurate predictions. Although there is a gap between the training and validation accuracy, it doesn't signal severe overfitting, additional measures like regularization and cross-validation could further mitigate overfitting risks.

![Feature Importances XG Boost](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/assets/133201112/b666d818-eb92-4ea5-89df-61c405b1c933)

Looking at the feature importances of the model, __"quantity_dry" notably emerges as the most influential factor, accounting for approximately 22.58% of the model's predictive power__, highlighting the substantial impact of water scarcity on functionality. Additionally, __"waterpoint_type_other" and "region_iringa" play a key role contributing 6.50% and 3.12% importance__ respectively underscoring the significance of water point types and region in water well functionality. These insights provide valuable information for policymaking and interventions aimed at enhancing access to clean water.

## Summary 

I recommend that the NGO utilizes the XG Boost model as the primary tool for predicting water point functionality in Tanzania. This model has demonstrated its efficiency in handling complex data and achieving a remarkable accuracy rate. The model achieved the __highest accuracy of 81% and the highest F1-Score of 80.21%__ although the Random Forest Classifier was not far behind as it achieves an accuracy of 80.82% and F1-Score of 79.84%.

The model excelled in predicting "functional" and "non-functional" water pump conditions but similarly to the other models, it struggled with the "functional needs repair" class. This should be taken into consideration by the NGO when applying the model to real world scenarios.

The NGO should also take into considertion the following findings identified through exploration of the data: 
1. Positive correlation between water availability and functionality is evident, with dry water points primarily non-functional, while regions with sufficient water exhibit a high count of functional pumps. "quantity_dry" is a crucial feature for predicting well functionality, emphasizing the importance of addressing water scarcity.
2. Regions like Iringa stands out with the highest number of functional water pumps, but Morogoro, Mbeya, and Shinyanga face substantial challenges with non-functional pumps. Regions like Morogoro and Mbeya, with significant populations relying on these water points, require immediate attention for maintenance and to ensure clean water access.
3. "Never pay" water pumps are prevalent in Tanzania however they have a higher number of non-functioning water points compared to functioning ones. Conversely, categories involving payments show a positive correlation between payment presence and water point functionality, making payment systems a factor to consider.
4. The status of water points varies across different water basins, with some basins, like Lake Rukwa, experiencing a high number of non-functional points. Urgent maintenance and attention are necessary in these areas to secure clean and reliable water sources for the communities. These disparities underscore the need for targeted interventions and improvements.
5. Total Static Head measurement, may serve as a valuable predictor for pump functionality. When "amount_tsh" is zero, there is an equal likelihood of functional and non-functional pumps, but an increase in "amount_tsh" corresponds to a higher likelihood of functionality. These insights can inform the NGO's strategies for improving clean water access in Tanzania
6. Water points with zero recorded populations tend to have a higher proportion of non-functional pumps. However, there is a clear correlation between population and water point functionality. As population increases, the number of both functional and non-functional pumps rises, but functional pumps remain more prevalent. The highest concentration of water points occurs in areas with around 7,000 people.
7. There is a distinct correlation between the age of water pumps and their functionality. Older pumps, built between approximately 1960 and 1988, are often non-functional, while newer pumps constructed after 1988 are more likely to be in working condition.


## Study Limitations

Data-related challenges included missing values in various features, with "scheme_name" having the highest count of missing data, and other features like "funder," "installer," "public_meeting," "scheme_management," and "permit" also containing substantial gaps. Thorough data cleaning and standardization were necessary due to the multitude of unique entries in the "installer" feature.

Additionally, certain features had a high prevalence of zero values, raising concerns about data quality and making it challenging to handle these during modeling. Model tuning was resource-intensive, with limited exploration of hyperparameter combinations due to time constraints.

Class imbalance in the target variable was addressed using SMOTE, but it had limited impact on model performance. Suggested strategies included regularization, cross-validation, or expanding the dataset with more diverse data to mitigate overfitting and improve the model's performance.

## Recommendations for Future Study
1. Implement rigorous data quality assurance measures to reduce the presence of missing values, ensure consistent data entry practices, and address zero values accurately. This will enhance the reliability of the dataset and improve modeling outcomes. Collaboration with local authorities, water management organizations, and NGOs can ensure data is frequently updated.
2. Evaluate a broader range of machine learning algorithms, including ensemble methods, and  gradient boosting, to identify the most suitable algorithms for this classification task. A more comprehensive model selection process may reveal models that perform better than the ones tested.
3. Collect more diverse and comprehensive data, especially concerning well characteristics, water quality, geological factors, and socioeconomic information of the regions. This additional data can contribute to a better understanding of water pump functionality and further improve predictive models particularly when it comes to issues of class imbalance.

## Further Information
- The project presentation can be viewed through this [link](https://storymaps.arcgis.com/stories/bb62dd48334f423d8b73168b5d575e61)
- The code for all models tested can be viewed on my [Jupyter Notebook](https://github.com/YKamari/Faulty-Water-Pump-Detection-in-Tanzania/blob/main/water_wells_notebook.ipynb)
