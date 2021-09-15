<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/covid19-3.PNG" width="90%" align="center">

<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/daily-progress.PNG" width="90%" align="center">

- When the manufacuture is selected, by the progress of timeline, the rate of vaccination of each country that have used the manufacurer are updated.

```python
import dash_core_components as dcc
import dash_html_components as html
import dash_table as dt
import plotly.graph_objects as go
import plotly.figure_factory as ff
import dash_bootstrap_components as dbc
from dash_html_components.Figure import Figure
import pandas as pd
import numpy as np

from dash.dependencies import Input, Output, State
from dash_setup import app
from . import callbacks
from .data import *

# Administering companies
data_matrix = [['Pfizer', 'Administering'],
               ['Moderna', 'Administering'],
               ['Johnson & Johnson', 'Administering'],
               ['Sanofi', 'Approved'],
               ['Novavax', 'Approved'],
               ['McKesson', 'Approved'],
               ['Emergent BioSolutions', 'Approved'],
               ['AstraZeneca', 'Approved']]

administering_df = pd.DataFrame(
    data_matrix, columns=['Company', 'Vaccination Status'])

#company_administering_fig = ff.create_table(data_matrix)

company_administering_fig = dt.DataTable(
    id='company_table',
    columns=[{"name": i, "id": i} for i in administering_df.columns],
    data=administering_df.to_dict('records'),
    style_as_list_view=True,
    style_header={
        'backgroundColor': 'white',
        'fontWeight': 'bold'
    },
    style_cell={
        'padding': '5px',
        'overflow': 'hidden',
        'textOverflow': 'ellipsis',
        'maxWidth': 0,
        'whiteSpace': 'normal',
        'height': 'auto',
        'textAlign': 'left',
        'boxShadow': '0 0'
    },
)

# pie-chart
trace = go.Pie(labels=list(manufactures),
               values=list(total_by_manufactures),
               marker=dict(colors=['rgb(42,60,142)', 'rgb(199,119,68)', 'rgb(91,138,104)',
                           'rgb(67,125,178)', 'rgb(225,184,10)', 'rgb(165,12,12)'])
               )
data = [trace]
layout = go.Layout(title='Client segments')
pie_fig = go.Figure(data=data, layout=layout)

manufacturers_layout = html.Div(style={'marginTop': 100}, children=dbc.Row(
    dbc.Col(width={"size": 10, "offset": 1}, children=[
        dbc.Card(id='manufacturer-card-1',body=True, style={'margin-top': 10}, children=[
            dates.sort(reverse=False),

            html.H2(id='header-d'),
            html.Br(),
            dbc.Row(
                html.H2("Total Number of COVID-19 Vaccines Distributed by a Manufacturer"),
                justify="center",
            ),
            html.Br(),
            dcc.Dropdown(
                id='manufacture-lists',
                options=[
                    {'label': i, 'value': i} for i in manufactures
                ],
                searchable=True,
                value='Moderna',
            ),
            dcc.Graph(id='vaccine-g', figure={}),            

            dbc.Row(style={'margin-top': '4vh'}, children=[
                dbc.Col(width=2, className='center-col', children=[
                    dbc.Button('play', id='timelapse-btn-p', n_clicks=0),
                    html.Img(
                        src=app.get_asset_url('infoicon.png'),
                        height="25px", id="date-tooltip-msg",
                        style={'margin-left': '10px'}
                    ),
                    dbc.Tooltip(
                        "Use the slider and date selector to view previous dates data. Use the play button to start and pause the time-lapse. All data above will reflect the selected date. Charts below and on other pages will not be effected.",
                        target="date-tooltip-msg",
                        placement="auto",
                        style={'width': '100%'}
                    ),
                ]),
                dbc.Col(width=7, children=dcc.Slider(
                    id='date-s',
                    min=0,
                    max=(dates[-1] - dates[0]).days,
                    value=len(dates) - 1,
                    marks={
                        int(i): {'label': dates[i].strftime("%m/%d")} for i 
                        in np.round(np.linspace(0, len(dates) - 1, 4)).astype(int)
                    },
                )),
                dbc.Col(width=3, className='center-col', children=dcc.DatePickerSingle(
                    id='date-p',
                    min_date_allowed=dates[0],
                    max_date_allowed=dates[-1],
                    initial_visible_month=dates[-1],
                    date=dates[-1],
                )),
            ]),
            html.Br(),
            dcc.Interval(
                id='timelapse-i',
                interval=0.5*1000,
                max_intervals=len(dates)-1,
                n_intervals=len(dates)-1,
                disabled=True
            ),

            html.P("Daily Progress By Each Nation"),
            dcc.Graph(id='daily-total'),
            html.Br(),

            html.P("Total Numbers of Vaccines By Manufacturers"),

            dt.DataTable(
                id='total-table',
                columns=[{
                    'name': '{}'.format(manufactures[i]),
                    'id': 'column-{}'.format(i),
                } for i in range(len(manufactures))],
                data=[{'column-{}'.format(i): total_by_manufactures[i]
                        for i in range(len(manufactures))}],
                style_header={
                    'backgroundColor': 'white',
                    'fontWeight': 'bold'
                },
                style_cell={
                    'padding': '5px',
                    'overflow': 'hidden',
                    'textOverflow': 'ellipsis',
                    'maxWidth': 0,
                    'whiteSpace': 'normal',
                    'height': 'auto',
                    'textAlign': 'left',
                    'boxShadow': '0 0'
                },
            ),
            html.Br(),
            html.P('Data last updated: {}'.format(
                dates[-1].strftime("%m/%d/%Y"))),
        ]),

        dbc.Card(id='manufacturer-card-2',body=True, style={'margin-top': '4vh'}, children=[
            dbc.Row(
                html.H2("Manufacturer Country Rollout"),
                justify="center",
            ),

            dcc.Dropdown(
                id='select-manufacture',
                options=[
                    {'label': i, 'value': i} for i in manufacture_list
                ],
                value='Moderna'
            ),
            dcc.Graph(id='world-choropleth-manufacture'),
        ]),

        dbc.Card(id='manufacturer-card-3',body=True, style={'margin-top': '4vh'}, children=[
            dbc.Row(
                html.H2("Manufacturer Information"),
                justify="center",
            ),
           dcc.Tabs(id='manufacturer-tabs', value='tab-1', style={'margin-top': '4vh'}, children=[
                 dcc.Tab(label='Distribution Percentage by Manufacturer', selected_style={
                        'border-top': '2px solid #2A3F5F'}, value='tab-1'),
                dcc.Tab(label='Manufacturers Status', selected_style={
                        'border-top': '2px solid #2A3F5F'}, value='tab-2'),
            ]),
            html.Div(id='manufacturer-tab-div'),
        ])
    ])
))

@app.callback(Output('manufacturer-tab-div', 'children'),
              Input('manufacturer-tabs', 'value'))
def render_content(tab):
    if tab == 'tab-1':
        return html.Div([
            dcc.Graph(figure=pie_fig)
        ])
    elif tab == 'tab-2':
        return html.Div([
            company_administering_fig
        ])
```

