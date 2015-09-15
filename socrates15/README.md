# SoCraTes Conference 2015

SoCraTes, la conference majeur de la communauté craftsmanship en Europe, a eu lieu du 27 au 30 Aout, dans la campagne allemande près de Hambourg. Nous avons eu le plaisir d'y assister et allons vous le raconter dans cet article.

![image](kickoff.png)

## Jeudi

La conférence commence officiellement le jeudi soir à 19h avec un World Café. Le but de cette session est double : 

* Briser la glace entre les participants
* Faire émerger les idées et le attentes des participants

Le déroulement : les 180 participants sont repartis sur une vingtaine de tables. Chaque table discute de ce que chacun veut apporter à cette édition de SoCraTes. Toutes les idées sont notées sur une grande nappe en papier. Au bout de 20 minutes tous les participants sont invités à changer de table. L'historique des discussions de la table est assuré par une personne qui, contrairement à tous les autres, ne changera jamais de table.

Au bout de 3 rondes les notes sont récupérées par les facilitateurs et affichées dans la salle principale pour inspirer les sujets des sessions qui auront lieu les jours suivants. 

Exemples des sujets que nous avons noté : techniques de tests (BDD, TDD), Infrastructure as Code (Docker, Ansible), soft skills, programmation fonctionnelle, caractéristiques des bonnes entreprises, gamification, pair programming, peer review, vim. Il s'agissait, nous le découvrirons par la suite, que d'un petit aperçu des sujets qui seront proposés le long des deux jours.

## Open Space (vendredi et samedi)

Vendredi demarre le Open Space. Pour ceux qui se savent pas de quoi il s'agit utilisant les mots du facilitateur Pierluigi le résument très bien :
 
> Les discussions les plus interessants aux conférences se font souvent à l'occasion d'une pause café. Dans cet esprit un Open Space est une pause café qui dure deux jours.

L'organisation se fait au jour le jour le matin. Ceux qui veulent apporter quelque chose (proposer un talk, poser des questions, parler de leur projets) n'auront qu'à noter leur sujet sur un post-it et le présenter rapidement à tout le monde.

Voici la liste des sujets auxquels nous avons assistés et que nous avons trouvés interessants :

#### Les sujets de la première journée
![day1 schedule](day1schedule.png)


* Be Cat-matic ([@pawelduda](https://twitter.com/pawelduda/)): arrêtons d'être Dog-matic! Durant cette session, nous avons pris les sujets qui nous tiennent le plus à cœur et essayé de trouver les cas où il ne faut pas les utiliser.
* Javascript Koans ([@carlosble](https://twitter.com/carlosble/)): pendant une heure, nous avons ouverts no chakras à javascript en plongeant dans du code aux effets de bord étranges.
* Property based testing (@magicmonty): un échange sur les techniques pour générer automatiquement les paramètres pour nos tests
* Containers patterns ([@luebken](https://twitter.com/luebken/)): Très intéressante présentation d'un catalogue de différentes utilisations des conteneurs. Docker mais pas que.
* What the VIM (Jan @janernsting): parcequ'un bon éditeur devient un très bon éditeur quand il est bien configuré, nous avons échangés sur les plugins et autres astuces que nous connaissons sur vim (comme comment faire tourner vim dans emacs)
* Remote pair-programming (holger et raimo): deux outils pour faire du pair programming à distance : saros, un plugin eclipse et tmate, un outil qui se base sur tmux. Holger est un maintainer du projet saros.
* Show me your test pyramid (Thorsten Bevonendorf): nous connaissons tous le schéma de la pyramide de tests et sont anti-pattern connu sous le nom de cône de glace, mais d'autres «pyramides» de tests existent.


#### Les sujets de la deuxième journée
![day2 schedule](day2schedule.png)

* Haskell Test Driven Learning ([@xdetant](https://twitter.com/xdetant)): j'ai eu le plaisir de présenter comment j'apprends un nouveau language grâce à TDD. Je me suis appuyé sur Haskell pour cela.
* Your code as a crime scene (Stephan Segers) : note de lecture du livre omonyme, quelques idées pour dénichers les problèmes de notre code en analysant les données de nos vcs (les commits).
* NixOS ([@tpflug](https://twitter.com/tpflug/)) : les qualités (immutabilité et mises à jour très fréquentes) et les limites (courbe d'apprentissage longue) du package manager de NixOS que @tpflug utilise depuis plus d'un an. 
* Open Salary ([@luebken](https://twitter.com/luebken/)) : chez Giant Swarm ils ont décidé de ne rien cacher, tous les salaires sont connus et discutés dans des réunions où tous peuvent participer.
* TDD does not always lead to good design ([@sandromancuso](https://twitter.com/sandromancuso/)) : 
* Git Internals (Andreas Gayp) : content driven storage et ce qui se cache dans le folder objects (blob, tree, commit, tag)

## Workshops (dimanche)

Le dimanche il n'y a pas de Open Space. C'est le jour des workshops : code retreat, introduction à la programmation fonctionelle, google spreadsheet TDD etc...

Parmi tous nous avons choisi de participer à un Extreme Startup. Il s'agit d'un jeu ou nous devons développer un serveur http qui répond à des questions allant de «quelle est la couleur d'une banane?» à «quel était la monnaie utilisé en espagne avant l'Euro?» en passant par «quel est la 9ième valeur de la suite de fibonnaci?». Chaque bonne réponse rapporte des point et une fausse réponse en enlève.

Nous nous sommes régulièrement arrêtés afin de discuter sur ce qui marche et ce qui ne marche pas. Ce fût une expérience très enrichissante.

## Les autres activitées

SoCraTes n'est pas que des talks est des workshop. Voici une liste, non exhaustives, des activités auquelles nous avons participé :

* Running : rendez-vous tous les matins à 7h pour parcourir 5km dans la forêt de Soltau tout en discutant des talks auquels on a assisté.
* Jeux de société : le soir après diner les gamers se retrouvaient dans une salle pour 
* Crappy Tools : un paper board était affiché dans la grande salle et nous étions tous invités à y  les outils que nous détéstons.
* Power Point Karaoke : chaque concurant doit faire un talk mais le sujet est choisi aléatoirement et les slides n'ont strictement rien à voir avec (parler du zoo de Boston en s'appuyant sur des sl)
* Coding Dojo : Randori (Diamond), C64 TDD, Gilded Rose, intro à Ruby

## Conclusion

Si cet article vous a mis l'eau à la bouche et bien sachez que dans quelques semaines aura lieu [la première edition française de SoCraTes](https://socrates-fr.github.io/) !
