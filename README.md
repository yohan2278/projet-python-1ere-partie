# Importation des modules nécessaires
from collections import Counter
import re
import csv
from bs4 import BeautifulSoup
from urllib.parse import urlparse
import requests



# Étape 1 : Compter les occurrences des mots dans le texte

def compter_occurrences_mots(texte):
    # Convertir le texte en minuscules pour éviter la distinction entre majuscules et minuscules
    texte = texte.lower()
    # Utiliser une expression régulière pour extraire les mots du texte
    mots = re.findall(r'\w+', texte)
    # Compter le nombre d'occurrences de chaque mot à l'aide de Counter
    compteur_mots = Counter(mots)
    return compteur_mots



# Étape 2 : Filtrer les mots parasites de la structure de données

def filtrer_mots_parasites(structure_donnees, mots_parasites):
    # Convertir la liste des mots parasites en un ensemble pour une recherche plus efficace
    mots_parasites = set(mots_parasites)
    # Filtrer les mots parasites de la structure de données
    structure_filtree = [(mot, occ) for mot, occ in structure_donnees.items() if mot not in mots_parasites]
    return structure_filtree



# Étape 3 : Récupérer les mots parasites depuis un fichier CSV

def recuperer_mots_parasites_avec_fichier_csv(nom_fichier):
    # Initialiser une liste pour stocker les mots parasites
    mots_parasites = []
    # Lire le fichier CSV et ajouter les mots de chaque ligne à la liste
    with open(nom_fichier, newline='') as fichier_csv:
        lecteur = csv.reader(fichier_csv)
        for ligne in lecteur:
            mots_parasites.extend(ligne)
    return mots_parasites


# Étape 5 : Suppression des balises HTML

def supprimer_balises_html(texte_html):
    # Utiliser BeautifulSoup pour analyser le texte HTML
    soup = BeautifulSoup(texte_html, 'html.parser')
    # Extraire le texte sans les balises
    texte_sans_html = soup.get_text(separator=' ')
    return texte_sans_html


# Étape 6 : Récupérer les valeurs associées à une balise et un attribut

def valeurs_attribut_dans_balises(texte_html, nom_balise, nom_attribut):
    # Utiliser BeautifulSoup pour analyser le texte HTML
    soup = BeautifulSoup(texte_html, 'html.parser')
    # Trouver toutes les balises du type spécifié
    balises = soup.find_all(nom_balise)
    # Extraire les valeurs de l'attribut spécifié pour chaque balise
    valeurs = [balise.get(nom_attribut) for balise in balises if balise.get(nom_attribut)]
    return valeurs


# Étape 8 : Extraction du nom de domaine à partir d'une URL

def nom_de_domaine_de_url(url):
    # Utiliser urlparse pour extraire le nom de domaine de l'URL
    parsed_url = urlparse(url)
    return parsed_url.netloc


# Étape 9 : Filtration des URL par nom de domaine

def filtrer_urls_par_domaine(nom_domaine, liste_urls):
    # Filtrer les URL en fonction du nom de domaine
    urls_du_domaine = [url for url in liste_urls if nom_de_domaine_de_url(url) == nom_domaine]
    urls_pas_du_domaine = [url for url in liste_urls if nom_de_domaine_de_url(url) != nom_domaine]
    return urls_du_domaine, urls_pas_du_domaine



# Étape 10 : Récupération du contenu HTML à partir d'une URL

def recuperer_contenu_html(url):
    # Envoyer une requête GET pour récupérer le contenu HTML de l'URL
    reponse = requests.get(url)
    # Vérifier si la requête a réussi (code de statut 200)
    if reponse.status_code == 200:
        # Renvoyer le contenu HTML
        contenu_html = reponse.text
        return contenu_html
    else:
        # Renvoyer None si la requête a échoué
        return None



# Étape 4 : Création et écriture du fichier CSV (pas besoin de modification)

# Étape 7 : Utilisation des étapes précédentes pour obtenir des mots-clés


def obtenir_mots_clefs(texte):
    # Compter les occurrences des mots dans le texte
    occurrences_mots = compter_occurrences_mots(texte)
    # Récupérer les mots parasites depuis un fichier CSV
    fichier_mots_parasites = "parasite.csv"
    mots_parasites = recuperer_mots_parasites_avec_fichier_csv(fichier_mots_parasites)
    # Filtrer les mots parasites de la structure de données
    mots_clefs = filtrer_mots_parasites(occurrences_mots, mots_parasites)
    return mots_clefs

# Tester la fonction avec un texte assez long
texte_long = "le jeux vidéo est le meilleur univers du monde du monde"
resultat_mots_clefs = obtenir_mots_clefs(texte_long)
print(resultat_mots_clefs)



# Étape 10 (suite) : Utilisation de la fonction pour récupérer du contenu HTML

url_test = "https://www.example.com"
contenu_html = recuperer_contenu_html(url_test)
print("Contenu HTML de", url_test, ":", contenu_html)
