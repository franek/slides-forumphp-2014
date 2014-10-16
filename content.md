name: inverse
layout: true
class: center, middle, inverse
---
#Retour d'expérience ARTE GEIE : développement API

???
Raccourci clavier : P -> permet d'afficher le plan
Raccourci clavier : C -> permet d'ouvrir une autre fenêtre avec les slides

---
# Ich heiße François Dume
---
# "First do it, 
# then do it right, 
# then do it better"

<!-- "It’s not at all important to get it right the first time. It’s vitally important to get it right the last time."
– Andrew Hunt and David Thomas-->

---
layout: false
class: layout-arte

.center[![ARTE](http://arte.tv/apiepg_static/arte_logo_mobile_128.png)]

.layout-arte-content[
* chaine TV franco-allemande
* canal 7 = M6+1
* Pas de Dominique Chapatte
* web : [arte.tv](http://www.arte.tv), [concert.arte.tv](http://concert.arte.tv), [future.arte.tv](http://future.arte.tv), [creative.arte.tv](http://creative.arte.tv)
* CatchUp : Arte+7
* Applications mobiles : [iOS](https://itunes.apple.com/fr/app/arte/id405028510?mt=8), [android](https://play.google.com/store/apps/details?id=tv.arte.plus7&hl=fr_FR)
* Applications ARTE+7 sur les box des opérateurs, HBBTV (applications TV connectées), ...
]
???
# Complexité/contraintes ARTE
* plusieurs entités : 
  * [ARTE GEIE](http://www.arte.tv/fr/arte-geie/7870412,CmC=2196658.html) (Strasbourg) : moyens techniques
  * [ARTE France](http://www.arte.tv/fr/arte-france/7870412,CmC=2196662.html) (Paris), [ARTE Deutschland](http://www.arte.tv/fr/arte-deutschland-tv-gmbh/7870412,CmC=2196668.html) (Baden-Baden) : production de contenu
* [soumis au code des marchés publics](http://www.arte.tv/fr/appels-d-offres/2196586.html)
* contenu franco-allemand
* heure de diffusion différente en France et en Allemagne
* petites équipes techniques
---
# Stack technique

![Drupal](http://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Druplicon.vector.svg/langfr-96px-Druplicon.vector.svg.png)
![Java](http://upload.wikimedia.org/wikipedia/fr/thumb/2/2e/Java_Logo.svg/180px-Java_Logo.svg.png 100x100)
![Ruby](http://upload.wikimedia.org/wikipedia/commons/thumb/0/0f/Ruby-logo-notext.png/131px-Ruby-logo-notext.png)
![Go](http://upload.wikimedia.org/wikipedia/fr/thumb/6/64/Go-logo-black.png/131px-Go-logo-black.png)
![Symfony2](http://upload.wikimedia.org/wikipedia/commons/thumb/6/60/Symfony2.svg/250px-Symfony2.svg.png)
![MongoDB](http://upload.wikimedia.org/wikipedia/commons/thumb/e/eb/MongoDB_Logo.png/96px-MongoDB_Logo.png)
![Redis](http://upload.wikimedia.org/wikipedia/en/thumb/6/6b/Redis_Logo.svg/200px-Redis_Logo.svg.png)
![Newrelic](http://storefront.nr-assets.net/assets/newrelic/logo_46-15b0d64a9d6a833df7a9ae04087f7d6e.png)
![JIRA](http://upload.wikimedia.org/wikipedia/en/thumb/b/bf/JIRA_logo.svg/150px-JIRA_logo.svg.png)
![Github](http://upload.wikimedia.org/wikipedia/commons/thumb/2/29/GitHub_logo_2013.svg/140px-GitHub_logo_2013.svg.png)
![docker](http://upload.wikimedia.org/wikipedia/commons/thumb/7/79/Docker_%28container_engine%29_logo.png/250px-Docker_%28container_engine%29_logo.png)
![Vagrant](http://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Vagrant.png/150px-Vagrant.png)

---
# A quoi servent les API d'Arte ? 

* mise à disposition :
  * des métadonnées des programmes diffusés à l'antenne (titre, description, photo, producteur, casting)
  * des URLs des streams (mp4, hls)
  * de flux optimisés pour une plate-forme dédiée (applications mobiles, TV connectées, ...)
  * des statistiques de consultation nos contenus

```json
  {
    "videos": [
        {
            "id": "055075-000-A_SHOW_ALW_FR_fr",
            "programId": "055075-000-A",
            "channel": "FR",
            "language": "fr",
            "kind": "SHOW",
            "platform": "ALW",
            "title": "Angus & Julia Stone \u00e0 la Maroquinerie",
            "originalTitle": "Angus & Julia Stone \u00e0 la Maroquinerie",
            "durationSeconds": 3101,
            "shortDescription": "Depuis Down The Way en 2010, Angus et Julia s'\u00e9taient \u00e9chapp\u00e9s chacun de leur c\u00f4t\u00e9 chantant l'un sans l'autre pendant un temps. La s\u00e9paration ne f\u00fbt heureusement pas d\u00e9finitive puisque les fr\u00e8res et soeurs retrouvent aujourd'hui le chemin vers de nouvelles sc\u00e8nes. Les retrouvailles se scellent \u00e9galement dans un troisi\u00e8me album o\u00f9 les puret\u00e9s folk et les m\u00e9lodies fredonn\u00e9es c\u00f4toient les ballades cotonneuses et les mots doux. ",
            "producer": "ARTE FRANCE",
            "videoRightsBegin": "2014-08-25T17:00:00Z",
            "videoRightsEnd": "2015-02-25T22:59:00Z",            
            "mainImage": {
                "name": "055075-000_1392179_32_202.jpg",
                "extension": "jpg",
                "caption": "Angus & Julia Stone",
                "url": "http:\/\/www.arte.tv\/prog_img\/IMG_APIOS\/055000\/055000\/055075-000_1392179_32_202.jpg"
            },
            "programmingId": 1781191,
            "mainReassembly": true,
            "reassembly": "A",
            "reassemblyRef": "A",

```
---
# Développement nouvelle API (OPA)
* **objectif** : mise à disposition de tout le contenu ARTE :
  * contenu antenne (broadcast, ARTE+7)
  * contenu spécifique Web (Concert, Future, Creative, Bonus web, ...)
* socle Symfony2/MongoDB
* reprise des fonctionnalités de l'API historique
* synchronisation des données via messages asynchrones (RabbitMQ)
* utilisation du standard [{json:api}](http://www.jsonapi.org)
* micro-services : création de plusieurs composants (authentification, API programme, API Player,...)
* sécurisé par authentification oAuth
* suivi de l'usage (throttling)

???
# API historique (PAPI)
* JAVA/MongoDB
* batch de synchro ORACLE -> MongoDB
* points forts : 
  * performance 
    * en moyenne 1500 requêtes/min sur les backends - hors Varnish/CDN
    * temps de réponse : <50ms
  * utilisée par de nombreux partenaires
* points faibles
  * asynchrone (synchro ORACLE / crontab)
  * uniquement contenu broadcast (contenu diffusé à l'antenne) ainsi que certains "habillages" (bande-annonce, bonus, ...)
  * pas de suivi de l'usage
  * trigramme pour certaines propriétés

---
# Migration en cours
* Symfony2, ça marche.
* OPA est déjà utilisé sur :
  * nombreux players sur les sites arte.tv
  * plate-forme [cinéma](http://cinema.arte.tv], nouveau site [Tracks](http://tracks.arte.tv) (développement module Drupal spécifique)
  * une partie des applications mobiles
* mise à jour prévue pour :
  * les applications TV connectées (fin d'année)
  * [concert.arte.tv](http://concert.arte.tv)
  * usine à sites

---
# Sécurisation des API et throttling
* API sécurisée par authentification oAuth
* Mise en place d'un reverse proxy authentifiant ([Openresty](http://openresty.org), distribution nginx avec support de Lua)
* Développement de scripts [Lua](http://fr.wikipedia.org/wiki/Lua) chargés dans la configuration Nginx permettant de valider token oAuth
* Toutes les API "sécurisées" sont protégées par ce Reverse Proxy
* Le reverse proxy authentifiant est également en charge du throttling (exemple : 1000 requêtes/heure)

---
# Exemple d'un process de requête à nos API

---

# 1. Interrogation du serveur oAuth pour récupérer un token oAuth

```bash
curl https://.../oauth/token?client_id=...&client_secret=...&grant_type=credentials
```

Réponse : 
```json
{
"access_token":"MDBjYzMzNTRjNTQxMjE2NGI0ODJkNTc3NGJhODFlOTc0MDY2ZjMyZTIxY2ExYjI1ZGIxYWYzMDZmYTgzM2UxMA",
"expires_in":3600,
"token_type":"bearer",
"scope":"user",
"refresh_token":"ZTJjZTEyOWFiNjQ1YTkwMWFhYmY4NjZhYmU3YTc3NjJiYzNlNWIyY2FlYjlhYjQ1MzAyOTA4OGFmZjA0YmI1Ng",
"roles":["USER"],
"rateLimit":1000
}

```

---

# 2. Utilisation du token

```bash
curl -I https://.../api1/resource?access_token=MDBjYzMzNTRjNTQxMjE2NGI0ODJkNTc3NGJhODFlOTc0MDY2ZjMyZTIxY2ExYjI1ZGIxYWYzMDZmYTgzM2UxMA
```

```data
HTTP/1.1 200 OK
Server: openresty
Date: Thu, 23 Oct 2014 19:51:39 GMT
Content-Type: application/vnd.api+json
Vary: X-ARTE-Roles
X-Rate-Limit-Limit: 1000
X-Rate-Limit-Remaining: 999
X-Rate-Limit-Reset: 1412887899
...
```

---

# 3. Après 1000 requêtes...

```bash
curl -I https://.../api1/resource?access_token=MDBjYzMzNTRjNTQxMjE2NGI0ODJkNTc3NGJhODFlOTc0MDY2ZjMyZTIxY2ExYjI1ZGIxYWYzMDZmYTgzM2UxMA
```

```data
HTTP/1.1 429 Too Many Requests
Server: openresty
```

---
# Schéma d'architecture globale

![schéma d'artchitecture](./schemas/architecture-api.png)

---
# Configuration nginx

```.nginxconf
location /api1 {
    # lua_code_cache off; # Dev : disable cache
    set $roles ''; # This variable is set by lua script
    access_by_lua_file /chemin/vers/oauth-throttle.lua;
    header_filter_by_lua_file /chemin/vers/oauth-header-filter.lua;
    proxy_pass http://api1.local;
    proxy_set_header X-Roles $roles;
}

# api2 n'est pas protégé par oAuth
location /api2 {
    proxy_pass http://api2.local;
} 
```
Pour aller plus loin, [documentation du module Lua pour Nginx](http://wiki.nginx.org/HttpLuaModule)

???
Description des directives
  * init_by_lua : script lua exécuté au démarrage du processus nginx principal (inclusion de librairie)
  * init_worker_by_lua : script lua exécuté au démarrage d'un worker nginx
  * content_by_lua : script de génération de contenu (~= php)
  * rewrite_by_lua : script exécuté après un traitement de réécriture d'URL
  * access_by_lua : script intervenant lors de l'accès à une ressource (permet de protéger une URL, par exemple)
  * header_filter_by_lua : permet d'ajouter des headers dans la réponse

---
# Exemple de scripts Lua

```lua
local userRateLimit = 1000
count = cache:get('throttle_' .. name)
if tonumber(count) > userRateLimit then
    ngx.header.content_type = 'application/json'
    ngx.status = 429
    ngx.say('{"message": "API rate limit exceeded. See documentation for details."}')
    ngx.exit(429)
end
```

sous-requête : 
```lua
local token = ngx.var.arg_access_token
local subrequest = ngx.location.capture('/oauth/checkToken?access_token=' .. token)
if subrequest.status == ngx.HTTP_OK then
  local content = subrequest.body
  ...
end
```

---
# Limites de la solution

* la première requête est plus lente (sous-requête vers le serveur oAuth)
  * on pourrait modifier le script lua pour lire la BDD du serveur oAuth
* tests de la solution
  * pas de framework de tests unitaires
  * mise en place de tests fonctionnels (casper-js, [frisby.js](http://frisbyjs.com/))
* peu de documentation (bientôt un article sur le blog d'Arte et/ou sur le blog de Jolicode - ping [@ThibZ](https://twitter.com/ThibZ))

---
# Solutions alternatives

* Implémentation via des bundle Symfony2 (est-ce que vous voulez vraiment recevoir une requête sur votre applicatif ?) : 
  * [noxlogic/ratelimit-bundle](https://github.com/jaytaph/RateLimitBundle) ([details](https://www.adayinthelifeof.nl/2014/05/28/throttle-your-api-calls-ratelimitbundle/))
* Implémentation Varnish
  * [libvmod-throttle](https://github.com/nand2/libvmod-throttle)
* Autres solutions ? (Vos retours m'intéressent)

---
# Standard [{json:api}](http://www.jsonapi.org)

* Convention pour décrire un appel REST au format JSON

> A JSON object MUST be at the root of every JSON API document. This object defines a document's "top level".
>
> A document's top level SHOULD contain a representation of the resource or collection of resources primarily targeted by a request (i.e. the "primary resource(s)").
>
> The primary resource(s) SHOULD be keyed either by their resource type or the generic key "data".
>
> A document's top level MAY also have the following members:
>
>  * "meta": meta-information about a resource, such as pagination.
>  * "links": URL templates to be used for expanding resources' relationships URLs.
>  * "linked": a collection of resource objects, grouped by type, that are linked to the primary resource(s) and/or each other (i.e. "linked resource(s)").
>
> No other members should be present at the top level of a document.

---
# Exemples

```bash
GET /users?limit=1
```

```json
{
  "meta": {
      "users": {
          "page": 1,
          "limit": 1,
          "pages": 5,
          "totalCount": 5,
          "href": "https://server/users",
          "links": {
              "first": "https://server/users?limit=5&page=1",
              "next": "https://server/users?limit=5&page=2",                
              "next": "https://server/users?limit=5&page=5",                
          }
      }
  },
  "users": [
      {
          "id": "gaston",
          "username": "Gaston",
          "href": "https://server/users/gaston",
          "links": {
              "groups": {"href": "https://server/groups?user=gaston"}
          }
      }
    }
  ]
}
```

---
# Inclusion de documents

[{json:api}](http://www.jsonapi.org) décrit la manière d'inclure des sous-documents (réduction du nom de requête)

```bash
GET /users?limit=1&include=groups
```

```json
{
...
  "users": [
      {
          "id": "gaston",
          "username": "Gaston",
          "href": "https://server/users/gaston",
          "links": {
              "groups": {"href": "https://server/groups?user=gaston"}
          }
      }
    }
  ],
  "linked" : {
    "groups": [
      {
        "id": "group1",
        "name" : "group1",
        "href": "https://server/groups/group-1",
      },
      {
        "id": "group-2",
        "name" : "group2",
        "href": "https://server/groups/group-2",
      },
    ]
  }
}

```
---
# Restriction des attributs renvoyés

[{json:api}](http://www.jsonapi.org) décrit comment limiter les attributs retournés : 
```bash
GET /users?limit=1&fields=id
```

```json
{
"users": [
      {
          "id": "gaston",
          "href": "https://server/users/gaston",
          "links": {
              "groups": {"href": "https://server/groups?user=gaston"}
          }
      }
    }
  ]
}
```

```bash
GET /users?limit=1&fields=id,name
```

```json
{
"users": [
      {
          "id": "gaston",
          "name": "Gaston",
          "href": "https://server/users/gaston",
          "links": {
              "groups": {"href": "https://server/groups?user=gaston"}
          }
      }
    }
  ]
}
```

---
# Syntaxe pour les filtres

```bash
GET /users?id=gaston,fantasio
```

```json
{
"users": [
      {
          "id": "gaston",
          "href": "https://server/users/gaston",
          "links": {
              "groups": {"href": "https://server/groups?user=gaston"}
          }
      },
      {
          "id": "fantasio",
          "href": "https://server/users/fantasio",
          "links": {
              "groups": {"href": "https://server/groups?user=fantasio"}
          }
      }      
    }
  ]
}
```

---
# Création de requête complexe 

Mélange de toutes les fonctionnalités de [{json:api}](http://www.jsonapi.org) 

```bash
GET /users?id=gaston,fantasio&tags=marsupilami,spirou&fields=name&include=groups&sort=-id
```

---
# Solutions mises en oeuvre 

* Surcharge de [BazingaHateoasBundle](https://github.com/willdurand/BazingaHateoasBundle)
  * utilisation d'annotations pour ajouter les links à la volée

* gestion des inclusions : utilisation d'un 'serializer.post_serialize'
  * dans le cas de l'inclusion d'une seule ressource, utilisation d'une sous-requête Symfony2 ([pull-request sur jms-serializer en attente](https://github.com/schmittjoh/serializer/pull/341))
  * dans le cas d'une inclusion multiple, utilisation de multi-curl (cache varnish)
* limitation des attributs retournés : 
  * [mise en place d'une classe ExclusionStrategy](http://jolicode.com/blog/how-to-implement-your-own-fields-inclusion-rules-with-jms-serializer) (ping [@damienalexandre](https://twitter.com/damienalexandre))

---
# Problèmes rencontrés

* performance du JMSSerializer :
  * création d'un bundle permettant de mettre en cache les "visitors"
  * le bundle devrait bientôt être libéré
  * ping [@xlacot](https://twitter.com/xlacot)
* performance de HateoasBundle :
  * création d'un bundle permettant de mettre en cache le mécanisme de serialization
  * presque libre : https://github.com/ArteGEIE/ArteHateoasBundle
  * ping [@xlacot](https://twitter.com/xlacot)
* performance de l'hydratation de DoctrineMongoDB

---
# Les autres bundles utilisés : 
 
 * [DoctrineMongoDBBundle](http://symfony.com/doc/current/bundles/DoctrineMongoDBBundle/index.html)
 * [FOSRestBundle](https://github.com/FriendsOfSymfony/FOSRestBundle) : 
 * [NelmioDocBundle](https://github.com/nelmio/NelmioApiDocBundle)
 * [EkinoNewrelicbundle](https://github.com/ekino/EkinoNewRelicBundle)
 * [oldsound/rabbitmq-bundle](https://github.com/videlalvaro/RabbitMqBundle)
 * ...

---
# Next ? 
* monitoring de l'usage : script Lua pour envoyer des métriques à StatsD ?
* mise à disposition de SDK pour faciliter l'utilisation de l'API par des partenaires externes : 
  * Work In Progress : Module Drupal
* ouvrir le code du serveur de l'API (cf. [The Guardian](https://github.com/guardian))
* ouvrir l'API à des développeurs externes (Open Data ?)
* HHVM ? 
* développement d'une app [Arte Firefox OS](https://www.mozilla.org/fr/firefox/os/) ? 

---
name: inverse
layout: true
class: center, middle, inverse
---
# Merci
(ainsi qu'à [<img height="30em" style="vertical-align:middle" src="http://jolicode.github.io/best-bundle-conf/lib/font/logo-fond-noir.svg" />](http://jolicode.com/))

---
# Des questions ? 