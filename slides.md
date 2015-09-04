%Openstack, open cloud framework
%Malik Bougacha
%4 septembre 2015

# Introduction

### Openstack

\center
\includegraphics[width=10cm]{img/logo.png}

### Framework Open source de cloud privé

* Framework:
    Assemblage de technologies.

\pause

* Open source:
    Source accessible, modifiable et libre d'accès.

\pause

* Cloud:
    Service à la demande, self-service.
    Couche d'abstraction de ressource et de l'infrastructure

\pause

* Privé:
    Le matériel vous appartient !

### Cloud

Encore une couche d'abstraction !

\pause

Abstraction des ressources, présentation de ressources en tant que services

* Gérable sans connaissance des couches du dessous.

\pause

* Cloud privé: Gestion des ressources abstraite en interne de l'entreprise.

\pause

* Cloud publique: Gestion du cloud de l'entreprise.

\pause

* Cloud hybride: Abstraction de ressources externe à travers une api interne.
    * Exemple: Abstraire l'Openstack de Switch et l'Openstack EPFL

### Niveaux d'abstraction

\center
\includegraphics[width=10cm]{img/abstraction.png}

### A la demande

* Utilisateur Interagissant avec le framework pour acquérir une ressource.
    \pause

    * Serveur
    \pause

    * Réseau
    \pause

    * Volume de stockage
    \pause

    * Entrée dns
    \pause

    * ...

\pause

* Minimum d'interaction directe avec l'administrateur (changement des quotas d'utilisation des ressources).

* Gestion et management de ces ressources.

### Interfaces

* Api (HTTP)
\pause

* librairies
    * python
    * ruby
    * perl
\pause

* Client en ligne de commande
\pause

* Interface web

### Service interface

* container lxc
* kvm machines
* fiber channel volumes
* bind entries
...
\pause

Opaque pour l'utilisateur


# Openstack les entrailles

### Openstack est simple

\center
\includegraphics[width=10cm]{img/archi.jpg}

### Openstack est simple

Par morceau
:)

## Autour d'Openstack

### Queuing system
rabbitmq:

\includegraphics[width=1cm]{img/rabbitmq_logo.png}

\pause

* Écrit en erlang.

\pause

* Quasi incassable™.

\pause

* Trivialement clusterisable.

```erlang
{cluster_nodes, {['rabbit@node0', 'rabbit@node1'], disc}},
```

### Base de donnée relationnelle

mariadb avec galera:

\includegraphics[width=3cm]{img/galera_logo.png}

\pause

* Fork de mysql, open source

\pause

* Réplication en master/master synchrone.

\pause

* Gestion relativement aisée.

\pause

* Réplication et résolution des conflits.

### Load-balancer

haproxy:

\includegraphics[width=3cm]{img/haproxy_logo.png}

\pause

* Support de multiples protocoles.

\pause

* Multiples vérifications des hosts.

### Balançage dynamique d'ip:

Keepalived:

\includegraphics[width=3cm]{img/keepalived_logo.png}

\pause

* Support pour le balançage d'ip (VRRP).

\pause

* Support de multiple type de vérification pour les services sous-jacent.

\pause

* Utilisé en dessus du load-balancer afin de garantir la disponibilité du load-balancer.

### Cache

memcached:

\includegraphics[width=3cm]{img/memcache_logo.png}

\pause

* Stockage clé/valeur en mémoire.

\pause

* Simple et rapide.


### Gestion du Réseau

openVSwitch:

\includegraphics[width=3cm]{img/ovs_logo.png}

\pause

* Support d'openflow

\pause

* Gestion du tagging.

\pause

* Gestion des tunnels GRE.

\pause

* Très facilement scriptable, api disponible dans de nombreux langages.

## Service de base d'Openstack

### Horizon

Couche de présentation:

* Application django.
* Modulable en fonction des services ajoutés à la stack.
* Couvre la majorité des fonctions de la stack ainsi que les fonctions de management.

### Keystone

* Responsable de l'identité

    * Service
    * Liste des points d'accès aux apis
    * Autorisation
    * Authentification

\pause

Backend:

* LDAP: Utilisateur humain
* mariadb: Service ou modification de l'admin

### Cinder

Interface de stockage.

* Création des volumes des machines virtuelles.
* Création des volumes supplémentaires et attachement aux vms.
* Hosting des images utilisées pour le démarrage des vms.

\pause

Backend:

* CEPH: stockage générique object

### Glance

Interface de stockage des images.

* Création et manipulation
* Hosting des images utilisées pour le démarrage des VMS
* Gestion des caches sur les hyperviseurs à travers le gestionnaire de machines virtuelles

\pause

Backend:

* filesystem: stockage local et réplication sur les contrôleurs
* CEPH: stockage distant sur le cluster CEPH

### Nova

Gestion des machines virtuelles

* Gestion des hyperviseurs
* Hosting des images utilisées pour le démarrage des vms
* Gestion des volumes associé à travers cinder

\pause

Backend:

* KVM: virtualisation intégrée dans le noyaux linux.
* CEPH: Par défaut comme backend de stockage

### Neutron

Gestion du réseau

* Gestion des réseaux:
    * Réseau virtuel (vxlan)
    * Réseau physique (vlan et natif)
    * Port des machines
    * Adresse MAC

* Gestion des services lié au réseau:
    * Load-balancer
    * Firewall
    * Routeur (nat et routing)

\pause

Backend:

* OpenvSwitch: support uniquement pour le bridging, prévoir le futur.

### Petit quiz !

\pause

* Qui dois je interroger pour obtenir un mapping mac-ip ?

\pause

* Qui dois je interroger pour obtenir une liste des services disponibles ?

\pause

* Qui dois je interroger pour obtenir une liste des images ?

\pause

* Qui dois je interroger pour démarrer une machine virtuelle ?

## Service additionnel d'Openstack

### Trove

Gestion des bases de données

* Agent sur une base de donnée déjà existante
* Création depuis 0
    * Création du serveur
    * Installation de la base de donnée
* Création rapide d'une base de donnée et des utilisateurs associés.
* Intégration à horizon

### Designate

Gestion des entrées DNS

* Multiple DNS supporté (bind principalement mais aussi PowerDNS).
* Interface HTTP.
* Intégration dans nova (à la création d'une VM).

### Autre
* Murano: Saas dans Openstack
* Ironic: Bare metal provisioning (PXE)
* Ceilometer: Metering
* Barbicane: Secret management
* ...

See http://git.Openstack.org/cgit/Openstack/governance/plain/
reference/projects.yaml


## Openstack: type de service

### type de service
Suivant l'interaction et le rôle que le service a avec son environnement, le service est séparé en composants.

Nom standard à travers la stack

### Api

Point d'entrée.

\pause

Délègue aux autres services en fonction de la demande client.

### Scheduler

Décide de l'endroit où la tâche va être effectuée

* Emplacement du volume
* Emplacement de la machine virtuel


# Notre Openstack

### Organisation générique

Organisé en niveau d'abstraction

* Module
* Profile
* Rôle

### Module

une technologie

### Profile

Simple composant
Unité atomique de gestion.
Responsable de

* la création ou la demande de création de la base de donnée
* la création ou la demande de création des accès au stockage (système de fichier ou accès a CEPH)
* la création ou la demande des droits d'accès au bus de messagerie
* Ajout dans le load-balancer
* ...

\pause

Exemple:

* Nova serveur
* Nova client (nœud d'hyperviseur)
* Neutron serveur
* Neutron client (agent)

### Rôle

Mapping 1-1 avec un serveur.
Set de profile définissant le set de service appliqué sur le serveurs

* controller
* compute
* controller_ha

### Serveur

Application d'un seul rôle

### Compute

* KVM
* nova client
* neutron client

### Controlleur

* API et serveurs
* queues
* interface


# Conclusion

### Conclusion

Openstack ne doit pas être pris comme un seule bloc.

Il s'agit d'un assemblage des meilleurs technologies open source accessible.

Comme un ensemble de services assemble pour fonctionner de façon très stable.

Perte d'un seule sous système n'affecte que rarement le fonctionnement de l'ensemble.

### Petit mot de la fin 

Don't hesitate to patch !
