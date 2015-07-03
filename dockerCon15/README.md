# DockerCon 2015 - La grande messe de Docker

Le 22 et 23 juin dernier s'est déroulée à San Francisco la DockerCon. Nous y étions invités en tant que vainqueurs du Docker Hackathon de décembre et nous n'avons pas été décus : ambiance joviale, talks de qualité et grandes annonces ont rendu cet evennment exceptionnel.

![Docker Con](DockerConBanner.jpg)

## DockerCon Hackathon

![Hackathon](HackathonBanner.jpg)

La DockerCon commence samedi 20 avec le Hackathon : une compétions ouverte à tout développeur qui a envie de se lancer, en à peine 24h, dans le développement d'un projet from scratch autour de Docker. Deux-cent développeurs sont au rendez-vous et une 40ène de projets seront soumis. Tous les aspects sont représentés : orchestration, sécurité, monitoring etc...Parmi les nombreux projets en voici quelques-uns que nous avons particulierement aprécié :
* Build and run iOS apps with the docker client: https://github.com/jkingyens/dockerthon
* Security updates through Docker: https://github.com/advanderveer/docksec
* LightningStorm: Multiple providers for Kitematic using docker-machine (e.g. Kitematic on steroids): https://github.com/fsoppelsa/kitematic
* Calcul distribué avec Docker et Rancher par l'équipe de [CommitStrip|http://www.commitstrip.com/en/about/] (@ipernet et @ThomasGX ): https://github.com/ipernet/docker-hackathon-2015

Le projet qui remporte le premier prix est un client Docker vocal pour [Cortana|https://fr.wikipedia.org/wiki/Cortana_(Microsoft)], le "Siri" de Microsoft, développé par @HaishiBai2010 et @danielfe.

Nous avons participé à l'épreuve avec le projet [the Phedds|https://github.com/mjbright/thephedds/] qui a suscité de l'interet avant et après le hackathon mais qui n'a pas été retenu parmi les 12 projets qui ont participé à la phase finale.

Ci dessous les t-shirts offertes aux participants du Hackathon réalisés par CommitStrip. Oui encore eux et nous sommes des grands fan chez Zenika !

((/public/Billet_0xxx/HackathonTShirts.png|Hackathon Tshirts|C))

## 1er jour - Les nouveautés de la keynote
C'est Ben Golub, le CEO de Docker, le premier à monter scène. Après quelques remerciment il passe la parole au CTO Solomon Hykes qui commence par rappeler la mission de Docker :

"Our mission is to build tools for mass innovations" 
((/public/Billet_0xxx/ToolsForMassInnovation.png|Tools for mass innovation|C))

Le discours de Solomon est clair et convaincant. Le logiciel est le moyen plus puissant aujourd'hui pour innover. Et Docker veut simplifier au maximum le développement et la pubblication de logiciel. Les quattres objectifs que Docker se donne pour les prochaines années en découlent: 
1. Reinvent the developer toolbox
2. Build better plumbings 
3. Open Standards
4. Help Organisations solve real world problems

Les annonces qui suivent au cours de cette keynote addressent les trois permiers objectifs. Le quatrième sera le sujet de la deuxième journée.

### Docker network et les nouvelles versions de machine, compose et swarm
((/public/Billet_0xxx/Networking.png|Networking|C))
Le premier jour de la DockerCon coincide avec la release de la version 1.7 de Docker. La nouveauté plus importante de cette version est l'integration du nouveau module réseau [libnetwork|https://github.com/docker/libnetwork] dans le engine. Cela rend plus flexible la configuration réseau (grace à un systeme de plugin que nous verrons plus tard) mais surtout le support de configurations multihost. C'est a dire qu'il sera possible établir des liens (*links*) entre conteneurs sur des hosts distants. Cette dernière fonctionalitée n'est pas livrée avec la version 1.7 mais est disponible uniquement avec [la release experimentale | https://experimental.docker.com/].

((/public/Billet_0xxx/MachineCompose.png|MachineCompose|C))
Machine, compose et swarm, les outils de orchestration de Docker, [ont aussi été mis à jour pour la DockerCon|http://blog.docker.com/2015/06/compose-1-3-swarm-0-3-machine-0-3/]. Ces outils ne sont pas encore assez murs pour êtres utilisés en production donc les efforts ont surtout visé à la stabilisation du code. 
Mais ceci n'a pas empéché d'ajouter deux nouvautés majeures en ce qui concerne swarm : l'integration avec mesos (i.e. la possibilité de deployer les conteneurs dans un cluster mesos en passant par swarm) et l'exploitation du nouveau module de networking de l'engine Docker (i.e. la possibilité de faire communiquer conteneurs qui se trouvent des noeuds différents).

### Docker plugins
((/public/Billet_0xxx/Extensions.png|Extensions|C))
Et on arrive à une des annonces plus attendues : le systeme de plugin Docker.
Le besoin est clair : Docker est relativement jeune et ne peux pas disposer de toutes les fonctionalités demandées par ses clients. Des    utilisé dans des environnements très etherogènes et des  et les derniers mois les demandes de 
https://clusterhq.com/2014/12/08/docker-extensions/
http://blog.docker.com/2015/06/extending-docker-with-plugins/

### Docker plumbing project et notary
((/public/Billet_0xxx/Notary.png|Notary|C))

### runC
((/public/Billet_0xxx/RunC.png|RunC|C))
https://github.com/opencontainers/runc
https://runc.io
Aujourd'hui le engine Docker est capable de faire beaucoup de choses : build d'une image, runtime d'un conteneur, . runC est un outil pour executer des 

### Open Container Format et Open Container Project
((/public/Billet_0xxx/OCP.png|OpenContainerProject|C))
L'année dernière, quelques jours avant la DockerCon Europeenne, CoreOS avait présenté un standard ouvert pour la définition du format des images de conteneurs : [appc|https://github.com/appc/spec]. Et avait livré en même temps, [rkt|https://github.com/coreos/rkt], un runtime qui implémente ce standard. Plutot qu'un attaque il s'agissait d'une incitation : Docker était dévenu le standard de facto mais n'avait pas défini des spécifications des conteneurs.
La réponse de Docker arrive aujourd'hui avec l'annonce du [Open Container Project|https://www.opencontainers.org/], une coalition de sociétés (Amazon, CoreOS, Docker, Google, IBM, Mesosphere, Microsoft, Rancher Labs, Red Hat, VMware etc...) unies pour définir un standard pour le format des containers. Ce projet sera maintenu par la Linux Foundation. La rédaction de [la spécification (Open Container Format)|https://github.com/opencontainers/specs] est encore en cours. Le premier draft sera pubblié à la fin du mois de juillet.  
Au moment de cette annonce, Alex Polvi, le CEO de CoreOS qui se trouvait au premier rang, s'est levé pou serrer la main à Solomon : la guerre du standard des conteneurs est terminée !

## 1er jour - Les talks

### Arnaud Porterie et Mike Cosby - Docker Engine
((/public/Billet_0xxx/Engine.png|Engine|C))
Sans aucun doute Arnaud et Mike ont la démo plus cool de toute la conférence. 

Arnaud Porterie (aka icecrime) et Mike Cosby, deux mainteneurs du Docker Engine  
Volume drivers
Network stack
    => libnetwork 
	=> userland-proxy
=> docker exec --user
=> ZFS storage driver

future: 
	breaking up docker in many componestns runtime (runC binary), build, trust (notary)
	security: trusted image distribution, user namespaces, syscall filtering with seccomp, engine security profiles
	networks and volume management: (docker network create, docker run --publish-, docker volume create)

demo:
	checkpoint / restore (rsync and iptables)

### Luke Marsden (ClusterHQ), Alexis Richardson (Weaveworks), (Glitter Labs) - Docker plugins
((/public/Billet_0xxx/Plugins.png|Plugins|C))

ClusterHQ + Weave + Glitter Labs
Different customers have different network and storage configurations => plugins
Today networking and data volumes plugins
docker run --publish-service=service.network.weave
          --volume-driver=flocker
demo....

## 2eme jour - Les nouveautés de la keynote

((/public/Billet_0xxx/Prod.png|Prod|C))
C'est encore Ben Golub qui prend la parole au début de la matinée. Il rapelle les thèmes du premier jours (Open Standards, Plumbing, Developer Platform) et introduit le thème de cette deuxième journée : Business Solutions. Si la première journée était plus orientée développeurs, la deuxième est pour les clients et les partners. Est-ce que Docker est prêt pour la prod ? Toutes les annonces de cette matinée seront des réponses à tous les clients qui posent cette question.

### Une nouvelle version du Docker Hub

### Docker Trusted Registry
((/public/Billet_0xxx/DockerTrustedRegistry.png|DockerTrustedRegistry|C))

### Project Orca
((/public/Billet_0xxx/Orca.png|Orca|C))

### Docker commercial solutions
https://docker.com/solutions

## 2eme jour - Les talks

### Jessie Frazelle - Contain yourself
((/public/Billet_0xxx/Contain.png|Prod|C))

### Mark Russinovich - Docker on Windows
((/public/Billet_0xxx/Windows.png|Windows|C))

## Rendez-vous à Barcelone
((/public/Billet_0xxx/Barca.png|Barca|C))







