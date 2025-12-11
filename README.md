Agent IA – Automatisation des leads et prise de rendez-vous
Analyse automatique des dossiers PDF, création de lead, envoi d’un lien de réservation, suivi et confirmation du rendez-vous via Google Calendar

Introduction
Cet Agent IA automatise tout le processus d’accueil d’un prospect depuis la réception d’un dossier jusqu’à la réservation d’un rendez-vous.
Il repose sur un ensemble de workflows n8n orchestrant plusieurs APIs : Gmail, OpenAI, Airtable et Google Calendar.
Grâce à cet agent, l’entreprise n’a plus besoin d’effectuer manuellement :
. la lecture des pièces jointes
. l’analyse des informations administratives
. la création d’un lead dans le CRM
. l’envoi des propositions de créneaux
. la confirmation lorsque le prospect réserve un créneau
Tout est automatisé de bout en bout.

 Fonctionnalités principales
Analyse automatique des emails entrants
Lorsqu’un prospect envoie un email contenant un dossier PDF, l’agent :
•	télécharge la pièce jointe,
•	extrait le texte,
•	envoie le contenu au modèle OpenAI,
•	génère une fiche complète du prospect.
Création d’un lead dans Airtable
Le JSON structuré renvoyé par OpenAI est utilisé pour créer automatiquement un enregistrement dans la table Leads/Prospects.
Envoi automatique du lien de prise de rendez-vous
Après la création du lead, l’agent envoie :
•	un email personnalisé,
•	incluant un lien Google Calendar Booking permettant au prospect de choisir un créneau disponible.
Détection de la réservation du créneau
Un second workflow surveille Google Calendar :
•	dès qu’un événement « Appel téléphonique client » est créé,
•	l’agent identifie l’email du participant,
•	retrouve automatiquement le lead Airtable correspondant.
Mise à jour du lead + Email de confirmation
Une fois le lead identifié :
•	son statut passe à RDV programmé,
•	la date/heure du rendez-vous est enregistrée,
•	un email de confirmation est envoyé au prospect.
________________________________________
 Architecture générale
┌─────────────────────┐
│    Gmail Trigger     │  → Réception email + PDF
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Extract From File    │ → Extraction du texte PDF
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ OpenAI – Prompting   │ → Analyse + JSON structuré
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Airtable – Create    │ → Création du lead
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Gmail – Send Email   │ → Lien de prise de RDV
└──────────────────────┘


     (Workflow 2 – Confirmation RDV)

┌───────────────────────────┐
│ Google Calendar Trigger    │ → Détection réservation
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ Airtable – Search records  │ → Trouver le lead
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ Airtable – Update record   │ → Statut + Date RDV
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ Gmail – Confirmation Email │
└───────────────────────────┘
________________________________________
Technologies utilisées
Technologie	Usage
n8n	Orchestration des workflows
Gmail API	Lecture des mails + envoi d’emails
Google Calendar API	Détection des réservations
OpenAI API	Analyse intelligente du PDF
Airtable API	CRM + stockage des leads
JavaScript	Nettoyage et transformation des données
Booking Calendar Google	Interface de prise de RDV pour le prospect

Workflows inclus
Workflow 1 – Analyse et création du lead
 Déclencheur : Gmail Trigger
Résultat : Lead créé + email envoyé avec lien de réservation
Étapes :
1.	Trigger Gmail
2.	Extraction PDF
3.	Analyse via OpenAI
4.	Création Airtable
5.	Script de génération du message
6.	Envoi email avec lien Google Booking

Workflow 2 – Mise à jour du lead + confirmation
Déclencheur : Google Calendar – Event Created
Résultat : Lead mis à jour + confirmation envoyée
Étapes :
1.	Trigger Google Calendar
2.	Recherche du lead (email participant)
3.	Mise à jour Airtable
4.	Email de confirmation

Installation
1. Cloner le repo
git clone https://github.com/votre-org/agent-ia-rdv.git
cd agent-ia-rdv
2. Pré-requis
Vous devez posséder :
•	un compte n8n (self-host ou cloud)
•	une clé OpenAI (OPENAI_API_KEY)
•	un compte Gmail (API auto-configurée via n8n)
•	un calendrier Google configuré en « Rendez-vous Réservables »
•	une base Airtable avec la table Leads/Prospects
3. Importer les workflows
Dans n8n :
1.	Aller dans Workflows
2.	Import from File
3.	Importer les fichiers :
o	workflow_1_lead_creation.json
o	workflow_2_rdv_confirmation.json
4. Ajouter les Credentials dans n8n
•	Gmail OAuth
•	Google Calendar OAuth
•	Airtable Personal Access Token
•	OpenAI API key
5. Activer les workflows

Tests
Tester le Workflow 1
Envoyez un email test avec un PDF, vérifiez :
•	extraction réussie
•	analyse via OpenAI
•	lead ajouté dans Airtable
•	email envoyé au client
Tester le Workflow 2
Réservez un créneau sur le Google Booking, vérifiez :
•	déclenchement du workflow
•	mise à jour du lead
•	mail de confirmation

 Limites connues
•	L’analyse PDF dépend de la qualité du document (texte vs image).
•	Les emails doivent être cohérents entre PDF, booking et lead.
•	Google Calendar Trigger peut avoir un léger retard (30–60 sec).
•	OpenAI génère parfois des champs incomplets → prévoir validations.
•	Si plusieurs prospects utilisent le même email → ambiguïté possible.

Contribution
Les PR sont les bienvenues !
Merci de suivre les conventions GitHub standard :
1.	Créer une branche
2.	Faire vos modifications
3.	Ouvrir une Pull Request

 Licence
Ce projet est distribué sous licence MIT.
Vous êtes libre de l’utiliser, modifier, diffuser.

