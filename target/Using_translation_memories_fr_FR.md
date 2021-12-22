== Utilisation de mémoires Le programme casse le ''[[[source text]]'' (le texte à traduire) en segments, recherche des correspondances entre les segments et la moitié source des paires source-cible précédemment traduites stockées dans une '', Le traducteur peut accepter un candidat, le remplacer par une nouvelle traduction, ou le modifier pour correspondre à la source. Dans les deux derniers cas, la nouvelle ou modifiée va dans la base de données.

Certains systèmes de mémoire de traduction recherche pour 100 % seulement, c'est-à-dire qu'ils ne peuvent récupérer que des segments de texte qui correspondent aux entrées de la base de données exactement, tandis que d'autres utilisent des algorithmes [[] Il est important de noter que les systèmes de mémoire de traduction typiques ne cherchent que du texte dans le segment source.

La flexibilité et la robustesse de l'algorithme d'appariement déterminent largement les performances de la mémoire de traduction, bien que pour certaines applications, le taux de rappel des correspondances précises peut être suffisamment élevé pour justifier l'approche de 100 %.

Les segments où aucun match n'est trouvé devront être traduits manuellement. Ces nouveaux segments sont stockés dans la base de données où ils peuvent être utilisés pour les traductions futures ainsi que des répétitions de ce segment dans le texte actuel.

Les mémoires de traduction fonctionnent mieux sur des textes très répétitifs, tels que des manuels techniques. Ils sont également utiles pour traduire des modifications incrémentielles dans un document précédemment traduit, par exemple, des modifications mineures dans une nouvelle version d'un manuel d'utilisateur. Traditionnellement, les mémoires de traduction n'ont pas été considérées comme appropriées pour les textes littéraires ou créatifs, pour la simple raison qu'il y a si peu de répétition dans le langage utilisé. Cependant, d'autres les trouvent de valeur même pour les textes non répétitifs, parce que les ressources de base de données créées ont de valeur pour les recherches de concordance pour déterminer l'utilisation appropriée des termes, pour l'assurance de la qualité (pas de segments vides) et la simplification du processus de révision (les segments source et cible sont toujours affichés ensemble tandis que les traducteurs doivent travailler avec deux documents dans un environnement de revue traditionnel).