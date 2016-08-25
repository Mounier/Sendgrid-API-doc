# Sendgrid-API-doc

# Installation

## Prérequis

- Java version Oracle JDK 7, 8 ou OpenJDK 7
- Le service Sendgrid, commençant au [free level](https://sendgrid.com/free?source=sendgrid-java)

## Initialisation

Créer une clé API -> [SENDGRID_API_KEY](https://app.sendgrid.com/settings/api_keys).

# Structure générale

Les différents appels passés à l'API seront toujours écris sous cette forme :

```java
try {
    SendGrid sg = new SendGrid(System.getenv("SENDGRID_API_KEY"));
    Request request = new Request();
    request.method = Method.POST | Method.GET | Method.PATCH | Method.UPDATE | Method.DELETE;
    request.endpoint = "LE_ENDPOINT";
    request.body = "BODY_SOUS_FORME_JSON";
    Response response = sg.api(request);
    System.out.println(response.statusCode);
    System.out.println(response.body);
    System.out.println(response.headers);
  } catch (IOException ex) {
    throw ex;
  }
```
# Les endpoints

# Contact

Tout ce qui touche aux contacts en général est associé au endpoint "contactdb". Ce endpoint est la base de données des contacts pour la marketing campaign.

## Créer une liste

## Récupérer une liste

## Ajouter des contacts à une liste

## Supprimer des contacts d'une liste

## Récupérer tous les contacts

## Récupérer des contacts par critères de recherche

## Mettre à jours des contacts

## Supprimer des contacts





