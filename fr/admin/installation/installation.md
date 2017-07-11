# Installation

### Sur un serveur dédié (méthode conseillée)

Pour installer wallabag, vous devez exécuter ces commandes :

```bash
git clone https://github.com/wallabag/wallabag.git
cd wallabag && make install
```

Pour utiliser Wallabag avec LDAP, remplacer la dernière commande par
```bash
cd wallabag && make install LDAP_ENABLED=true
```
Voir plus bas pour la configuration spécifique de LDAP.

Pour démarrer le serveur interne à php et vérifier que tout s'est
installé correctement, vous pouvez exécuter :

```bash
make run
```

Et accéder wallabag à l'adresse <http://lipdevotreserveur:8000>

Pour définir des paramètres via des variables d'environnement, vous
pouvez les spécifier avec le préfixe `SYMFONY__`. Par exemple,
`SYMFONY__DATABASE_DRIVER`. Vous pouvez lire la [documentation
Symfony](http://symfony.com/doc/current/cookbook/configuration/external_parameters.html)
pour en savoir plus.

### Sur un serveur mutualisé

Nous mettons à votre disposition une archive avec toutes les dépendances
à l'intérieur. La configuration par défaut utilise SQLite pour la base
de données. Si vous souhaitez changer ces paramètres, vous devez
modifier le fichier `app/config/parameters.yml`.

Nous avons déjà créé un utilisateur : le login et le mot de passe sont
`wallabag`.

Avec cette archive, wallabag ne vérifie pas si les extensions
obligatoires sont présentes sur votre serveur pour bien fonctionner (ces
vérifications sont faites durant le `composer install` quand vous avez
un serveur dédié, voir ci-dessus).

Exécutez cette commande pour télécharger et décompresser l'archive :

```bash
wget https://wllbg.org/latest-v2-package && tar xvf latest-v2-package
```

Vous trouverez [le hash md5 du dernier package sur notre
site](https://static.wallabag.org/releases/).

Maintenant, lisez la documentation ci-dessous pour crééer un virtual
host. Accédez ensuite à votre installation de wallabag. Si vous avez
changé la configuration pour modifier le type de stockage (MySQL ou
PostgreSQL), vous devrez vous créer un utilisateur via la commande
`php bin/console wallabag:install --env=prod`.

### Installation avec Docker

Nous vous proposons une image Docker pour installer wallabag facilement.
Allez voir du côté de [Docker
Hub](https://hub.docker.com/r/wallabag/wallabag/) pour plus
d'informations.

#### Commande pour démarrer le containeur

```bash
docker pull wallabag/wallabag
```

### Installation sur Cloudron

Cloudron permet d'installer des applications web sur votre serveur
wallabag est proposé en tant qu'application Cloudron et est disponible
directement depuis le store.

[Installer wallabag sur
Cloudron](https://cloudron.io/store/org.wallabag.cloudronapp.html)

## Installation avec LDAP

Wallabag peut être utilisé avec LDAP. Pour activer LDAP, il faut ajouter le
composant correspondant à l’installation, et éditer le fichier
`app/config/parameters.yml` pour le configurer :
 - `ldap_enabled`: booléen, `true` pour activer l’authentification via LDAP
 - `ldap_host`: hôte LDAP auquel se connecter.
 - `ldap_port`: port LDAP à utiliser.
 - `ldap_tls`: booléen, Utiliser TLS (ne peut être utilisé simultanément avec SSL)
 - `ldap_ssl`: booléen, Utiliser SSL (ne peut être utilisé simultanément avec TLS)
 - `ldap_bind_requires_dn`: booléen, `true` pour utiliser le DN pour se
   connecter à LDAP
 - `ldap_manager_dn`: DN de l’instance Wallabag qui peut chercher des
   utilisateurs.
 - `ldap_manager_pw`: Mot de passe de l’instance Wallabag qui peut chercher des
   utilisateurs.
 - `ldap_filter`: Filtre pour rechercher des utilisateurs wallaabag.
 - `ldap_username_attribute`: attribut LDAP pour le pseudonyme.
 - `ldap_email_attribute`: attribut LDAP pour l’E-mail.
 - `ldap_name_attribute`: attribut LDAP pour le nom.
 - `ldap_enabled_attribute`: attribut LDAP pour vérifier qu’un utilisateur est
   activé (optionnel, par défaut `null` considère tous les utililsateurs comme
   actifs)
 - `ldap_admin_filter`: Filtre LDAP pour vérifier si l’utilisateur a les droits
   administrateurs. "%s" est remplacé par le login entré par l’utilisateur

