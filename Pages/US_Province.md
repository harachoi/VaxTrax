<img src="https://github.com/harachoi/VaxTrax/blob/main/Images/covid19-2.png" width="60%" align="center">

```Python
covidStats_layout = html.Div([
    dbc.Row(
        dbc.Col(
            children=[

                dbc.Card(id='stat-card',body=True, style={'margin-top': 10}, children=[
                    dbc.Row(
                        [html.H2("US COVID-19 Cases Statistics"), ],
                        justify="center"
                    ),
                    dcc.Dropdown(
                        id='y-axis',
                        options=[
                            {'label': stats_columns[col], 'value': col} for col in stats_columns.keys()
                        ],
                        value='positive_rate'
                    ),
                    dcc.Graph(id="US graph"),
                ]),

                dbc.Card(id='state-covid-card',body=True, style={'margin-top': '4vh'}, children=[
                    dbc.Row(
                        [html.H2("State COVID-19 Cases"), ],
                        justify="center"
                    ),
                    dcc.Dropdown(
                        id='stateSelection',
                        options=[
                            {'label': '{}'.format(i), 'value': i} for i in county_covid_data['Province_State'].unique()
                        ],
                        value='IL'
                    ),

                    dcc.Graph(id='choropleth'),
                    html.P("Confirmed Cases over the last 7 days by State"),
                    dt.DataTable(
                        id='stateTable',
                        columns=[{"name": i, "id": i} for i in table_columns],
                        page_size=15,
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
                    ),

                ]

                )

            ],
            width={"size": 10, "offset": 1}
        )
    ),

],
    style={'marginTop': 100}
)


@app.callback(
    Output('choropleth', 'figure'),
    Input('stateSelection', 'value'),
)
def display_choropleth(state):
    if not state:
        raise PreventUpdate

    scope = [state]
    df_State = county_covid_data[county_covid_data['Province_State'].isin(
        scope)]
    values = df_State['3/1/21'].tolist()
    fips = df_State['FIPS'].tolist()

    fig = ff.create_choropleth(
        fips=fips,
        values=values,
        scope=scope,
        round_legend_values=True,
        simplify_county=0,
        simplify_state=0,
        legend_title='Confirmed cases per county as of 3/1/21',
        title=list(us_state_abbrev.keys())[
            list(us_state_abbrev.values()).index(state)]
    )
    fig.update_layout(
        plot_bgcolor='white',
        paper_bgcolor='white',
        font_color='#2a3f5f'
    )
    return fig
```