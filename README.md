# Sopra Steria ALMTech Radar

## Wat is de Sopra Steria ALMTech Radar?

De Sopra Steria ALMTech Radar is een lijst van technieken ondersteund door een ring assignment. Deze bestaat uit vier ringen met de volgende karakteristieken:

- **Gebruik** — Deze technieken (kunnen) worden ingezet in productie.
- **Probeer** — Deze technieken _zijn_ interessant en kunnen onderdeel zijn van een MVP in productie.
- **Onderzoek** — Deze technieken _lijken_ interessant en kunnen onderdeel zijn van een POC in een _niet_ productie omgeving.
- **Verminder** — Inzet op deze technieken dient verminderd te worden.

## Wat is het doel?

Deze Tech Radar is bedoeld om alignment te creëren voor onze practices.

## Wie bepaalt dit?

De inhoud van de Sopra Steria ALMTech Radar wordt regelmatig vastgesteld door onze engineers. Staat iets verkeerd of er niet op? Geen paniek! Dan gaan we het daar over hebben.

## Repository Inhoud

Deze repository bevat:

- **Interactieve Tech Radar**: Een webgebaseerde visualisatie van onze technologie-stack
- **Versie Geschiedenis**: Alle vorige versies van de Tech Radar zijn beschikbaar in de [Changelog](https://www.almtech-radar.nl/changelog.html)
- **Changelog**: Overzicht van wijzigingen tussen versies

- ## Versies

Vorige versies kan je hier vinden: [Changelog](https://www.almtech-radar.nl/changelog.html)

## Over Sopra Steria

Meer projecten en repositories van Sopra Steria zijn te vinden op:

## Gebruik

Open de [Sopra Steria ALMTech Radar](https://www.almtech-radar.nl/) in een webbrowser om de interactieve Tech Radar te bekijken. De radar toont alle technologieën georganiseerd in vier kwadranten:

1. **Infrastructuur & Platformen** (kwadrant 0) - Infrastructure en hosting platforms
2. **Talen & Frameworks** (kwadrant 1) - Programmeertalen en ontwikkel-frameworks  
3. **Architectuur & Cultuur** (kwadrant 2) - Methodologieën en architectuurpatronen
4. **Ondersteuning & Tools** (kwadrant 3) - Ontwikkel- en productietools

### Lokaal

Als je [index.html](index.html) lokaal opent, dan wordt de radar niet ingeladen. Om dit te doen, installeer je de [Live Preview](https://marketplace.visualstudio.com/items?itemName=ms-vscode.live-server) extension in VS Code. Vervolgens kun je in de Verkenner met de rechtermuisknop op [index.html](index.html) klikken en _Show Preview_ selecteren. Dit start een lokale server binnen VS Code waardoor je wijzigingen aan de inhoud van de radar direct kunt bekijken in VS Code of in een web browser.

## Preview Environments

Voor elke pull request wordt automatisch een preview environment aangemaakt via Azure Static Web Apps. Dit stelt reviewers in staat om wijzigingen visueel te controleren voordat ze worden samengevoegd met de main branch.

### Hoe werkt het?

1. **Pull Request Aanmaken**: Wanneer je een pull request aanmaakt, wordt automatisch een preview environment gedeployed
2. **Preview URL**: De Azure Static Web Apps GitHub Action plaatst automatisch een commentaar op de pull request met de preview URL
3. **Updates**: Bij elke nieuwe commit naar de pull request branch wordt de preview environment automatisch bijgewerkt
4. **Opruimen**: Wanneer de pull request wordt gesloten of samengevoegd, wordt de preview environment automatisch verwijderd

### Voordelen

- **Visuele Verificatie**: Reviewers kunnen wijzigingen aan de radar direct in hun browser bekijken
- **Isolatie**: Elke PR heeft zijn eigen omgeving, zonder impact op productie
- **Automatisch**: Geen handmatige stappen nodig voor deployment of cleanup

## Gebaseerd op

- [ThoughtWorks Tech Radar](https://www.thoughtworks.com/radar)
- [Zalando TechRadar](https://github.com/zalando/tech-radar)
- [Wigo4it TechRadar](https://github.com/wigo4it/techradar)
