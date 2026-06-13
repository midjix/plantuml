@startuml
!theme plain
skinparam activityShape roundBox

start
:L'Administrateur initie l'importation;
fork
  :Uploader le fichier DSK\n(Heures travaillées);
fork again
  :Uploader le fichier EpiLogg\n(Forfaits de prestations);
end fork

:Analyse et nettoyage des données brutes;

while (Pour chaque ligne de prestation EpiLogg) is (oui)
  :Lire le code du forfait (ex: 8.1, 9.1);
  :Chercher la règle de conversion active\nà la date exacte de la prestation;
  :Convertir le forfait en minutes réelles;
endwhile (non)

:Agréger les données par utilisateur (Matricule) et par mois;
:Enregistrer les résultats consolidés dans la base de données;
:Afficher un message de succès à l'Administrateur;

stop
@enduml