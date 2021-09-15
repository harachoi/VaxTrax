 
# Covid-19 Vaccine Tracker
Based on United States, this program provides the progress of vaccination for each states and for each counties. Also, the users can compare different kinds rates from their state to the other state. Visit us for more information.

## Project Obejctives
  - Develop a web app that graphically displays COVID-19 vaccination data along with other useful COVID-19 related statistics.
  - Display vaccinations administered.
  - Display state vaccination distribution.
  - Predict vaccine doses based on current distribution.
  - Display a herd immunity progress bar, the percent of people vaccinated, for each U.S. state.
  - Identify states that are struggling to administer the vaccine through color coordination.

## 1. Non-Functional Issues

```
Architecture: Client-Server
Language: Python
Frontend Framework: Plotly Dash/Flask
Data visualization/Querying API: Plotly
CSV data loaded from: AWS S3 Urls & Data Migration Script
Service: AWS Elastic Beanstalk
```

## 2. Design Details
<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/image.png" width="60%" align="center">

## 3. Views & Source Codes

#### 1) View U.S. COVID-19 Statistics 
- The user can select one state and another state to compare.

    [Source Code](https://github.com/harachoi/VaxTrax/blob/main/Pages/CompareUS.md)

#### 2) View U.S. COVID-19 Statistics 
- The user can see the detailed progress of vaccination of regions of each state selected.

    [Source Code](https://github.com/harachoi/VaxTrax/blob/main/Pages/US_Province.md)

#### 3) View World Vaccination by Manufacturers
- The user can select one manufacurer and see which countries have used by the timeline.

    [Source Code](https://github.com/harachoi/VaxTrax/blob/main/Pages/US_Province.md)
    
