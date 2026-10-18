# Sécurité de la bêta 0.4.36-beta.1

Les 11 torrents intégrés utilisent désormais un tracker HTTPS hébergé directement sur Cloudflare Workers. Le registre des pairs utilise un Durable Object SQLite partagé, avec expiration et limites de requêtes. Les indications d'adresse des clients sont ignorées au profit de l'IPv4 fournie à l'entrée par Cloudflare. Les fichiers des jeux ne transitent pas par Workers.

La migration conserve les infohashes, morceaux, indices des fichiers, caches, modes et politiques de sauvegarde. Le tracker local précédent, son démarrage automatique et sa redirection TCP ont été arrêtés sur le poste de partage. Le retrait de l'ancien DNS nécessite encore une connexion administrateur Freebox ; une ancienne adresse DNS et les historiques peuvent toujours révéler l'IP domestique.

Les protections de la 0.4.35 sont conservées : réglages MW2/MW3 et chemins personnels protégés, manifestes SHA-256 de publication obligatoires pour Drive, Electron 44.9.0, intégrité ASAR et fonctions d'injection Node désactivées.

Validation : **165 tests du launcher, 5 tests du Worker et 229 contrôles de l'interface compilée passent**. Le paquet compilé correspond aux sources vérifiées et ses fuses sont contrôlés. Un test réseau synthétique confirme HTTPS, l'absence de cache et l'annonce du moteur aria2 réel. Les 11 sources complètes qBittorrent ont été observées sur le nouveau tracker avec l'adresse NordVPN. Windows Defender ne détecte aucune menace dans cet EXE précis. Aucun vrai jeu n'a été téléchargé ou exécuté pendant ces essais ; aucun transfert entre deux joueurs sur des réseaux distincts n'est certifié.

## Limites

- La clé commune est extractible du launcher. Une identification et des autorisations délivrées par joueur restent à développer. Un participant autorisé peut recopier les fichiers reçus.
- Les connexions torrent exposent leur adresse de sortie aux participants. Pour masquer l'IP domestique, le VPN doit couvrir qBittorrent **et** aria2. Le déplacement du tracker n'efface pas une IP déjà connue et ne garantit pas l'absence de DDoS sur la connexion domestique.
- Workers Free est soumis à quotas ; les annonces peuvent être interrompues en cas de dépassement. Cette version du tracker gère IPv4 uniquement.
- L'EXE reste **non signé Authenticode**, faute de certificat d'éditeur. L'empreinte SHA-256 ne remplace pas une signature Windows ni une source de confiance.
- Ces contrôles concernent le launcher ; ils ne certifient pas tous les jeux, DLL, mods ou mises à jour tierces installés par un joueur.
