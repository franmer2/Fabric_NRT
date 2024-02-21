Microsoft Fabric propose une suite d'outils très pratiques pour accélérer la mise en place de project "presque temps réel"

Dans cet article, nous allons voir un exemple d'implémentation de ces outils pour mettre en place un rapport Power BI "presque temps réel" à partir des données de ce [site web](https://bctransit.com/open-data). Plus précismenent nous allons utiliser les données se trouvant [ici](https://bct.tmix.se/gtfs-realtime/vehicleupdates.js?operatorIds=12).

Ci-dessous une vue globale la solution

![Overview](/Pictures/001.jpg)



# Prérequis

- Une [capacité Fabric](https://learn.microsoft.com/fr-fr/fabric/enterprise/buy-subscription#buy-an-azure-sku)
- Une [souscription Azure](https://azure.microsoft.com/en-ca/free/)
- Un service [Azure Logic Apps déployé](https://learn.microsoft.com/fr-fr/azure/logic-apps/logic-apps-overview#get-started)



# Microsoft Fabric
## Base de données KQL


Afin de stocker les données que nous allons récupérer et créer des rapports "presque temps réel", la base de données KQL est le candidat idéal ici.

Pour commencer, connectez-vous sur le portail [Microsoft Fabric](https://fabric.microsoft.com).

[Créez un nouvel espace de travail](https://learn.microsoft.com/fr-fr/fabric/get-started/create-workspaces) afin d'y déployer les différents services.

Une fois dans votre espace de travail nouvellement créer, choisissez le persona "Real-Time Analytics"

![Persona](/Pictures/002.jpg)

Cliquez sur le bouton "+ New" puis sur "KQL Database"

![KQL](/Pictures/003.png)

Donnez un nom à votre base KQL et cliquez sur le bouton "Create"

![Create](/Pictures/004.png)

Une fois votre base KQL créée, vous devriez obtenir une fenêtre similaire à celle-ci dessous :

![Create](/Pictures/005.png)

## Eventstream

Depuis votre espace de travail, cliquez sur le bouton "+ New" puis sur "Eventstream".

![Eventstream](/Pictures/006.png)

Donnez un nom à votre "Eventstream" puis cliquez sur "Create".

![Eventstream](/Pictures/007.png)

### Creation de l'entrée des évènements

Une fenêtre similaire à celle-ci dessous devrait s'ouvrir. Cliquez sur "New source" puis sur "Custom App"

![Input](/Pictures/008.png)

Sur la droîte, un panneau devrait s'ouvrir. Donnez un nom à la source. Cliquez sur le bouton "Add"

![Eventstream](/Pictures/009.png)

Une fois votre entrée créée, cliquez dessus. Dans la partie inférieure centrale de l'écran cliquez sur "Keys".

Notez les valeurs pour les éleéments suivants, nous allons en avoir besoin ultérieurement :
- Event hub name
- Primary Key

 
![Eventstream](/Pictures/010.png)


### Création de la destination des évènements

Cliquez sur "New destination", puis sur "KQL Database".

![Output](/Pictures/011.png)

Choisissez "Direct ingestion" (Dans le cas où vous souhaiteriez faire des transformations avant l'envoie vers la destination, vou)


# Azure
# Azure logic apps

Depuis le [portail Azure](https://portal.azure.com), allez dans votre service Azure logic app
