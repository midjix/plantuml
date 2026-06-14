```plantuml
@startuml
!theme plain

start
:L'utilisateur accède à la page Tableau de Bord;
:Le système charge les données du mois en cours par défaut;

repeat
  :L'utilisateur sélectionne des filtres\n(Dates, Type de suivi, etc.);
  :Le système interroge la base de données agrégée;
  :Le système calcule le Ratio d'Efficacité :\n(Heures de prestations / Heures normales travaillées);
  :Mise à jour de l'affichage\n(Graphiques et tableaux détaillés);
  
  if (L'utilisateur souhaite exporter ?) then (oui)
    :Générer le rapport au format Excel;
    :Téléchargement du fichier;
  else (non)
  endif

repeat while (Modifier les filtres ?) is (oui)
endwhile (non)
stop
@enduml
```