---

copyright:
  years: 2015, 2018
lastupdated: "2018-12-12"
subcollection: "liberty"

---

{:new_window: target="_blank"}
{:codeblock: .codeblock}

# Utiliser New Relic pour surveiller Liberty dans {{site.data.keyword.cloud_notm}}
{: #new_relic}

New Relic est un service tiers qui fournit des mesures de surveillance pour votre application. Pour plus d'informations sur le service fourni par New Relic, voir [New Relic](http://newrelic.com/java).

D'après la [documentation relative à l'installation manuelle de l'agent Java](https://docs.newrelic.com/docs/agents/java-agent/installation/java-agent-manual-installation), les applications Java qui doivent être surveillées par le service New Relic doivent généralement être regroupées et configurées avec un agent New Relic et une clé de licence de compte. Dans l'environnement {{site.data.keyword.Bluemix}}, vous pouvez obtenir un contrat de licence et un compte New Relic en créant une instance de service dans {{site.data.keyword.Bluemix_notm}}. Les applications Java peuvent ensuite être liées à l'instance de serveur New Relic et le pack de construction Liberty configure automatiquement l'application prête à être surveillée par le service New Relic.
Le pack de construction :

* fournit un agent New Relic à l'application,
* obtient le nom de l'application et la clé de licence à partir des variables d'environnement VCAP_APPLICATION et VCAP_SERVICES,
* configure les propriétés et le modèle de configuration requis par l'agent New Relic.

Voici un exemple de configuration généré par le pack de construction Liberty pour l'application :

```
    -javaagent:/home/vcap/app/.new_relic_agent/new_relic_agent-3.12.0.jar
    -Dnewrelic.home=/home/vcap/app/.new_relic_agent
    -Dnewrelic.config.license_key=123456
    -Dnewrelic.config.app_name=myapp
    -Dnewrelic.config.log_file_path=../../../../../logs
```
{: codeblock}


## Ajout d'un service New Relic fourni par l'utilisateur
{: #add_user_provided_new_relic}

Si vous disposez d'un compte New Relic existant et d'une clé de licence, vous pouvez lier le service New Relic existant à l'application à l'aide d'un "service fourni par l'utilisateur".

1. Créez une instance de service fourni par l'utilisateur avec votre clé de licence existante.  Si, par exemple, votre clé de licence existante est 1234567, vous pouvez utiliser l'interface de ligne de commande {{site.data.keyword.Bluemix_notm}} pour entrer la commande "create-user-provided-service" et indiquer la clé de licence 1234567 à l'invite, comme illustré ci-dessous :

  ```
    ibmcloud cf create-user-provided-service mynewrelic -p "licenseKey"
    licenseKey> 1234567
  ```
  {: codeblock}

2. Déployez votre application sur {{site.data.keyword.Bluemix_notm}} avec l'instance de service New Relic fournie par l'utilisateur.  Voici un exemple de manifeste d'application qui utilise une instance de service New Relic fournie par l'utilisateur :
  <pre>
        &dash;&dash;&dash;
        applications:
        - name: myapp
         memory: 1G
         instances: 1
         host: myapp
         domain: mybluemix.net
         path: myapp.war
         services:
         - mynewrelic
  </pre>
  {: codeblock}

3. Accédez au tableau de bord New Relic pour afficher les mesures de l'application.

La configuration automatique du service New Relic diffère de la configuration automatique des autres services car un service géré par le conteneur est rendu disponible via l'infrastructure du pack de construction.  La configuration automatique de ce service diffère donc en trois points :
* Il n'est pas possible de résilier.
* L'intégration de service repose sur l'agent New Relic, un agent Java. Par conséquent, elle est configurée via les options Java et non via les variables cloud du fichier server.xml.
* La configuration est basée sur les variables VCAP_SERVICES et VCAP_APPLICATION.
