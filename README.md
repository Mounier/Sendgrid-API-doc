# Sommaire
* [Installation](#installation)
* [Prérequis](#prérequis)
* [API key](#api key)
* [Structure générale](#structure générale)
* [Les endpoints](#les endpoints)
	* [Contacts](#les contacts)
	* [Tracking](#le tracking)
	* [Template](#template)
* [L'envoi de mails](#envoi de mails)

<a name="installation"></a>
# Installation

<a name="prérequis"></a>
## Prérequis

- Java version Oracle JDK 7, 8 ou OpenJDK 7
- Le service Sendgrid, commençant au [free level](https://sendgrid.com/free?source=sendgrid-java)

<a name="api key"></a>
## API key

Créer une clé API -> [SENDGRID_API_KEY](https://app.sendgrid.com/settings/api_keys).

<a name="structure générale"></a>
# Structure générale

Premièrement, il faut initialiser un objet *Sendgrid* (avec la clé API) et un objet Request :
```java
import com.sendgrid.*;
import java.io.IOException;

public class Example {
    public static void main(String[] args) throws IOException {
        try {
        
            SendGrid sg = new SendGrid(System.getenv("SENDGRID_API_KEY"));
            Request request = new Request();
            ...
            
        } catch (IOException ex) {
            throw ex;
        }
    } 
}
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
*TYPE_DE_REQUETE* peut avoir les valeurs GET/HEAD/POST/OPTIONS/PUT/DELETE.

Pour avoir une réponse du serveur on instancie un objet *Response*. Il y a trois méthodes nous perméttant d'afficher la ligne de statut, l'en-tête et le corps du message envoyés par le serveur de Sendgrid :
```java
Response response = sg.api(request);
System.out.println(response.statusCode);
System.out.println(response.body);
System.out.println(response.headers);
```

Au final on obtient un code de cette forme pour tous les appels :
```java
import com.sendgrid.*;
import java.io.IOException;

public class Example {
public static void main(String[] args) throws IOException {
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
  }
}
```
<a name="les endpoints"></a>
# Les endpoints

<a name="Contacts"></a>
## Les contacts

Tout ce qui touche aux contacts en général est associé au endpoint "contactdb". Ce endpoint est la base de données des contacts pour la marketing campaign.

### Créer un custom field associé aux contacts

*Custom fields* : Ce sont les différents attributs qui caractérisent un contact.
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

*Custom fields* : Ce sont les différents attributs qui caractérisent un contact.
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

*Custom fields* : Ce sont les différents attributs qui caractérisent un contact.
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
On obtient un JSON nous indiquant pour chaque liste leur id et leur nom ainsi que le nombre de contacts qu'elle contient :
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

```java
...
request.method = Method.POST;
request.endpoint = "contactdb/lists/{list_id}/recipients";
request.body = "[\"recipient_id1\",\"recipient_id2\"]";
...
```

### Récupérer tous les contacts d'une liste

```java
...
request.method = Method.GET;
request.endpoint = "contactdb/lists/{list_id}/recipients";
Map<String,String> queryParams = new HashMap<String, String>();
queryParams.put("list_id", "10659");
request.queryParams = queryParams;
...
```

### Supprimer un contact d'une liste

```java
...
request.method = Method.DELETE;
request.endpoint = "contactdb/lists/{list_id}/recipients/{recipient_id}";
Map<String,String> queryParams = new HashMap<String, String>();
queryParams.put("recipient_id", "9856");
queryParams.put("list_id", "10659");
request.queryParams = queryParams;
...
```

### Ajouter des contacts

```java
...
request.method = Method.POST;
request.endpoint = "contactdb/recipients";
request.body = "[{\"age\":25,\"last_name\":\"User\",\"email\":\"example@example.com\",\"first_name\":\"\"},{\"age\":25,\"last_name\":\"User\",\"email\":\"example2@example.com\",\"first_name\":\"Example\"}]";
...
```

### Récupérer tous les contacts

```java
...
request.method = Method.GET;
request.endpoint = "contactdb/recipients";
...
```

### Supprimer des contacts

```java
...
request.method = Method.DELETE;
request.endpoint = "contactdb/recipients";
request.body = "[\"recipient_id1\",\"recipient_id2\"]";
...
```

### Récupérer des contacts par critères de recherches

```java
...
request.method = Method.GET;
request.endpoint = "contactdb/recipients/search";
Map<String,String> queryParams = new HashMap<String, String>();
queryParams.put("{field_name}", "test_string");
request.queryParams = queryParams;
...
```

<a name="le tracking"></a>
## Tracking

Pour gérer le tracking des mails, Sendgrid organise son API autour de trois endpoints :

* Tracking_setting : Ce endpoint permet le paramétrage du tracking des mails.
* Stats : Ce endpoint permet de récupérer toutes les statistiques globales sur une période définie. Ceci concerne les éléments suivants :
    * blocks
    * bounce_drops
    * bounces
    * clicks
    * deferred
    * deliverred
    * invalid_emails
    * opens
    * processed
    * requests
    * spam_report_drops
    * spam_reports
    * unique_clicks
    * unique_opens
    * unsubscribe_drops
    * unsubscribes
* Suppression : Ce endpoint sert principalement à supprimer/identifier les emails invalides, les emails ayant signalés un spam ou encore les emails s'étant désabonnés de la newsletter.

### Récupérer une liste des paramètres de tracking   

Cette liste nous indique pour chaque paramètre de tracking si il est activé ou désactivé.
```java
...
request.method = Method.GET;
request.endpoint = "tracking_settings";
...
```

### Mettre à jours le paramètre click tracking

```java
...
request.method = Method.PATCH;
request.endpoint = "tracking_settings/click";
request.body = "{\"enabled\":true}";
...
```
*enabled* = true -> activer le click trakcing
*enabled* = false -> désactiver le click tracking

### Mettre à jours le paramètre open tracking

```java
...
request.method = Method.PATCH;
request.endpoint = "tracking_settings/open";
request.body = "{\"enabled\":true}";
...
```
*enabled* = true -> activer le open trakcing
*enabled* = false -> désactiver le copen tracking

### Récupérer toutes les statistiques

```java
...
request.method = Method.GET;
request.endpoint = "stats";
Map<String,String> queryParams = new HashMap<String, String>();
queryParams.put("aggregated_by", "day");
queryParams.put("start_date", "2016-07-01");
queryParams.put("end_date", "2016-08-05");
request.queryParams = queryParams;
...
```
Ici *queryParams* nous permet de définir la période sur laquelle on veut collecter les données.

Voici un exemple du corps de la réponse serveur :
```json
[
	{
		"date":"2016-07-01",
		"stats": [ 
			{
				"metrics": { 
						"blocks":0,
						"bounce_drops":0,
						"bounces":0,
						"clicks":0,
						"deferred":1,
						"delivered":8,
						"invalid_emails":0,
						"opens":3,
						"processed":8,
						"requests":8,
						"spam_report_drops":0,
						"spam_reports":0,
						"unique_clicks":0,
						"unique_opens":3,
						"unsubscribe_drops":0,
						"unsubscribes":0
				}
			}
		]
	},

	.
	.
	.

	{
		"date":"2016-08-05",
		"stats": [
			{ 
				"metrics": {
					"blocks":0,
					"bounce_drops":0,
					"bounces":0,
					"clicks":2,
					"deferred":0,
					"delivered":0,
					"invalid_emails":0,
					"opens":15,
					"processed":0,
					"requests":0,
					"spam_report_drops":0,
					"spam_reports":0,
					"unique_clicks":2,
					"unique_opens":2,
					"unsubscribe_drops":0,
					"unsubscribes":0
				}
			}
		]
	}
]
```

### Récupérer tous les blocks

```java
...
Calendar now = Calendar.getInstance();
long nowInSeconde = now.getTimeInMillis()/1000l;	// on divise par mille pour obtenir des secondes
long twoMonthBeforeInSeconde = (now.getTimeInMillis()/1000l) - 5176678;		// on soustrait la valeur du temps now par 5176678 (2 mois en secondes)

request.method = Method.GET;
request.endpoint = "suppression/blocks";
Map<String,String> queryParams = new HashMap<String, String>();
queryParams.put("start_time", String.valueOf(twoMonthBeforeInSeconde));
queryParams.put("end_time", String.valueOf(nowInSeconde));
request.queryParams = queryParams;
...
```

### Supprimer des blocks

```java
...
request.method = Method.DELETE;
request.endpoint = "suppression/blocks";
request.body = "{\"emails\":[\"example1@example.com\",\"example2@example.com\"]}";
...
```

### Récupérer tous les bounces

```java
...
Calendar now = Calendar.getInstance();
long nowInSeconde = now.getTimeInMillis()/1000l;	// on divise par mille pour obtenir des secondes
long twoMonthBeforeInSeconde = (now.getTimeInMillis()/1000l) - 5176678;		// on soustrait la valeur du temps now par 5176678 (2 mois en secondes)

request.method = Method.GET;
request.endpoint = "suppression/bounces";
Map<String,String> queryParams = new HashMap<String, String>();
queryParams.put("start_time", String.valueOf(twoMonthBeforeInSeconde));
queryParams.put("end_time", String.valueOf(nowInSeconde));
request.queryParams = queryParams;
...
```

### Supprimer des bounces

```java
...
request.method = Method.DELETE;
request.endpoint = "suppression/bounces";
request.body = "{\"emails\":[\"example@example.com\",\"example2@example.com\"]}";
Response response = sg.api(request);
...
```

### Récupérer tous les emails invalides

```java
...
Calendar now = Calendar.getInstance();
long nowInSeconde = now.getTimeInMillis()/1000l;	// on divise par mille pour obtenir des secondes
long twoMonthBeforeInSeconde = (now.getTimeInMillis()/1000l) - 5176678;		// on soustrait la valeur du temps now par 5176678 (2 mois en secondes)

request.method = Method.GET;
request.endpoint = "suppression/invalid_emails";
Map<String,String> queryParams = new HashMap<String, String>();
queryParams.put("start_time", String.valueOf(twoMonthBeforeInSeconde));
queryParams.put("end_time", String.valueOf(nowInSeconde));
request.queryParams = queryParams;
...
```

### Supprimer des invalid_emails

```java
...
request.method = Method.DELETE;
request.endpoint = "suppression/invalid_emails";
request.body = "{\"emails\":[\"example1@example.com\",\"example2@example.com\"]}";
...
```

### Récupérer tous les spam_reports

```java
...
Calendar now = Calendar.getInstance();
long nowInSeconde = now.getTimeInMillis()/1000l;	// on divise par mille pour obtenir des secondes
long twoMonthBeforeInSeconde = (now.getTimeInMillis()/1000l) - 5176678;		// on soustrait la valeur du temps now par 5176678 (2 mois en secondes)

request.method = Method.GET;
request.endpoint = "suppression/spam_reports";
Map<String,String> queryParams = new HashMap<String, String>();
queryParams.put("start_time", String.valueOf(twoMonthBeforeInSeconde));
queryParams.put("end_time", String.valueOf(nowInSeconde));
request.queryParams = queryParams;
...
```

### Supprimer des spam_reports

```java
...
request.method = Method.DELETE;
request.endpoint = "suppression/spam_reports";
request.body = "{\"emails\":[\"example1@example.com\",\"example2@example.com\"]}";
...
```

### Récupérer les unsubscribes (les emails qui se sont désinscrit de la newsletter)

```java
...
Calendar now = Calendar.getInstance();
long nowInSeconde = now.getTimeInMillis()/1000l;	// on divise par mille pour obtenir des secondes
long twoMonthBeforeInSeconde = (now.getTimeInMillis()/1000l) - 5176678;		// on soustrait la valeur du temps now par 5176678 (2 mois en secondes)

request.method = Method.GET;
request.endpoint = "suppression/unsubscribes";
Map<String,String> queryParams = new HashMap<String, String>();
queryParams.put("start_time", String.valueOf(twoMonthBeforeInSeconde));
queryParams.put("end_time", String.valueOf(nowInSeconde));
request.queryParams = queryParams;
...
```

<a name="template"></a>
## Template

### Créer un template

```java
...
request.method = Method.POST;
request.endpoint = "templates";
request.body = "{\"name\":\"example_name\"}";
...
```
Une fois notre *example_template* créé, il faut récupérer son id depuis le *request.body*. Voici dans notre exemple à quoi ressemble le *request.body* : 
```json
{
	"id":"3ad02638-c7f3-42c2-b1fc-86b0f4501729",
	"name":"example_name",
	"versions":[]
}
```
Une fois l'id récupéré, on peut créer une nouvelle version de notre template. C'est dans cette version que l'on met le code html du template. Voici un exemple  d'une méthode se chargant de la creation d'une version de template, j'y instancie un objet *templateBody* qui va nous permettre de sérialiser cet objet en données JSON que l'API s'attend à recevoir :
```java
public void createNewTransactionalTemplateVersion(String versionName, File htmlContent, File plainContent, int active, String templateId, String subject) throws IOException {
	try {
		SendGrid sg = new SendGrid(this.apiKey);
		Request request = new Request();
		
		request.method = Method.POST;
		request.endpoint = "templates/"+templateId+"/versions";
		
		GsonBuilder builder = new GsonBuilder();
		Gson gson = builder.disableHtmlEscaping().create();
		String htmlString = FileUtils.readFileToString(htmlContent, "US-ASCII");
		String contentString = FileUtils.readFileToString(plainContent, "UTF-8");
		
		TemplateBody templateBody = new TemplateBody(versionName, htmlString, contentString, active, templateId, subject);
		String json = gson.toJson(templateBody);
		
		Response response = null;
		try {
			request.body = json;
			response = sg.api(request);
		} catch (HttpResponseException e) {
			e.printStackTrace();
		}
	
		System.out.println(response.statusCode);
		System.out.println(response.body);
		System.out.println(response.headers);
		
	} catch (IOException ex) {
		throw ex;
	}
}

public class TemplateBody {
	
	private String name;
	private String html_content;
	private String plain_content;
	Integer active;
	private String templateId;
	private String subject;
	
	public TemplateBody(String name, String html_content, String plain_content, Integer active, String templateId,
			String subject) {
		super();
		this.name = name;
		this.html_content = html_content;
		this.plain_content = plain_content;
		this.active = active;
		this.templateId = templateId;
		this.subject = subject;
	}
	
	@Override
	public String toString() {
		return "TemplateBody [name=" + name + ", html_content=" + html_content + ", plain_content=" + plain_content
				+ ", active=" + active + ", templateId=" + templateId + ", subject=" + subject + "]";
	}
	
	//GETTERS AND SETTERS
}
```
NB : Il faudra ensuite s'assurer d'activer la version de notre template -> [activer une version de template](#activer_version_template)

### Récupérer tous les templates

```Java
...
    request.method = Method.GET;
    request.endpoint = "templates";
...
```

### Supprimer un template

```Java
...
request.method = Method.DELETE;
request.endpoint = "templates/{template_id}";
...
```

### Récupérer une version d'un template

```Java
...
request.method = Method.GET;
request.endpoint = "templates/{template_id}/versions/{version_id}";
...
```

### Supprimer une version d'un template

```Java
...
request.method = Method.DELETE;
request.endpoint = "templates/{template_id}/versions/{version_id}";
...
```

<a name="activer_version_template"></a>
### Activer une version de template

```Java
...
request.method = Method.POST;
request.endpoint = "templates/{template_id}/versions/{version_id}/activate";
...
```

<a name="envoi de mails"></a>
# L'envoi de mails

```Java
...
import com.sendgrid.*;
import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.util.HashMap;
import java.util.Map;
import org.apache.commons.codec.binary.Base64;
import org.apache.commons.io.FileUtils;
import org.springframework.util.Base64Utils;

public class MailService {
	public final static String PARAM_TEMPLATE_ID = "template";
	public final static String PARAM_FROM = "from";
	public final static String PARAM_FROM_NAME = "from-name";
	public final static String PARAM_TO = "to";
	public final static String PARAM_TO_NAME = "to-name";
	public final static String PARAM_BCC = "bcc";
	public final static String PARAM_SUBJECT = "subject";
	private final static String API_KEY = "YOUR_API_KEY";
	private SendGrid sendGrid;
	
	public MailService() {
		this.sendGrid = new SendGrid(API_KEY);
	}
	
	public void send(Map<String, String> params, Map<String, String> substitutions, File attachment)
			throws IOException {
		// prepare the REST call to SendGrid API
		Request request = new Request();
		request.method = Method.POST;
		request.endpoint = "mail/send";

		// build the JSON structure for the REST call
		Mail mail = this.buildEmail(params, substitutions, attachment);
		request.body = mail.build();

		// do it
		Response response = this.sendGrid.api(request);

		System.out.println(
				"sent an email, sengrid status: " + response.statusCode + " " + response.body + " " + response.headers);
	}
	
	private Mail buildEmail(Map<String, String> params, Map<String, String> substitutions, File attachment)
			throws IOException {
		Mail result = new Mail();

		// use the designated template
		String templateId = params.get(PARAM_TEMPLATE_ID);
		result.setTemplateId(templateId);

		// build the From:
		String from = params.get(PARAM_FROM);
		Email fromEmail = new Email(from);

		// optional, the displayed From: name
		String fromName = params.get(PARAM_FROM_NAME);
		if (fromName != null) {
			fromEmail.setName(fromName);
		}
		result.setFrom(fromEmail);

		// set up the recipient and its associated variable substitutions
		Personalization personalization = new Personalization();

		// build the To:
		String to = params.get(PARAM_TO);
		Email toEmail = new Email(to);

		// optional, the displayed To: name
		String toName = params.get(PARAM_TO_NAME);
		if (toName != null) {
			toEmail.setName(toName);
		}
		personalization.addTo(toEmail);

		// add a Bcc: if asked
		String bcc = params.get(PARAM_BCC);
		if (bcc != null) {
			personalization.addBcc(new Email(bcc));
		}

		// Subject:
		String subject = params.get(PARAM_SUBJECT);
		result.setSubject(subject);
		personalization.setSubject(subject);

		// set up the variable substitutions
		if ((substitutions != null) && !substitutions.isEmpty()) {
			for (Map.Entry<String, String> currSubEntry : substitutions.entrySet()) {
				personalization.addSubstitution(currSubEntry.getKey(), currSubEntry.getValue());
			}
		}

		Content contentHtml = new Content("text/html", "Test HTML"); // modifié
		result.addContent(contentHtml);

		// add the one and only personalization
		result.addPersonalization(personalization);
		
		return result;
	}
}
	
	
class Main {

	public static void main(String[] args) throws Exception {	
	
		MailService mailService = new MailService();
		Map<String, String> params = new HashMap<>();
		
		params.put(MailService.PARAM_TEMPLATE_ID, "7dac85e4-388e-400e-8e96-3b90422670f8");
		params.put(MailService.PARAM_FROM_NAME, "John");
		params.put(MailService.PARAM_FROM, "john@example.com");
		params.put(MailService.PARAM_BCC, "bob@example.com");
		params.put(MailService.PARAM_TO_NAME, "Alexane");
		params.put(MailService.PARAM_TO, "alexane@example.comr");
		
		Map<String, String> substitutions = new HashMap<>();
		File attachmentFile = new File("yourFile.jpg");
		
		mailService.send(params, substitutions, attachmentFile);
	}
}
