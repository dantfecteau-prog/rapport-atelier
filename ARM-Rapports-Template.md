# 📊 Modèle Excel - Rapports Atelier ARM

## Instructions de création du fichier Excel

### Colonnes à créer :

| Numéro | Colonne | Type | Exemple |
|--------|---------|------|---------|
| A | **Numéro** | Numéro séquentiel | 1, 2, 3... |
| B | **Date/Heure** | Date + Heure | 2026-10-06 14:30:45 |
| C | **Poste de Travail** | Texte | Banc de scie |
| D | **Employé** | Texte | David Fecteau |
| E | **Email** | Email | dfecteau@raynaldmarcoux.com |
| F | **Impact** | Catégorie | ⛔ Arrêt production |
| G | **Description du Problème** | Texte long | Lame qui vibre anormalement |
| H | **Statut** | Texte | ✅ Reçu |
| I | **Photo** | Oui/Non | Oui |
| J | **Notes** | Texte | Action corrective appliquée |

---

## Format Excel simplifié

### Via Power Automate :

1. **Créer un fichier Excel** dans OneDrive :
   - Nom : `ARM-Rapports-2026.xlsx`
   - Localisation : `OneDrive\ATELIER RAYNALD MARCOUX\ARM-Rapports-Atelier\`

2. **Première ligne (en-têtes)** :
   ```
   Numéro | Date/Heure | Poste | Employé | Email | Impact | Description | Statut | Photo | Notes
   ```

3. **Format des données** :
   - **Numéro** : Numérotation auto (1, 2, 3...)
   - **Date/Heure** : Format ISO `2026-10-06 14:30:45`
   - **Poste** : Liste déroulante (Banc de scie, CNC, Edgeuse, etc.)
   - **Impact** : Liste déroulante (⛔ Arrêt, ⚠️ Ralent., 🔍 Qualité, ℹ️ Mineur)
   - **Statut** : Toujours `✅ Reçu` (car vient de Power Automate)

---

## Configuration Power Automate

### Étapes du Flow :

1. **Trigger** : "When an HTTP request is received"
   - URL sera générée automatiquement

2. **Action 1** : "Add a row into a table"
   - **Location** : OneDrive - ATELIER RAYNALD MARCOUX
   - **Document Library** : ARM-Rapports-Atelier
   - **File** : ARM-Rapports-2026.xlsx
   - **Table** : Tableau Excel ou créer une nouvelle

3. **Mappage des champs** :
   ```
   Numéro → AUTO (Excel peut auto-incrémenter)
   Date/Heure → triggerBody()['timestamp']
   Poste → triggerBody()['poste']
   Employé → triggerBody()['employe']
   Email → triggerBody()['email']
   Impact → triggerBody()['impact']
   Description → triggerBody()['description']
   Statut → "✅ Reçu"
   Photo → triggerBody()['photo']
   Notes → "" (vide)
   ```

4. **Action 2** : "Send an email notification"
   - À : `dfecteau@raynaldmarcoux.com`
   - Sujet : `Nouveau rapport atelier - @{triggerBody()['poste']}`
   - Corps : 
   ```
   Employé : @{triggerBody()['employe']}
   Poste : @{triggerBody()['poste']}
   Impact : @{triggerBody()['impact']}
   Description : @{triggerBody()['description']}
   
   Lien fichier : https://make.powerautomate.com
   ```

---

## Fichier Excel à créer manuellement

### Pas à pas :

1. **Ouvrir Excel**
2. **Créer un nouveau classeur**
3. **Renommer la feuille** : "Rapports"
4. **Première ligne** : Copier-coller ces en-têtes
   ```
   Numéro	Date/Heure	Poste	Employé	Email	Impact	Description	Statut	Photo	Notes
   ```
5. **Formater** :
   - Colonne A : Nombre
   - Colonne B : Date/Heure
   - Colonnes D, E : Texte
   - Colonne F : Liste déroulante

6. **Sauvegarder** :
   - Nom : `ARM-Rapports-2026.xlsx`
   - Format : Excel (.xlsx)
   - Chemin : `OneDrive\ATELIER RAYNALD MARCOUX\ARM-Rapports-Atelier\`

---

## JSON envoyé par le formulaire

Le formulaire envoie ces données à Power Automate :

```json
{
  "timestamp": "2026-10-06T14:30:45.123Z",
  "poste": "Banc de scie",
  "description": "Lame qui vibre anormalement",
  "impact": "Arrêt production",
  "employe": "David Fecteau",
  "email": "dfecteau@raynaldmarcoux.com",
  "photo": "Oui"
}
```

**Power Automate les enregistre directement dans les colonnes Excel** ✅

---

## Télécharger le modèle (simple)

**Format minimal pour commencer** :

| Numéro | Date/Heure | Poste | Employé | Email | Impact | Description | Statut |
|--------|-----------|-------|---------|-------|--------|-------------|--------|
| 1 | 2026-10-06 14:30:45 | Banc de scie | David Fecteau | dfecteau@raynaldmarcoux.com | Arrêt production | Lame vibre | ✅ Reçu |
| | | | | | | | |
| | | | | | | | |

**À faire** :
1. Copier ce tableau dans Excel
2. Ajouter les colonnes "Photo" et "Notes"
3. Sauvegarder comme `ARM-Rapports-2026.xlsx`
4. Mettre dans OneDrive : `ATELIER RAYNALD MARCOUX\ARM-Rapports-Atelier\`

---

## Résumé

✅ **Formulaire HTML** → Envoie les données au webhook Power Automate
✅ **Power Automate** → Reçoit et enregistre les données dans Excel
✅ **Excel OneDrive** → Centralise tous les rapports
✅ **Traçabilité complète** → Chaque rapport est numéroté et horodaté
