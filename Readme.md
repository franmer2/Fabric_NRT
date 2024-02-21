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

cliquez sur le bouton "+ New " puis sur "KQL Database"

![KQL](/Pictures/003.png)

Donnez un nom à votre base KQL et cliquez sur le bouton "Create"

![Create](/Pictures/004.png)




# Azure logic apps

Depuis le [portail Azure](https://portal.azure.com), allez dans votre service Azure logic app
