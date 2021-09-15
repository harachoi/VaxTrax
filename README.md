 
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

<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/Comparison-2.png" width="60%" align="center">

- The user can select one state and another state to compare.

```dotnetcli
import dash_html_components as html
import dash_core_components as dcc
import dash_bootstrap_components as dbc
from . import callbacks
from .data import *
from dash_setup import app

state_comparisons_layout = html.Div(id='comparison-container', children=[
    dbc.Row(
        dbc.Col(
            children=[
                dbc.Card(id='states-card',body=True, style={'margin-top': 10}, children=[

                    dbc.Row(
                        html.H2("State Vaccination Comparisons"),
                        justify="center"
                    ),

                    dbc.Row(children=[
                            dbc.Col(children=[html.Div([

                                dcc.Graph(id="state1-map"),
                                dcc.Dropdown(
                                    id='state1-name',
                                    options=[
                                        {'label': '{}'.format(i), 'value': i} for i in state_codes.values()
                                    ],
                                    value='IL'
                                ),
                                dcc.RadioItems(
                                    id="state1-data-selection",
                                    inputStyle={'margin-left': '6px'},
                                    options=[
                                        {'label' : 'Vaccination Data', 'value':'Vaccine'},
                                        {'label' : 'Election Data', 'value':'Election'}
                                    ],
                                    value='Vaccine'
                                ),
                                html.A(id='state1-covid',
                                       href='',
                                       children=[
                                           html.H2(id="state1-title",
                                                   children=["Please select a state to see state statistics"])
                                       ]),
        
                                html.Label(id="state1-population",children=[""]),
                                html.Br(),
                                html.Label(
                                    id="state1-fully-vaccinated", children=[""]),
                                html.Br(),
                                html.Label(
                                    id="state1-percent-vaccinated", children=[""]),
                                html.Br(),
                                html.Label(
                                    id="state1-daily-vaccinations", children=[""]),
                                html.Br(),
                                html.Label(
                                    id="state1-political-affiliation", children=[""]),
                                html.H2(children=["State Demographics"]),
                              
                                dcc.RadioItems(
                                            id="state1-demographics-selection",
                                            inputStyle={'margin-left': '6px'},
                                            options=[
                                                {'label' : 'Sex', 'value':'sex_translated'},
                                                {'label' : 'Race', 'value':'race_translated'},
                                                {'label' : 'Origin', 'value' :'origin_translated'}
                                            ],
                                            value='sex_translated'
                                        ),
                     
                                dcc.Graph(id='state1-demographics')
                            ], style={'display': 'inline-block', 'text-align': 'center'},), ]),
                            dbc.Col(children=[
                                html.Div([
                                    dcc.Graph(id="state2-map"),
                                    dcc.Dropdown(
                                        id='state2-name',
                                        options=[
                                            {'label': '{}'.format(i), 'value': i} for i in state_codes.values()
                                        ],
                                        value='IN'
                                    ),
                                    dcc.RadioItems(
                                        id="state2-data-selection",
                                        inputStyle={'margin-left': '6px'},
                                        options=[
                                            {'label' : 'Vaccination Data', 'value':'Vaccine'},
                                            {'label' : 'Election Data', 'value':'Election'}
                                        ],
                                        value='Vaccine',
                                    ),
                                    html.A(id='state2-covid',
                                           href='',
                                           children=[
                                               html.H2(id="state2-title",
                                                       children=["Please select a state to see state statistics"]),
                                           ]),
                                    html.Label(
                                        id="state2-population", children=[""]),
                                    html.Br(),
                                    html.Label(
                                        id="state2-fully-vaccinated", children=[""]),
                                    html.Br(),
                                    html.Label(
                                        id="state2-percent-vaccinated", children=[""]),
                                    html.Br(),
                                    html.Label(
                                        id="state2-daily-vaccinations", children=[""]),
                                    html.Br(),
                                    html.Label(
                                        id="state2-political-affiliation", children=[""]),
                                    html.H2(children=["State Demographics"]),   
                                    dcc.RadioItems(
                                                        id="state2-demographics-selection",
                                                        inputStyle={'margin-left': '6px'},
                                                        options=[
                                                            {'label' : 'Sex', 'value':'sex_translated'},
                                                            {'label' : 'Race', 'value':'race_translated'},
                                                            {'label' : 'Origin', 'value' :'origin_translated'}
                                                        ],
                                                        value='sex_translated'
                                                    ),
                                    dcc.Graph(id='state2-demographics')
                                ],
                                    style={'display': 'inline-block',
                                           'text-align': 'center'},
                                ), ]),
                        ],

                        justify="center", 

                    ),
                    


                ]),

                dbc.Card(id='trend-card',body=True, style={'margin-top': '4vh'}, children=[
                    dbc.Row(
                        [html.H2("State Vaccination Comparison Analyses"), ],
                        justify="center"
                    ),
                    html.Div([
                        dcc.Dropdown(
                            id='state-comp-axis',
                            options=[
                                {'label': col.replace("_", " ").title(), 'value': col} for col in usable_vacc_cols
                            ],
                            value='total_vaccinations'
                        ),
                        dcc.RadioItems(
                        id="comparison-trendline",
                        inputStyle={'margin-left': '6px'},
                        options=[
                                    {'label': 'Linear', 'value' : 'ols'},
                                    {'label' : 'Non-linear', 'value' : 'lowess'}
                                ],
                        value='ols'
                    ),
                    html.Img(src=app.get_asset_url('infoicon.png'),
                                        height="25px", id="unique-identifier",
                                        style={'margin-left': '10px'}),
                    dbc.Tooltip(
                        'The linear trend line is calculated via ordinary least square regression while the non-linear \
                        while the non-linear trendline is calculated via local regression.',
                        target="unique-identifier",
                        placement="auto",
                        style={'width': '100%', 'text-align': 'left'}, )
                    ], style={'padding': '12px'}),
                    html.Div([
                        dcc.Graph(id="state-comparison-data")
                    ],
                        style={'padding': '12px'}),
                ]),
            ],
            width={"size": 10, "offset": 1}
        )
    ),
],
    style={'marginTop': 100}
)


```

#### 1) View U.S. COVID-19 Statistics 

[Source Code](https://github.com/harachoi/VaxTrax/blob/main/Portfolio/U.S.%20Statistics.md)

## 화면 구성	

프로젝트 화면 구성 Site 맵 - 이미지를 클릭해주세요!

{% include gallery caption="" %}


<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/US-covid19-statistics.PNG" align="center">

<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/covid19-1(cut).png" width="60%" align="center">

<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/covid19-2(cut).png" width="60%" align="center">

<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/covid19-3(cut).png" width="60%" align="center">

<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/covid19-4(cut).png" width="60%" align="center">


#### 2) 

