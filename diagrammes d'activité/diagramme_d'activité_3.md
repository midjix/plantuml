@startuml
!theme plain

start
:L'Administrateur accède aux paramètres de conversion;
:Sélectionner un code forfait à modifier (ex: 8.1 AISP);
:Saisir la nouvelle valeur en minutes\net la nouvelle date d'application;

if (Une règle active existe-t-elle déjà pour ce code ?) then (oui)
  :Clôturer l'ancienne règle\n(Ajouter une 'Date de fin' = 'Date d'application' - 1 jour);
else (non)
endif

:Insérer la nouvelle règle dans la base de données\n(avec 'Date de début' et sans 'Date de fin');
:Afficher la confirmation de mise à jour;

stop
@enduml