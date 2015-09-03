%Openstack, open cloud framework
%Malik Bougacha
%25 août 2015

# Introduction

### openstack

\center
\includegraphics[width=10cm]{img/logo.png}

### Framework Open source de cloud prive

* Framework
    assemblage de technologie
\pause
* Framework
    assemblage de technologie
\pause
* Open source
    Source accessible, modifiable et libre d'acces

\pause
* Cloud 
    A la demande 
    Couche d'abstraction du materiel et de l'infrastructure

\pause
* prive
    Le materiel vous appartient 

### Cloud 

Encore une couche d'abstraction !

\pause

resources informatique comme un service.

* Manageable sans connaissance des couches du dessous

### Private cloud

Gestion du cloud en interne de l'entreprise

### Hybrid cloud

Abstraction de resources externe a travers une api interne

Exemple: merger switch cloud et un cloud prive

### Niveaux d'abstraction

\center
\includegraphics[width=10cm]{img/abstraction.png}

### On demand

* Utilisateur interagissent avec le framework pour acquerir une resource.
    \pause

    * Serveur
    \pause

    * Reseau
    \pause

    * Base de donnee
    \pause

    * Entree dns
    \pause

    * .  \pause . \pause . \pause .

* Minimum d'interaction avec l'administrateur (changement des quotas d'utilisation des resources)

* Gestion et management de ces resources.

### Interfaces

* api (HTTP)
\pause

* libraries
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
* mariadb 
...
\pause

Transparent pour l'utilisateur


# Openstack les entrailles

### Openstack est simple

\center
\includegraphics[width=10cm]{img/archi.jpg}

### Openstack est simple

Par morceau 
:)

## Autour d'openstack

### Queing system 
rabbitmq:

\includegraphics[width=1cm]{img/rabbitmq_logo.png}

\pause

* erlang based 

\pause

* quasi incassable™

\pause

* triviallement clusterisable

```erlang
{cluster_nodes, {['rabbit@node0', 'rabbit@node1'], disc}},
```

### Database system 

mariadb with galera:

\includegraphics[width=3cm]{img/galera_logo.png}

\pause

* fork of mysql after the glorious sun

\pause

* replication en master/master synchrone

\pause

* Gestion relativement aisee

\pause

* Replication et resolution des conflicts

### load balancer

haproxy:

\includegraphics[width=3cm]{img/haproxy_logo.png}

\pause

* load balancer

\pause

* Support de multiple protocol et de multiple verification des hosts



### balancage dynamique d'ip:

keepalived:

\includegraphics[width=3cm]{img/keepalived_logo.png}

\pause

* Support pour le balancage d'ip (vrrp)

\pause

* Support de multiple type de verification pour les services sous jacent

\pause

* Utilise en dessus du load balancer afin de garantir l'availability du load-balancer

### cache

memcache:

\includegraphics[width=3cm]{img/memcache_logo.png}

\pause

* stockage cle/valeur en memoire

\pause

* Simple et rapide


### Gestion du switching openflow:

openvswitch:

\includegraphics[width=3cm]{img/ovs_logo.png}

\pause

* Support d'openflow

\pause

* Gestion du tagging, gestion des tunnels gre ....

\pause

* Tres facilement scriptable, api disponible 

## Openstack base services

### Horizon 

Couche de presentation:

* application django modulable.
* modulable en fonction des services ajoutes a la stack.
* couvre la majorite des fonctions de la stack ainsi que les fonctions de management

### Keystone

* Responsable de l'identity

    * service
    * list des points d'acces aux apis
    * authorization
    * authentification

Backend:

* utilise avec suivant l'utilisateur une query dans le ldap ou dans mariadb.
    * utilisateur humain                 ==> ldap
    * service ou modification de l'admin ==> mariadb


### Cinder

Interface de stockage.

* creation des volumes des machines virtuelless.
* creation de volumes supplementaire et attachement au vms
* hosting des images utilisee pour le demarrage des vms

Backend:

* ceph: stockage generique object d'un project talk

### Glance

Interface de stockage des images.

* creation et manipulation 
* hosting des images utilisee pour le demarrage des vms
* Gestion des caches sur les hyperviseurs a travers le getionnaire de machine virtuelles

\pause

Backend:

* filesystem: stockage local et replication sur les controlleurs
* ceph: stockage distant sur le cluster ceph

### Nova

Gestion des machines virtuelles

* Gestion des hyperviseurs
* hosting des images utilisee pour le demarrage des vms
* Gestion des volumes associe a travers cinder

\pause

Backend:

* kvm: virtualisation integre dans le noyaux linux.
* Par default utilisation de ceph Comme backend de stockage

### Neutron

Gestion du reseau

* Gestion des reseaux:
    * Reseau virtuel (vxlan, GRE pure)
    * Reseau 'physique' (vlan et natif)
* Gestion des resources reseau des machines:
    * Port des machines
    * MAC address 
* Gestion des services lie au reseau:
    * Load balancer
    * Firewall
    * Routeur (nat et routing)
\pause
* Gestion du reseau au niveau des hyperviseurs
* Gestion du reseau au niveau du server neutron

\pause

Backend:

* Openvswitch agent sur tous les noeuds

### Petit quizz !

\pause

* Qui dois je interroger pour obtenir un mapping mac-ip ?

\pause

* Qui dois je interroger pour obtenir une liste des services disponibles ?

\pause

* Qui dois je interroger pour obtenir une liste des images ?

\pause

* Qui dois je interroger pour demarrer une machine virtuelle ?

## Openstack additional services

### Trove

Gestion des bases de donnees

* Agent sur une base de donnee deja existante
* Creation depuis 0, creation du serveur et installation de la db.
* Creation rapide d'une db et des utilisateurs associes.
* Integration a horizon

### Designate

Gestion des entrees DNS

* Multiple DNS suporte (bind principalement mais aussi )
* Interface http
* Integration dans nova (a la creation d'une vm)

### Other 
* Murano: Saas
* Ironic: Bare metal provisioning (PXE)
* Ceilometer: Metering 
* Barbicane: Secret management
* ...

See http://git.openstack.org/cgit/openstack/governance/plain/reference/projects.yaml


## Openstack type de service
### Openstack type de service
Suivant l'interaction et le role que le service a avec son environement, le service est separe en composant
Nom standard a travers la stack

### Api
Point d'entree
Delegue au autre services en fonction de la demande client

### Scheduler
Decide de l'endroit ou la tache va etre effecture
* emplacement du volume
* emplacement de la machine virtuel

### 

Decide de l'endroit ou la tache va etre effecture

# Notre openstack 
### Generic organisation

Loosely coupled services
organized in abstraction level:

* Module
* Profile
* Role

### Module

une technologie

### Profile

Simple composant 
Unite atomique de gestion.
Responsable de 

* la creation ou la demande de creation de la base de donnee
* la creation ou la demande de creation des access au stockage (systeme de fichier ou access a ceph)
* la creation ou la demande des credentials d'acces au bus de messagerie
* Ajout dans le load balancer
* ...

\pause

Example:

* Nova server
* Nova client (noeud d'hypervisor)
* Neutron server
* Neutron client (agent)

### Role

Mapping 1-1 avec un server.
Set de profile definissant le set de service applique sur le serveurs

* controller
* compute
* controller_ha

### Server 

Application d'un seul role

### Controlleur

* API et serveurs
* queues
* interface

### Compute 

* KVM

# Conclusion

Openstack ne doit pas etre pris comme un seule bloc.

Comme un ensemble de services assemble pour fonctionner de facon tres stable.

Perte d'un seule sous systeme n'affecte que rarement le fonctionnement de l'ensemble.
