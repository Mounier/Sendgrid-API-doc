# Installation

## Prérequis

- Java version Oracle JDK 7, 8 ou OpenJDK 7
- Le service Sendgrid, commençant au [free level](https://sendgrid.com/free?source=sendgrid-java)

## Identification

Créer une clé API -> [SENDGRID_API_KEY](https://app.sendgrid.com/settings/api_keys).

# Structure générale

Premièrement, il faut initialiser un objet *Sendgrid* (avec la clé API) et un objet Request :
```java
SendGrid sg = new SendGrid(System.getenv("SENDGRID_API_KEY"));
Request request = new Request();
```

Notre objet **request** nous permet de définir :
* Le *type de requête* que l'on veut faire passer. 
* Le *endpoint*.
* Le *body* qui sera en format JSON.
```java
request.method = Method.TYPE_DE_REQUETE
request.endpoint = "ENDPOINT";
request.body = "BODY_FORMAT_JSON";
```
*TYPE_DE_REQUETE* peut avoir les valeurs GET/HEAD/POST/OPTIONS/CONNECT/PUT/DELETE.

Pour avoir une réponse du serveur on instancie un objet *Response*. Il y a trois méthodes nous perméttant d'afficher la ligne de statut, l'en-tête et le corps du message envoyés par le serveur de Sendgrid :
```java
Response response = sg.api(request);
System.out.println(response.statusCode);
System.out.println(response.body);
System.out.println(response.headers);
```

Au final on obtient un code de cette forme pour tous les appels :
```java
try {
    SendGrid sg = new SendGrid(System.getenv("SENDGRID_API_KEY"));
    Request request = new Request();
    
    request.method = Method.TYPE_DE_REQUETE
    request.endpoint = "ENDPOINT";
    request.body = "BODY_FORMAT_JSON";
    
    Response response = sg.api(request);
    System.out.println(response.statusCode);
    System.out.println(response.body);
    System.out.println(response.headers);
  } catch (IOException ex) {
    throw ex;
  }
```
# Les endpoints

## Contact

Tout ce qui touche aux contacts en général est associé au endpoint "contactdb". Ce endpoint est la base de données des contacts pour la marketing campaign.

*Custom fields* : Ce sont les différents attributs qui caractérisent un contact.

### Créer un custom field associé aux contacts

```java
...
request.method = Method.POST;
request.endpoint = "contactdb/custom_fields";
request.body = "{\"type\":\"number\",\"name\":\"phone_number\"}";
...
```
Le paramètre **type** peut prendre trois valeurs différentes : *number*, *text* ou *date*. 

Si on affiche la réponse body :
```java
...
Response response = sg.api(request); 
System.out.println(response.body);
...
``` 
on obtient un JSON de cette forme :
```Json
{
  "id": 1,
  "name": "phone_number",
  "type": "number"
}
``` 

### Récupérer les custom fields associés aux contacts

```java
...
request.method = Method.GET;
request.endpoint = "contactdb/custom_fields";
...
```
Si on affiche la réponse body :
```java
...
Response response = sg.api(request); 
System.out.println(response.body);
...
``` 
on obtient un JSON nous indiquant les différents custom fields avec leurs id, leurs noms et leurs types :
```Json
{
  "custom_fields": [
    {
      "id": 1,
      "name": "birthday",
      "type": "date"
    },
    {
      "id": 2,
      "name": "middle_name",
      "type": "text"
    },
    {
      "id": 3,
      "name": "favorite_number",
      "type": "number"
    }
  ]
}
```
### Supprimer un custom field associé aux contacts

```java
...
request.method = Method.DELETE;
request.endpoint = "contactdb/custom_fields/{custom_field_id}";
...
```
Si on veut afficher le code d'erreur :
```java  
Response response = sg.api(request.statusCode); 
System.out.println(response.); 
``` 

### Créer une liste de contacts

```java
...
request.method = Method.POST;
request.endpoint = "contactdb/lists";
request.body = "{\"name\":\"your list name\"}";
...
```
Si on affiche la réponse body :
```java 
...
Response response = sg.api(request); 
System.out.println(response.body); 
...
```
On obtient un JSON nous indiquant l'id, le nom de notre liste ainsi que le nombre de contacts qu'elle contient :
```Json
{
    "id":530381,
    "name":"your list name",
    "recipient_count":0
}
``` 

### Récupérer toutes les listes de contacts

```java
...
request.method = Method.GET;
request.endpoint = "contactdb/lists";
...
```
Si on affiche la réponse body :
```java
...
Response response = sg.api(request); 
System.out.println(response.body);
...
``` 
On obtient un JSON nous indiquant l'id et le nom de chaque liste éxistante ainsi que le nombre de contacts qu'elles contiennent :
```Json
{
    "lists": [ 
        {
            "id":403955,
            "name":"list test",
            "recipient_count":2
        },
        
        {
            "id":530381,
            "name":"your list name",
            "recipient_count":0
        }
    ]
}
```

### Ajouter des contacts à une liste

### Supprimer des contacts d'une liste

### Récupérer tous les contacts

### Récupérer des contacts par critères de recherche

### Mettre à jours des contacts

### Supprimer des contacts





