# Définitions

- l'**identification** est une phase qui consiste à établir l'identité de l'utilisateur, par exemple via un login
- l'**authentification** est une phase qui permet à l'utilisateur d'apporter la preuve de son identité, par exemple via un mot de passe (vérification de l’identité d’un utilisateur)
- l’**autorisation** détermine les droits d’accès de la personne sur les objets (vérification des autorisations de la personne pour accéder aux ressources). L’autorisation est généralement effectuée après l’authentification


# OAuth 2.0

http://www.bubblecode.net/fr/2016/01/22/comprendre-oauth2/
https://www.ionos.fr/digitalguide/serveur/securite/oauth/
https://zestedesavoir.com/articles/1616/comprendre-oauth-2-0-par-lexemple/
https://nexworld.fr/securiser-api-oauth2/

https://www.tutorialspoint.com/spring_boot/spring_boot_oauth2_with_jwt.htm
https://www.toptal.com/spring/spring-boot-oauth2-jwt-rest-protection

- OAuth, abréviation de « Open Authorization », est un protocole standard ouvert permettant à des applications tierces d’obtenir un accès limité à un service par le biais d’une autorisation préalable du détenteur des ressources
- Par exemple : pour pouvoir poster un élément sur Facebook au nom de l’utilisateur (c’est-à-dire accéder à l’API de Facebook), une application doit présenter une autorisation dudit utilisateur. 
Grâce à OAuth, l’utilisateur peut accorder une telle autorisation sans avoir à communiquer à l’application autorisée son nom d’utilisateur ou son mot de passe

## Les rôles

OAuth2 définit 4 rôles bien distincts :
- le détenteur des données (**Resource Owner**) : généralement vous-même
- le serveur de ressources (**Resource Server**) : serveur qui héberge les données dont l’accès est protégé (par exemple Google qui stocke les informations de votre profil)
- le client (**Client Application**) : une application demandant des données au serveur de ressources (cela peut être votre application PHP côté serveur, une application Javascript côté client ou une application mobile par exemple)
- le serveur d’autorisation (**Authorization Server**) : serveur qui délivre des tokens (ou jetons) au client. Ces tokens seront utilisés lors des requêtes du client vers le serveur de ressources. Ce serveur peut être le même que le serveur de ressources (physiquement et applicativement), et c’est souvent le cas

## Les tokens

Les tokens sont des chaines de caractères générées par le serveur d’autorisation et sont émis lorsque le client en fait la demande
- le token d’accès (**Access Token**) : c’est le plus important car c’est lui qui permet au serveur de ressources d’autoriser la mise à disposition des données d’un utilisateur. Ce token est envoyé par le client (l’application) en tant que paramètre ou en tant que header dans la requête vers le serveur de ressources. Il a une durée de vie limitée qui est définie par le serveur d’autorisation. Il doit rester confidentiel dès que possible (non connu du détenteur de ressources) mais on verra que ce n’est pas toujours possible, notamment lorsque c’est votre navigateur qui envoie les requêtes au serveur de ressources via Javascript
- le token de renouvellement (**Refresh Token**) : ce token est délivré au même moment que le token d’accès mais n’est en revanche pas envoyé lors de chaque requête du client vers le serveur de ressources. Il sert simplement à être envoyé au serveur d’autorisation lorsque le token d’accès est expiré et que le client souhaite le renouveler. Pour des raisons de sécurité, il n’est pas tout le temps possible de disposer de ce token pour générer un nouveau token d’accès quand celui-ci est expiré. Nous verrons plus loin dans quels cas

## Les types d’autorisation

4 processus d’autorisation prédéfinis (**Grant Types**) utilisés dans différents cas d’application :

- code d’autorisation (**Authorization Code**)
    - le Client demande au Resource Owner de se connecter auprès d’un Authorization Server. Le Resource Owner est ensuite redirigé vers le Client avec un code d’autorisation. En échange de ce code, l’Authorization Server génère un jeton d’accès pour le Client
    - génère également un jeton de renouvellement
    - à utiliser plutôt quand le Client est un serveur web et que le jeton n'est pas transmis côté client (le navigateur)
- autorisation implicite (**Implicit Authorization**)
    - ce processus d’autorisation est fortement similaire à l’autorisation via un code d’autorisation, mais est moins complexe puisque l’Authorization Server génère directement le jeton d’accès
    - il doit être utilisé quand l’application se trouve côté client (typiquement une application Javascript). Il ne permet pas d’obtenir de jeton de renouvellement
- déblocage du mot de passe par le Resource Owner (**Resource Owner Password Credentials**)
    - dans ce cadre, le Resource Owner confie directement au Client ses données d’accès, ce qui va certes à l’encontre du principe de base de OAuth, mais permet un effort moindre pour le Resource Owner
    - principalement utilisé lorsque le client a été développé par la même autorité que celle fournissant le serveur d’autorisation
- autorisation Client (**Client Credentials**) : ce processus d’autorisation particulièrement simple est utilisé lorsque le Client souhaite accéder à des données n’ayant pas de propriétaire ou pour lesquelles aucune autorisation n’est nécessaire

## Déroulement du protocole

1. Le Client demande une autorisation au Resource Owner, soit directement soit via l’Authorization Server.
2. Le Resource Owner accorde une autorisation via un processus d’autorisation.
3. Sur la base de cette autorisation, le client demande un jeton d’accès à l’Authorization Server.
4. L’Authorization Server authentifie le Client à l’aide de son autorisation et génère un jeton d’accès.
5. Le Client utilise le jeton d’accès pour demander les données protégées du Resource Owner au Resource Server.
6. Le Resource Server authentifie le Client à l’aide de son jeton d’accès et met les données souhaitées à disposition.


# OpenID

- OpenID Connect (OIDC), pour Open Identification, est basée sur OAuth 2.0
- contrairement à OAuth, OpenID n’est pas un standard d’autorisation, mais d’**authentification**
