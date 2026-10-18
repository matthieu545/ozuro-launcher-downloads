# Sécurité de la bêta 0.4.35-beta.1

Cette version corrige plusieurs constats de l’audit de la 0.4.31. Elle conserve les choix de modes et l’interface tricolore.

- Les réparations MW2/MW3 préservent les configurations du joueur ; les sauvegardes et statistiques personnelles de l’auteur ne sont pas installées. Les anciens reçus, les sources importées et les superpositions Drive bénéficient de protections sur les chemins personnels connus, dont `players2`.
- Les fichiers Drive doivent correspondre à une taille et un SHA-256 de publication avant remplacement ou extraction. COD2 possède 94 références embarquées. Un téléchargement ou un cache falsifié est refusé ; la copie déjà installée est conservée. Les sources Drive personnalisées nécessitent désormais un manifeste de confiance.
- Electron est actualisé en 44.9.0. La validation d’intégrité ASAR et le chargement exclusif de cette archive sont activés. Les fonctions Node inutilisées pouvant servir à injecter du code sont désactivées.
- L’audit npm complet et l’audit de production ne signalent aucune vulnérabilité au moment de cette publication. Windows Defender ne détecte aucune menace dans cet EXE précis.

Validation : 156 tests de la suite complète, puis 31 tests ciblés revalidés après le dernier ajustement des archives, dont une nouvelle régression (157 cas distincts au total). Les 229 contrôles de l’interface compilée passent. Des essais négatifs confirment le refus d’une archive ASAR modifiée et l’ignorance des options d’injection Node. Les téléchargements de test utilisent des données artificielles ; aucun vrai jeu n’a été exécuté pour cette validation.

## Points restant à finaliser

Le tracker de production reste en HTTP et utilise une clé commune récupérable dans le catalogue du launcher. Le serveur est préparé pour TLS et des autorisations révocables, mais leur déploiement nécessite un hébergement HTTPS et un système d’identification des joueurs. Un torrent privé réduit la découverte publique ; il ne fournit ni exclusivité Ozuro ni anonymat des pairs.

L’EXE est toujours **non signé Authenticode**, faute de certificat d’éditeur. Les empreintes publiées permettent de comparer les fichiers ; elles ne remplacent pas une signature Windows et une distribution de confiance.

Ces contrôles portent sur le launcher. Ils ne constituent pas une certification antivirus ou une preuve d’authenticité de tous les jeux, DLL, mods ou mises à jour tierces qu’un joueur installe ou importe.
