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

Avant d'aller plus loin avec Microsoft Fabric Eventstream, nous devons envoyer des données dans le moteur d'ingestion afin d'otenir le schéma de données.

Dans note exemple, nous alons déployer un Azure Logic Apps pour récupérer les données et les envoyer à Microsoft Fabric Eventstream. (il est bien entendu possible d'utiliser d'autres solutions comme les "Azure Functions").

# Azure
# Azure logic apps

Depuis le [portail Azure](https://portal.azure.com), allez dans votre service Azure logic app.
Dans le panneau de gauche, cliquez sur "Workflows" puis sur le bouton "Add", donnez un nom à votre Workflow et sélectionnez "Statefeul..." puis cliquez sur "Create".

![LogicApps](/Pictures/015.png)

Une fois votre "workflow" créé, cliquez dessus.

![LogicApps](/Pictures/016.png)

Cliquez sur "Designer" puis sur "Add a trigger"

![LogicApps](/Pictures/017.png)

Comme on désire récupérer les données toutes les 30 secondes, on va choisir un déclencheur de type récurrence. 
Dans la zone de recherche, entrez "recurrence", puis sélectionnez le déclecheur "Schedule / Recurrence".

![LogicApps](/Pictures/019.png)

Définissez les paramètres de votre déclencheur. Ici je demande un déclenchement toutes les 30 secondes.

![LogicApps](/Pictures/020.png)

Sous le déclencheur "Recurrence", cliquez sur le signe "+", cherchez avec le mot clef "https" puis choissisez l'action "HTTP"

![LogicApps](/Pictures/021.png)

Dans l'onglet "Parameters", entrez les valeurs suivantes pour les champs :
- **URI** : https://bct.tmix.se/gtfs-realtime/vehicleupdates.js?operatorIds=12
- **Method** : GET

![LogicApps](/Pictures/022.png)


En dessous de l'action "HTTP", cliquez sur le signe "+", recherchez "Event hub" et sélectionnez "Event Hubs / Sent **E**vent"

![LogicApps](/Pictures/023.png)

Si aucune connexion existe, vous devriez avoir la fenêtre suivante :

![LogicApps](/Pictures/024.png)

L'information concernant la "Connection string" se retrouve dans l'entrée de votre Microsoft Fabric Eventstream (l'information a été notée plus tôt dans cet article).
Cliquez sur le bouton "Create New".

La fenêtre suivante apparaît alors. Renseignez le nom de votre Eventstream dans le champ "Event Hub Name". Dans la liste déroulante "Advanced parameters" sélectionnez "Content".

![LogicApps](/Pictures/025.png)

Cliquez dans le champ "Content" afin de faire apparaîte la petite bulle bleue. Cliquez sur l'éclair. 

![LogicApps](/Pictures/026.png)

Puis sous l'action "HTTP" sélectionnez "Body".

![LogicApps](/Pictures/027.png)

Vous devriez avoir une fenêtre similaire à celle ci-dessous :

![LogicApps](/Pictures/028.png)

Cliquez sur le bouton "Save"

![LogicApps](/Pictures/029.png)

Afin de vérifier que tout se passe bien, après avoir sauvegarder votre "workflow", cliquez sur "Overview" et vérifier que votre "workflow"se déclenche bien toutes les 30 secondes et qu'une erreur n ést repportée dans la colonne "Status.

![LogicApps](/Pictures/030.png)


# Microsoft Fabric (suite)
 
Après avoir créer notre "workflow" Azure Logic Apps, retournez dans Microsoft Fabric, et plus précisement dans votre Eventstream. 
Cliquez sur sur votre Evenstream afin de vérifier que les évènements arrivent correctement.

![LogicApps](/Pictures/031.png)


### Création de la destination des évènements

Cliquez sur "New destination", puis sur "KQL Database".

![Output](/Pictures/011.png)

Choisissez "Direct ingestion" (Dans le cas où vous souhaiteriez faire des transformations avant l'envoie vers la destination, vous avez la possibilité de choisir l'option "Event processing before ingestion"). 
Puis renseignez les informations de votre base KQL. Cliquez sur "Add and configure".

![Output](/Pictures/012.png)

Maintenant, nous allons définir vers quelle table envoyer les données. Cliquez sur "New table".

![Picture](/Pictures/013.png)

Donnez un nom à votre table puis cliquez sur "Next".

![Picture](/Pictures/014.png)


Dans l'étape "Inspect the data", l'assistant devrait pouvoir retrouver un échantillon des données, Cliquez sur "Finish".

![Picture](/Pictures/032.png)

Puis durant l étape "Summary", cliquez sur "Close".

![Picture](/Pictures/033.png)

Votre process d'ingestion est maintenant prêt !

![Picture](/Pictures/034.png)

## KQL Database

Si tout va bien, votre table à du être créée dans votre base KQL. Ci dessous on peut voir ma table "FranmerBronze". Cliquez sur les 3 petits points à droite de la table afin d'aller chercher la commande "Show any 100 records"

![Picture](/Pictures/035.png)

La fenêtre "Explore your data" s'ouvre et vous donne un apreçu des données qui arrivent dans votre table.

![Picture](/Pictures/036.png)

En l'état, les données mne sont pas facilement exploitable pour la création d'un rapport presque temps réel. C'est ici que le langage Kusto entre en scène.

### Requête Kusto (KQL Queryset)

Grâce au langage Kusto, il est relativement assez simpe de créer un script qui permet de rendre les données plus facilement exploitable. 
Nous allons utiliser la fonctionnalité "KQL Queryset" afin d'écrire notre requête.

Depuis votre espace de travail, cliquez sur le bouton "New"puis sur le bouton "KQL Queryset".

![Picture](/Pictures/037.png)

Donnez un nom à votre "Queryset" et cliquez sur "Create".

![Picture](/Pictures/038.png)

Sélectionnez votre base KQL et cliquez sur le bouton "Connect".

![Picture](/Pictures/039.png)

Vous devriez avoir une fenêtre similaire à celle ci-dessous :

![Picture](/Pictures/040.png)

