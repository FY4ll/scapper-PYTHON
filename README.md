Code Python pour extraire des informations sur les apprentissages disponibles en Suisse (Exercice)(NOTE: IL RÉCUPÈRE UNIQUEMENT LES APPRENTIS DEVELLOPEUR)
=======================================================================================

Importation des bibliothèques
-----------------------------

La première étape consiste à importer les bibliothèques nécessaires pour exécuter le code :

- `json` pour formater les données en JSON
- `requests` pour envoyer des requêtes HTTP à l'API
- `etree` pour analyser le contenu HTML de la page
- `datetime` pour manipuler les dates

```
pythonCopy codeimport json
import requests
from lxml import etree
from datetime import datetime

```

Téléchargement de la page Web
-----------------------------

Le code utilise la bibliothèque `requests` pour envoyer une requête GET à l'URL du site web et stocker la réponse dans la variable `response`.

```
pythonCopy codeurl = "https://www.orientation.ch/dyn/show/2930?lang=fr&Idx=0&OrderBy=1&Order=0&PostBackOrder=0&postBack=true&CountResult=0&Total_Idx=0&CounterSearch=2&UrlAjaxWebSearch=%2FLenaWeb%2FAjaxWebSearch&getTotal=False&isBlankState=True&prof_=88613.1&fakelocalityremember=&LocName=&LocId=&Area=10&cty_=VD"
response = requests.get(url)

```

Analyse du contenu de la page
-----------------------------

Le code utilise la bibliothèque `etree` pour analyser le contenu HTML de la page téléchargée et le stocker dans la variable `tree`.

```
pythonCopy codeparser = etree.HTMLParser()
tree = etree.fromstring(response.content, parser)

```

Extraction des éléments div
---------------------------

Le code utilise la méthode `xpath` de `etree` pour extraire les éléments `div` contenant les informations d'apprentissage. Les informations sont stockées dans la variable `resultbox`.

```
pythonCopy coderesultbox = tree.xpath('//div[(@id="result-wrapper")]/div')

```

Traitement des données
----------------------

Le code parcourt la liste `resultbox` et extrait les informations d'apprentissage, les formate en JSON et les envoie à une API.

```
pythonCopy codefor div in resultbox:
    company = div.findall('div')[0].text
    locality = div.findall('div')[2].text
    language = div.findall('div')[3].text
    year = div.findall('div')[4].text
    places = div.findall('div')[5].text.split()[0]
    lastUpdate = div.findall('div')[6].text
    origin = 'orientation.ch'
    originID = div.findall('div')[0].attrib["data-id"]
    date_obj = datetime.strptime(lastUpdate, '%d.%m.%y')
    formatted_date = date_obj.strftime('%Y-%m-%d')
    apprenticeship = {
        'data':{
        'company': company,
        'locality': locality,
        'language': language,
        'year': year,
        'places': places,
        'lastUpdate': formatted_date,
        'origin': origin,
        'originID': int(originID)
        }
    }
    headers = {
        'Content-Type': 'application/json'
    }
    response = requests.post("http://192.168.1.83:1337/api/places", headers=headers, data=json.dumps(apprenticeship))

```

Le code parcourt chaque élément `div` de `resultbox` et extrait les informations pertinentes telles que le nom

