# Contr-le-Symfony

ECURIE :
- id
- NOM = str
- Pilotes.nom FK str
- moteur.marque KF str

PILOTE : 
- id
- nom str
- prenom str
- points base 12 int
- date-debut-F1 datetime
- Suspendu true/false Bool

Moteur :
- id
- marque (ecurie.nom) FK str

Infractions :
- id 
- penalités (points) int
- amendes (euros) int
- nom.course str
- pilote.nom str FK
- date.infraction datetime

Frédérick Rat