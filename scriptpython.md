```r
# Importer les bibliothèques nécessaires
import dash
from dash import html
from dash import dcc
import pandas as pd
from dash.dependencies import Output, Input
import datetime

# Initialiser l'application
app = dash.Dash(__name__)

# Charger les données à partir d'un fichier CSV et les convertir en type de données flottant
df = pd.read_csv('data.csv',  sep =';')
df.iloc[:,0] = df.iloc[:,0].str.replace(',','.')
df = df.astype('float')

# Créer une liste HTML des valeurs des données pour l'afficher dans la page web
values_list = [html.Li(str(val)) for val in df.values.flatten()]

# Définir la mise en page de l'application en utilisant la classe Div de html et les composants dcc.Graph et html.Ul
app.layout = html.Div(children=[
    # Titre de la page
    html.H1(children='Cours d ENGIE',
            style={'textAlign': 'center'}),
            
    # Graphique de la série chronologique des cours d'ENGIE        
    dcc.Graph(
        id='example-graph',
        figure={
            'data': [
                {'x': df.index, 'y': df.iloc[:, 0], 'type': 'line', 'name': 'Prix en €'},
            ],
            'layout': {
                'title': 'Série chronologique du cours d ENGIE',
                'xaxis': {'title': 'temps'},
                'yaxis': {'title': 'Prix en €'},
                'plot_bgcolor': '#f2f2f2',
                'paper_bgcolor': '#f2f2f2',
                'font': {'color': '#000000'}
            }
        }
    ),
    
  # Liste des valeurs des données
  html.Ul(children=values_list)  ,
  
  # Composant dcc.Interval pour actualiser les valeurs du graphique et de la liste toutes les 5 secondes
  dcc.Interval(
     id='interval-component',
     interval=5000, # actualiser toutes les 5 secondes
     n_intervals=0
          )
], style={'backgroundColor': '#ffffff'})

# Fonction de rappel pour actualiser les valeurs de la liste toutes les 5 secondes
@app.callback(
    Output('example-list', 'children'),
    Input('interval-component', 'n_intervals')
)
def update_list(n):
    # Mettre à jour les valeurs de la liste avec les données nouvellement chargées
    values_list = [html.Li(str(val)) for val in df.values.flatten()]
    return values_list


# Fonction de rappel pour actualiser les valeurs du graphique toutes les 5 secondes
@app.callback(
    Output('example-graph', 'figure'),
    Input('interval-component', 'n_intervals')
)
def update_graph(n):
    # Charger les nouvelles données depuis le fichier CSV et les convertir en type de données flottant
    df_new = pd.read_csv('data.csv', sep=';')
    df_new.iloc[:,0] = df_new.iloc[:,0].str.replace(',','.')
    df_new = df_new.astype('float')

    # Mettre à jour les valeurs du graphique avec les données nouvellement chargées
    return {
        'data': [
            {'x': df_new.index, 'y': df_new.iloc[:, 0], 'type': 'line', 'name': 'Prix en €'},
         ],
         'layout': {
            'title': 'Série chronologique du cours d ENGIE',
            'xaxis': {'title': 'temps'},
            'yaxis': {'title': 'Prix en €'},
            'plot_bgcolor': '#f2f2f2',
            'paper_bgcolor': '#f2f2f2',
            'font': {'color': '#000000'}
         }
    }

if __name__ == '__main__':
    app.run_server(debug=True,host='0.0.0.0',port ='8050', threaded= True)
```
